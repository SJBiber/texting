[[2.python]]
## **글 쓴 이유**

 - 데이터 등록에 NULL , np.nan , NA 등 아무런 값이 없는 경우를 겪게되는데 이러한 데이터 처리 방법에 대해 정리하고자 한다

 - 즉 결측값 종류 , 결측값에 대한 처리 방법 등을 설명하기 위해 작성한다. 

## **결측값 ??**

 **- 데이터에 값이 비어있는 상태를 의미**한다.

 - 결측을 특정한 값으로 표현한 것이 결측값임 (NULL , np.nan , NA ....)

 - 고객데이터 , 설문조사 , 정보 시스템 , 공정 내 검사등 이러한 결측값은 어떤 상황에서도 발생 할 수 있는 값이다.

### **결측값 종류 ?**

 - **완전 무작위 결측 , 무작위 결측 , 비무작위 결측**이 있으며 설명은 다음 표와 같다.

|   |   |
|---|---|
|**구분**|**상세 내용**|
|**완전 무작위 결측 (MCAR)**  <br>(Missing Completely at Random)|- 결측 발생이 완전히 무작위이며, 관측된 변수나 결측값 자체와 전혀 관련 없음  <br>- 예: 건강 설문에서 체중 무응답률이 성별, 연령 등 다른 변수와 상관없이 동일하게 나타날 때|
|**무작위 결측 (MAR)**  <br>(Missing at Random)|- 결측 발생이 관측된 다른 변수와 관련 있지만, 결측된 변수의 실제 값과는 무관  <br>- 예: 체중 무응답률이 남녀에 따라 다르지만, 실제 체중 값(날씬함/뚱뚱함 정도)이 결측 발생에 영향을 주지 않을 때|
|**비무작위 결측 (NMAR)**  <br>(Not Missing at Random)|- 결측 발생이 관측된 변수뿐만 아니라 결측된 변수의 실제 값에도 관련이 있을 때  <br>- 예: 체중 무응답률이 남녀에 따라 다를 뿐만 아니라, 실제 체중 값과 직접적 관련이 있을 때(뚱뚱할수록 무응답 확률이 높음)|

### **결측값 처리 방법??**

 - **원본 데이터로 채워넣기 , 단순 대체 , 결측값이 있는 행 삭제 , 다중대체**등 다양한 방법으로 처리 가능하다

```
# 시리즈에서 원소가 결측이면 True , 결측이 아니면 False
# 데이터 프레임에서 셸 값별로 측정됨
apt.isna()

# 출력
	거래금액	단지명	시도명	시군구	법정동	지번	입주년도	계약년도	계약월	계약일	...	층	평당금액	경과년수	재건축	계약일자	주소	세대수_x	주차대수_x	세대수_y	주차대수_y
0	False	False	False	False	False	False	False	False	False	False	...	False	False	False	False	False	False	False	False	False	False
1	False	False	False	False	False	False	False	False	False	False	...	False	False	False	False	False	False	False	False	False	False
2	False	False	False	False	False	False	False	False	False	False	...	False	False	False	False	False	False	False	False	False	False
3	False	False	False	False	False	False	False	False	False	False	...	False	False	False	False	False	False	False	False	False	False
4	False	False	False	False	False	False	False	False	False	False	...	False	False	False	False	False	False	False	False	False	False
...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...	...
220885	False	False	False	False	False	False	False	False	False	False	...	False	False	False	False	False	False	False	False	False	False
220886	False	False	False	False	False	False	False	False	False	False	...	False	False	False	False	False	False	False	False	False	False
220887	False	False	False	False	False	False	False	False	False	False	...	False	False	False	False	False	False	False	False	False	False
220888	False	False	False	False	False	False	False	False	False	False	...	False	False	False	False	False	False	False	False	False	False
220889	False	False	False	False	False	False	False	False	False	False	...	False	False	False	False	False	Fals


# 열별 결측값을 확인 가능한 함수임
apt.isna().sum()

# 출력
거래금액         0
단지명          0
시도명          0
시군구          0
법정동          0
지번           0
입주년도         0
계약년도         0
계약월          0
계약일          0
등기일자    152369
전용면적         0
층            0
평당금액         0
경과년수         0
재건축          0
계약일자         0
처리일수    152369
dtype: int64

# 열별 결측값의 비율을 구함
apt.isna().mean() * 100

# 출력
거래금액    0.00
단지명     0.00
시도명     0.00
시군구     0.00
법정동     0.00
지번      0.00
입주년도    0.00
계약년도    0.00
계약월     0.00
계약일     0.00
등기일자   65.70
전용면적    0.00
층       0.00
평당금액    0.00
경과년수    0.00
재건축     0.00
계약일자    0.00
처리일수   65.70
dtype: float64
```

