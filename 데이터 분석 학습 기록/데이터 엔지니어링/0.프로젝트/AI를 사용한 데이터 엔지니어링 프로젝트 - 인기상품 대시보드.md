# [설계서 C] Project Base: 지그재그 인기 상품 시장 분석 대시보드 (Market Analyzer)

### 1. 프로젝트 개요 (Overview)

- **목표:** 지그재그의 카테고리별 인기 순위 데이터를 매일 수집하여, **순위 변동 흐름**과 **카테고리별 시장 가격 형성대**를 시각화한다.
    
- **핵심 가치:** 단순한 현재 순위가 아닌, **"어제보다 순위가 급상승한 상품"**과 **"꾸준히 상위권을 지키는 스테디셀러"**를 구별해낸다.
    
- **기술적 초점:** 데이터 웨어하우징의 기본인 **Fact Table(변하는 정보)**과 **Dimension Table(변하지 않는 정보)**의 분리 설계.
    

### 2. 데이터베이스 스키마 설계 (Star Schema 구조)

가장 중요한 포인트는 **'상품 정보'**와 **'매일 변하는 순위 정보'**를 분리하는 것입니다.

#### **A. Dimension Table: 상품 정보 (변경 적음)**

상품의 고유한 속성을 저장합니다.

SQL

```
CREATE TABLE products (
    product_id VARCHAR(50) PRIMARY KEY, -- 상품 고유 ID (URL에서 추출)
    product_name VARCHAR(255),
    category_main VARCHAR(50),          -- 예: 상의
    category_sub VARCHAR(50),           -- 예: 티셔츠
    image_url TEXT,
    detail_url TEXT,
    brand_name VARCHAR(100),            -- 쇼핑몰 이름
    created_at TIMESTAMP DEFAULT NOW()  -- 최초 수집일
);
```

#### **B. Fact Table: 일별 순위 및 상태 히스토리 (매일 쌓임)**

이 테이블을 통해 "지난주 10위에서 오늘 1위로 상승" 같은 분석이 가능합니다.

SQL

```
CREATE TABLE daily_rankings (
    record_id SERIAL PRIMARY KEY,
    product_id VARCHAR(50) REFERENCES products(product_id),
    snapshot_date DATE,                 -- 수집 날짜 (예: 2024-01-11)
    
    ranking INT,                        -- 그 날의 순위
    price INT,                          -- 그 날의 가격 (할인 등으로 변동 가능)
    review_count INT,                   -- 그 날의 누적 리뷰 수
    rating FLOAT,                       -- 그 날의 별점
    
    is_sold_out BOOLEAN,                -- 그 날의 품절 여부
    
    UNIQUE(product_id, snapshot_date)   -- 하루에 같은 상품 중복 방지
);
```

### 3. Airflow DAG 파이프라인 구성

#### **DAG 1: Daily Rank Collector (일일 순위 수집)**

- **Schedule:** 매일 오전 9시 ( `0 9 * * *` )
    
- **Target:** 카테고리별 랭킹 페이지 (1위 ~ 100위)
    
- **Logic:**
    
    1. **크롤링:** 랭킹 페이지를 돌며 상품 정보와 현재 순위를 수집.
        
    2. **데이터 분기 처리 (Upsert Logic):**
        
        - `products` 테이블: `product_id`가 없으면 `INSERT`, 있으면 무시(또는 이미지 URL 등만 업데이트).
            
        - `daily_rankings` 테이블: 오늘의 날짜(`snapshot_date`)로 `INSERT`.
            
    3. **상태 업데이트:** 상세 페이지 수집이 필요한 대상(`crawling_status = READY`)으로 마킹.
        

#### **DAG 2: Detail Enricher (상세 정보 보강)**

- **Schedule:** DAG 1 완료 후 Trigger (`TriggerDagRunOperator` 사용)
    
- **Target:** 오늘 `daily_rankings`에 들어온 상품 중 상세 정보가 비어있거나 갱신이 필요한 상품.
    
- **Logic:** (우리가 설계한 `?tab=view` 방식 활용)
    
    1. 상세 페이지 접속 -> 구매 버튼 -> 옵션/품절 확인.
        
    2. `?tab=view` 이동 -> 리뷰 수 확인.
        
    3. `daily_rankings` 테이블의 `is_sold_out`, `review_count` 컬럼 업데이트.
        

### 4. 대시보드 시각화 시나리오 (Superset / Streamlit)

이 DB 구조를 활용하면 다음과 같은 **고급 분석**이 가능합니다.

#### **View 1. 급상승 랭킹 (Rising Star)**

- **질문:** "어제 대비 순위가 가장 많이 오른 옷은?"
    
- **Query Logic:**
    
    SQL
    
    ```
    SELECT 
        p.product_name,
        today.ranking AS current_rank,
        (yesterday.ranking - today.ranking) AS rank_jump -- 순위 상승폭
    FROM daily_rankings today
    JOIN daily_rankings yesterday 
      ON today.product_id = yesterday.product_id
    JOIN products p ON today.product_id = p.product_id
    WHERE today.snapshot_date = CURRENT_DATE
      AND yesterday.snapshot_date = CURRENT_DATE - 1
    ORDER BY rank_jump DESC
    LIMIT 10;
    ```
    

#### **View 2. 가격대별 인기 분포 (Price Range Analysis)**

- **질문:** "상의 카테고리는 얼마짜리가 가장 잘 팔릴까?"
    
- **Chart:** 히스토그램 (X축: 가격 구간, Y축: 랭킹 100위 내 상품 개수)
    
- **Insight:** "아우터는 10~15만원 대가 가장 인기가 많구나" 파악 가능.
    

#### **View 3. 스테디셀러 추적 (Trend Line)**

- **질문:** "이 옷은 반짝 인기인가, 꾸준한 인기인가?"
    
- **Chart:** 특정 상품 선택 시, 지난 30일간의 `ranking` 변화를 꺾은선 그래프로 표현.


[[0.프로젝트 설계(데이터 엔지니어링)]]