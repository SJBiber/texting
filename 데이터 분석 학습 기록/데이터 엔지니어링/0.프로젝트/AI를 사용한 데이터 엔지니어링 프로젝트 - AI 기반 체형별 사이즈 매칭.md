# [설계서 B] Project 4: AI 기반 체형별 사이즈 매칭 (Size Matcher)

### 1. 프로젝트 개요

- **목표:** 리뷰 텍스트에 숨겨진 구매자의 신체 스펙(키, 몸무게)을 AI로 추출하여, **"나와 같은 체형이 구매한 상품"**을 추천한다.
    
- **핵심 기술:** Airflow(배치 처리), LLM(Gemini API - 정보 추출), Prompt Engineering.
    
- **전략:** 사용자가 검색할 때 AI를 돌리는 것이 아니라, **데이터 수집 시점에 미리 AI 처리를 끝내두는(Pre-processing)** 구조.
    

### 2. 데이터베이스 스키마 설계

리뷰 테이블에 **AI 추출 데이터**를 담을 컬럼을 확보합니다.

SQL

```
CREATE TABLE reviews (
    review_id SERIAL PRIMARY KEY,
    product_id VARCHAR(50),
    review_text TEXT,
    rating INT,
    
    -- [AI 추출 컬럼]
    extracted_height INT,       -- 키 (cm)
    extracted_weight INT,       -- 몸무게 (kg)
    extracted_size_tip VARCHAR(50), -- 사이즈감 (크다/작다/딱맞다)
    sentiment_score VARCHAR(20),    -- 긍정/부정
    
    ai_processed BOOLEAN DEFAULT FALSE -- AI 처리 여부 플래그
);
```

### 3. Airflow DAG 파이프라인 구성

#### **DAG 1: Review Collector (수집)**

- **Role:** 순수하게 데이터만 긁어옵니다. (우리가 설계한 `?tab=view` 방식 활용)
    
- **Logic:**
    
    1. 상품 상세 페이지 -> `?tab=view` 이동.
        
    2. 리뷰 텍스트 수집.
        
    3. DB `reviews` 테이블에 저장 (`ai_processed` = `FALSE`).
        

#### **DAG 2: AI Processor (데이터 가공)**

- **Schedule:** DAG 1 완료 후 Trigger 또는 매일 밤(Batch).
    
- **Logic:**
    
    1. **Target:** `WHERE ai_processed = FALSE` 인 리뷰 100개씩 조회 (API 비용/속도 고려하여 배치 처리).
        
    2. **Gemini API Call:**
        
        - _Input:_ 리뷰 텍스트 리스트.
            
        - _Prompt:_ "다음 리뷰들에서 키(cm), 몸무게(kg), 사이즈 팁을 JSON으로 추출해줘. 정보가 없으면 null로 해."
            
    3. **Update DB:**
        
        - 응답받은 JSON 파싱.
            
        - `extracted_height`, `extracted_weight` 컬럼 업데이트.
            
        - `ai_processed` = `TRUE` 변경.
            

### 4. 대시보드 활용 시나리오 (Streamlit 예시)

사용자는 대기 시간 없이 즉시 결과를 봅니다.

- **사용자 입력:** 키 160cm ~ 165cm, 몸무게 50kg ~ 55kg
    
- **Backend Query:**


[[0.프로젝트 설계(데이터 엔지니어링)]]