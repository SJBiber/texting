# [설계서 A] Project 1: 실시간 트렌드 캐쳐 (Trend Hunter)

### 1. 프로젝트 개요

- **목표:** 상품의 '등록 시점'과 '품절 시점'을 추적하여, **가장 빠르게 품절되는 트렌드 상품**을 식별한다.
    
- **핵심 지표:** `Sell-out Velocity` (판매 속도 = 품절 시각 - 최초 발견 시각)
    
- **전략:** 전체 상품이 아닌 **'신상품(New Arrivals)'** 페이지를 집중 타게팅하여 수집 주기를 단축(30분~1시간)한다.
    

### 2. 데이터베이스 스키마 설계 (PostgreSQL 기준)

기존 상품 테이블에 **시간 추적(Time Tracking)** 컬럼을 추가합니다.

SQL

```
CREATE TABLE products (
    product_id VARCHAR(50) PRIMARY KEY,
    product_name VARCHAR(255),
    category VARCHAR(50),
    url TEXT,
    price INT,
    
    -- [핵심 추적 컬럼]
    is_new_arrival BOOLEAN DEFAULT FALSE, -- 신상 페이지에서 발견되었는가?
    crawling_status VARCHAR(20),          -- READY, DONE, SOLD_OUT
    stock_status VARCHAR(20),             -- IN_STOCK, SOLD_OUT
    
    first_seen_at TIMESTAMP,              -- 최초 발견 시간 (출시 추정)
    sold_out_at TIMESTAMP,                -- 품절 감지 시간
    last_checked_at TIMESTAMP             -- 마지막 확인 시간
);
```

### 3. Airflow DAG 파이프라인 구성

이 프로젝트는 **'발견(Discovery)'**과 **'추적(Tracking)'** 두 개의 DAG로 분리하여 운영합니다.

#### **DAG 1: The Scout (신상 발견)**

- **Schedule:** 매 30분 ( `*/30 * * * *` )
    
- **Target:** 쇼핑몰 `New Arrivals` 카테고리 1~5 페이지.
    
- **Logic:**
    
    1. 상품 목록 크롤링.
        
    2. DB에 `product_id`가 없으면 `INSERT`.
        
        - `first_seen_at` = `NOW()`
            
        - `is_new_arrival` = `TRUE`
            
        - `stock_status` = `IN_STOCK`
            
    3. 이미 존재하면 Pass.
        

#### **DAG 2: The Tracker (상태 추적)**

- **Schedule:** 매 1시간 ( `0 * * * *` )
    
- **Target:** `first_seen_at`이 **최근 7일 이내**이고, `stock_status`가 `IN_STOCK`인 상품만 조회 (Query Filter).
    
- **Logic:**
    
    1. 대상 상품 URL 접속 -> 구매 버튼 클릭 -> 품절 여부 확인.
        
    2. **만약 품절(`Sold-out`) 감지 시:**
        
        - `stock_status` = `SOLD_OUT` 업데이트.
            
        - `sold_out_at` = `NOW()` 업데이트.
            
    3. 여전히 판매 중이면 `last_checked_at`만 업데이트.

[[0.프로젝트 설계(데이터 엔지니어링)]]