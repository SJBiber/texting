[[2.python]]
## **글 쓴 이유**

 - 데이터 분석을 하게 되면 해당 분석 내용을 상대방에게 전달하는것도 중요한 부분이다

 - 상대방에게 전달하기 위한 도구 , 요소들 중 시각데이터가 가장 설명하기 효율적이다

 - 데이터 시각화란 무엇인지 정리하겠다.

## **탐색적 데이터 분석**

 - 데이터의 특징을 파악하는 방법

 - 모델 학습에 앞서 분석 대상 데이터셋의 구종와 특성을 이해하기 위해 수행

 - 해당 분석과정에서는 **기술 통계량**을 계산하지만 계산된 데이터들은 일정하지않은 변수 데이터로 그래프를 통해 데이터 분포와 관계를 확인한다.

 - 이상치 , 결측값의 존재 , 패턴 , 발생 원인등을 확인하는것도 해당 탐색적 데이터 분석의 목적 중 하나이다.

## **평균 , 편차 , 표준편차 , 분산 ?**

 - 데이터 시각화에 앞서 통계 그래프에서 사용될 단어들을 다시 정리해보자

 - 분산과 표준편차는 값이 달라진다

 - 개수 , 합계 , 평균 , 중위수는 바뀌지 않는다.

#### **1. 평균**

 - 모든 데이터값을 더해 데이터의 개수로 나눈 값

 - 데이터의 중심 무게중심 역할을 함

 - 극단값에 매우 민감하게 반응항여 데이터 전체를 왜곡할수 있음!

#### **2. 극단값**

 - 데이터의 전체적인 흐름에서 현저하게 크게 벗어난 아주 크거나 작은값

 - 즉 **중심값에서 많이 떨어진 값**

 - 평균을 자기쪽으로 끌어당겨 데이터의 대표성을 떨굼

 - **최댓값이나 최솟값이 다른 수치에 비해 매우 크거나 , 작을때 극단값 존재 여부를 확인**해야함

#### **3. 중위수**

 - 데이터를 크기순으로 나열하였을 때 정중앙에 위치한 값

 - 극단값의 영향을 거의 받지않음

 - 평균과 중위수의 차이가 크다면 , 그 데이터 집단에는 강력한 극단값이 존재한다.

#### **4. 편차**

 **- 개별 데이터 값과 평균의 차이 (데이터 - 평균)**

 - 각 데이터가 평균으로부터 얼마나 떨어져 있는지를 나타냄

 - **모든 편차를 더하면 항상 0**

#### **5. 분산**

 - 편차를 전부 더하면 0이 되기 떄문에 , **편차를 제곱하여 모두 더한 후 평균을 낸값**

 - 데이터가 평균을 중심으로 얼마나 퍼져있는지를 수치화 한것

 - 제곱을 했기 때문에 원래 데이터와 단위가 달라져 직관적으로 이해하기 어려움

#### **6. 표준편차**

 - **분산에 루트를 씌워 다시 원래 데이터와 단위를 맞춘것**

 - **평균적으로 얼마다 떨어져 있는지 나타내는 지표**

#### **7. 표준편차 그래프 예시**

 - 평균키가 175고 표준편차가 5인 정규분포 그래프를 그리면 다음과 같다.