#### **(부가 정보)통계에서의 표본 , 모수 , 확정적 , 확률적 ?**

 - 통계는 관심있는 집단 , 유한하거나 무한함 , 하지만 둘다 상당히커서 모두 수집하는건 불가능함 그래서 표본을 추출하는데

 해당 표본을 모집단을 대표해야한다 , 그렇지 않다면 편향이 발생한다!!

 - 표본 통계량(변수)로 부터 모수를 추출한다(모평균 , 모표준편차)

 - 모수는 간단히 말해 모르는 상수라고 한다.

 - 해당 동작을 정리하자면 모집단의 특정을 알고싶은데 , 모수 수집은 힘들어 그래서 표본을 추출하고 해당 추출된 데이터를 통계로 만들어 , 거기서 모수를 추출해서 데이터를 수집하는것이다.

 - 표본 통계량은 변수라서 오차가 발생하는데 그걸 표준 편차라고 한다.

 **- 표준오차 : 표본 통계량의 오차 범위다**

 **- 확정적 : 표본 평균을 사용한다 , 즉 우연이나 확률 , 오차가 개입될 여지가 없다고 가정하고 100% 확실하게 결정된 것**

 **- 확률적 : 표본 평균을 쓰는데 거기에 표준오차를 반영시킨다 , 즉 불확실하고 무작위적인 데이터**

#### **1. 원본 데이터로 채워넣기**

 - **분석 데이터셋에 결측값이 있으면 원본 데이터로 채우는 것이 가장 좋음**

 - 단 원본 데이터로 채워넣는 과정에서 **많은 시간과 노력이 소요될 뿐만 아니라 경우에 따라서는 불가**능할수있음.

#### **2. 단순 대체**

 - 단순 대체(simple Inputation)는 결측값을 대푯값(평균 , 중위수 , 최빈값 등)으로 대체하는 것

 - 결측값을 일부 기준에 따라 계층별로 나눈 대푯값으로 대체할 수 있음

 - 결측값을 **회귀식의 예측값으로 대체하는 확정적인 회귀대체법 , 회귀식에 확률 오차를 추가하는 확률적 회귀대체법이 있음**

 - **어떤 방법으로든 결측값을 단순대체하면 원본 데이터와 편향이 발생할수있음!**

```
#단순하게 원하는 value값으로 결측값을 대체해버리는 메서드
#filena : 데이터 프레임에 사용하면 안된다
# 모든 열에 대해 결측값들을 value로 변환하기 때문
# value를 넣는순간 데이터 타입도 변경이 될수 있음
# 아래 예시 datetime64[ns] 값이 object로 변함
apt['등기일자'].fillna(value = '모름')

# 그래서 변할것같은 민감한 값을 알잘딱해서 변경하자
apt['등기일자'].fillna(value = '1900-01-01')

# apt['등기일자'].fillna(value = '1900-01-01') 출력
0        1900-01-01
1        1900-01-01
2        1900-01-01
3        1900-01-01
4        1900-01-01
            ...    
231899   2024-12-13
231900   2024-12-18
231901   2024-11-20
231902   1900-01-01
231903   1900-01-01
Name: 등기일자, Length: 231904, dtype: datetime64[ns]


# 처리 일수의 평균을 구함
days_mean = apt['처리일수'].dt.days.mean()

# 등기일자의 결측값을 계약일자에 처리일수 평균을 더한 값으로 대체할수도 있음
days_mean_delta =  pd.Timedelta(value = 1 , unit = 'D')
apt['등기일자'].fillna(value = apt['계약일자'] +days_mean_delta)

# 출력
0        2020-02-01
1        2020-01-07
2        2020-01-15
3        2020-01-05
4        2020-01-13
            ...    
231899   2024-12-13
231900   2024-12-18
231901   2024-11-20
231902   2024-11-03
231903   2024-12-08
Name: 등기일자, Length: 231904, dtype: datetime64[ns]
```

#### **3. 결측값이 있는 행 삭제**

 - 특정 열에서 결측값이 일부(예를들어 5% 미만) 이 있다면 결측값이 있는 행을 삭제하는 방법을 선택할수는 있다. **(선택일뿐임)**

 - 해당 방법은 완전한 데이터셋을 만들 수 있지만 분석 데이터셋의 행 개수가 줄어듬

 - 결측값 비율이 높을때 전체 행을 삭제한다면 편향이 발생할 수 있음

 - 행을 삭제할때는 두가지 방법을 사용할 수 있다

     ->  **데이터 프레임에서 결측값이 있는 모든 행 삭제 , 특정 열에서 결측값이 있는 행 삭제**

```
# 기존 행 데이터 출력
apt.shape

# 출력
(231904, 18)

# 결측값이 있는 행을 모두 삭제
apt.dropna().shape

# 출력
(79535, 18)

# 결측값이 발생하는 열을 모두 삭제
apt.dropna(axis = 1).shape

# 출력
(231904, 16)
```

#### **4. 다중 대체**

 - **다중대체는 원본 데이터셋에 있는 결측값을 확률적 회귀대체로 채운 여러개의 데이터셋을 생성하고 , 각 데이터셋으로 동일한 분석을 반복 수행한 결과를 통합**한다.

 - 일반적으로 3~10개의 대체 데이터셋을 생성하는 것이 적절함

 - 다중대체의 목적이 단순하게 결측값을 채우는 것이라면 여러 대체값 중 하나를 선택하거나 평균을 사용할 수 있음