![](https://blog.kakaocdn.net/dna/bdr7U0/dJMcahbUTtm/AAAAAAAAAAAAAAAAAAAAAHWQHxSD8MwzX_jVvjKDxN9rh3t2NPUNAIi007FNUSbG/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=fCXv4mOY1e%2F1%2F4NVUK9Yo9toMsA%3D)

#### **8. 변동계수 ?**

 - 표준편차를 객관적으로 비교를 진행할때 사용

 - 평균 -> 상대적인 변동성의 크기를 비교할수 있다.

 - 개별로 비교하는것보다 평균을 가지고 비교해야 얼마나 편차가 발생하는지 알수있다

 - 변동계수가 낮을수록 평균과 가깝다 , 높을수록 평균과 멀어진다.

## **데이터 시각화 개요**

 - 데이터 시각화는 단순히 데이터를 차트로 표현하는 기술이 아니라 , 데이터를 이해하고 정보를 다른사람에게 명확하게 전달하는 커뮤니케이션 도구

 - 사람은 정보를 인지할 때 , 오감 중에서 시각이 가장 큰 비중을 차지함.

### **데이터 리터러시와 시각화의 관계 ?**

 - 데이터 리터러시는 데이터를 읽고 , 이해하고 , 해석하는 능력임

 - 수치형 데이터 속에 담긴 객관적인 사실을 올바르게 이해해야함

 - 비교를 통해 의미를 해석해야 좋다 , 나쁘다를 판단할수 있음

 - 평균 ,분산 , 범위 등의 기술 통계량은 비교를 위한 도구이다.

### **좋은 데이터 시각화의 조건 ?**

 - 불필요한 장식을 최소화하고 데이터의 본질에 집중하는것

 - Data-Ink Ratio라는 개념을 통해 이러한 원리를 체계화함 , Data-Ink Ratio는 1에 가까울수록 좋다

![](https://blog.kakaocdn.net/dna/cGyGEZ/dJMcagRB35F/AAAAAAAAAAAAAAAAAAAAAGyjmgezo2p-cf0iFNLliPK3hDRJCr2YFOSY-zi3JTrd/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=JUAc7TowZRmcGgQU3wCZnFgfdI4%3D)

 - 아래 그림과 같이 **장식이 많은 차트보다는 단순하지만 중요한 데이터는 확실하게 전달하는 , 즉 본질에 집중하는 차트를 보여주는 것이 시각화의 핵심**이다

![](https://blog.kakaocdn.net/dna/dR1kfg/dJMcag49NDS/AAAAAAAAAAAAAAAAAAAAAOprw0Dmd_xJJTTFdKdK1TBp_P0QuP3NaqPhcd9Rk_vb/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=j7kmwtbAhSyprYKb1iZvan82P5k%3D)

### **데이터 시각화 종류**

####  **1. 히스토그램**

 - 일변량 연속형 변수의 도수분포표를 시각화한 그래프

 - 세로축의 기본값은 개수(도수)이며 밀도(상대도수)로 변경할 수 있음

 - 히스토그램의 막대는 서로 붙어있어며 세로축을 밀도로 변경하면 막대의 총 면적은 확률 1을 의미

![](https://blog.kakaocdn.net/dna/BrbR4/dJMcafyo9O6/AAAAAAAAAAAAAAAAAAAAAEcoTArjb6sU_jqHFLOScSuTviI7pDP3UVu4c2i2VfRt/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=n%2BVY%2FESjmu9KFL%2FXKj0NHk9LpnY%3D)

####  **2. 상자 그림**

 - 일변량 연속형 변수의 분포에 사분위수와 이상치를 추가한 그래프

 - 가로축에 범주형 변수를 지정하면 집단 간 연속형 변수의 분포를 비교할 수 있음

![](https://blog.kakaocdn.net/dna/cFhR9U/dJMcabQiYnc/AAAAAAAAAAAAAAAAAAAAAL8H2gryf8mA65ItOT2YueaosU-bVNJ52Qp0TEErEDQn/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=Ut%2Bxejo9%2Fa5C%2F%2BeDhx2%2FTKDM2MU%3D)

####  **3. 막대 그래프**

 - 일변량 막대 그래프는 범주형 변수의 도수를 막대로 그린 그래프

 - 이변량 막대 그래프는 범주형 변수에 따라 연속형 변수의 크기를 비교할 수 있음

![](https://blog.kakaocdn.net/dna/2E425/dJMcaaDRwbo/AAAAAAAAAAAAAAAAAAAAAKIpjwOH8SWv-E8HzMjoYd3qPciMAO70rsdTrMqmpFkU/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=XU47pkva9ucvtwSeQ6u6hi90Knc%3D)

####  **4. 선 그래프**

 - 시간에 따라 연속형 변수의 변화를 표현한 그래프 

 - 주가 데이터와 같이 시계열 변수를 시각화 할 때 사용

![](https://blog.kakaocdn.net/dna/MakqP/dJMcacBGGzC/AAAAAAAAAAAAAAAAAAAAAM60-FPJZnMN8O5lVk3rppQnnxtlBJKgLKyriMOoJrZ3/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=BfjlWt4ES7gCETY5JP15rwx%2B4VM%3D)

####  **5. 산점도**

 - 산점도는 이변량 변수의 선형관계를 점으로 표현한 그래프

 - 선형 회귀분석의 입력변수와 목표변수에 직선의 관계가 존재하는지 확인

![](https://blog.kakaocdn.net/dna/cnTXt6/dJMcahJLQls/AAAAAAAAAAAAAAAAAAAAAEe6RULVOQGRMYJjz1tj6R2_sKKpM6ny396NW6H6OjsP/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=hQLlTftktqs%2F0M2F32rcJuQYERw%3D)

####  **6. 히트맵**

 - 히트맵은 여러 변수 간 관계의 크기를 색의 분포로 표현한 그래프 

 - 선형 회귀분석의 입력변수 간 상관관계가 크면 다중공선성 문자가 발생할 수 있으므로 히트맵을 통해 확인할 수 있음

![](https://blog.kakaocdn.net/dna/bTyQOm/dJMcacBGGzM/AAAAAAAAAAAAAAAAAAAAAGFxulUHwev0rIDKXw6qAAMnJvBBojW0v1OnU090LMMY/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=C4YFaoh7sndANTCOPiF%2F0kbvx6o%3D)