[[2.python]]
## **글 쓴 이유**

 - 금일은 **파이차트 , 묶음 막대 그래프 , 선그래프 , 산점도 , 히트맵 , 대화형 시각화에 대해 설명 및 예제코드**를 작성하겠음

 - 추가적으로 히트맵에서 사용되는 **피어슨과 스피어만의 용어**도 작성함

### **파이 차트**

 - **전체에서 각 항목이 차지하는 비율을 나타낼 때 사용**

 - 원을 부채꼴로 나누어 크기를 표시하며 , 모든 조각의 합은 항상 100%가 되어야함

 - **항목이 너무 많으면 가독성이 떨어지므로 최대 5개 까지만 표시**할수있도록 하자

 - 다음은 코드예시와 출력 결과이다.

```
# 먼저 차트화를 하고싶은 데이터를 생성
grp

# 출력
시군구
강남구    12291
강동구    11958
강북구     4875
Name: count, dtype: int64

# plt의 pie를 사용해서 파이차트화를 시킴
# x = 만들고 싶은 리스트
# labels = 각 범위에 대한 이름 list
# cooles = 범위 색
# autopct = 범위 안 퍼센트 출력 포멧
plt.pie(x=grp,
       labels = grp.index,
       colors = my_pal,
       autopct = '%.1f%%');
```

**출력결과 )** 

![](https://blog.kakaocdn.net/dna/EgODX/dJMcabiuSnn/AAAAAAAAAAAAAAAAAAAAAH36svcySXFyJ9tGNn5zFv8z9Hqkau7yVufRSAVTknpA/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=F4IoaZDAKabtZYkc3SimTI4vNT4%3D)

```
# startangle = 90으로 하면 시작 위치를 90도로 지정
# 기본값은 0 도 임
# counterclock = 범주 데이터를 시계방향순으로 표시할지 반시계로 표시할지 설정 , 기본값은 반시계방향
# textprops = 텍스트의 표시 설정
# wedgeprops = 범위 테두리 색 , 선두께 설정
# 쐐기 출력 데이터가 리스트 안에 3개의 데이터가 출력됨 patches , labels , pcts
# 각 리스트의 값을 받고싶은게 있고 없는게 있다면 다음과 같이 세팅
# _ : 받고싶지않은 더미데이터를 넣음 , pcts = 담고싶은 리스트가 있다면 변수 설정
_,_,pcts = plt.pie(x=grp,
       labels = grp.index,
       colors = my_pal,
       autopct = '%.1f%%',
       explode = [0,0,0.1],
       startangle = 90,
       counterclock = False,
       textprops = dict(fontweight = 'bold'),
       wedgeprops = dict(ec = '1' , lw = 1))

# 위에서 받아온 pcts(범주 리스트) 중 1번째 범주 글씨의 색을 바꿈
pcts[1].set_color('white')
```

**출력결과 )** 

![](https://blog.kakaocdn.net/dna/cTPThl/dJMcagqyQFL/AAAAAAAAAAAAAAAAAAAAAOUAlX8ZSWw3fhorTwAawxvYKYDAzt0L24qP7q-Sw-Ox/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=2AKdLCYi7bg3RHjpEargdFQ%2BdJA%3D)

### **묶음 막대 그래프**

 - **두가지 이상의 범주를 가진 데이터를 나란히 배치하여 비교**할 때 사용

 - **하나의 대분류(x축) 안에 여러 개의 소분류(묶음 막대)가 존재**

 - 묶음 내에서 막대들의 높이를 직접 비교할 수 있어 , 그룹간 차이를 파악하기 좋음

 - 멀티 인덱싱 : 하나의 인덱스 안에 다른 인덱스가 들어있는 중첩된 구조

     -> 사용이유 : 2차원 평면 표에 효율적으로 담기위해 사용

     -> '연도'안에 '월' , '시도' 안에 '시군구' 와 같은 포함 관계를 명확히 보여줌

     -> 대량의 데이터를 특정 그룹별로 묶어서 통계를 낼 때 자동으로 생성되는 경우가 많음

```
# 멀티 인덱싱
# level = 0 시군구 , level = 1 재건축 , value = 거래금액
grp = sub.groupby(by = ['시군구' , '재건축'])['거래금액'].mean()
sns.barplot(data = sub , x = '시군구' , y = '거래금액' , hue = '재건축');
```

**출력결과 )** 

![](https://blog.kakaocdn.net/dna/mRAaO/dJMcahbVX3V/AAAAAAAAAAAAAAAAAAAAADDpSSHx-c-HQnyP-xCoAHv6j0bVUeqYdtSGhkaRb3Uv/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=3xTUSUQrlJsPMNINuko0wLW%2FLIE%3D)

### **선 그래프**

 - **시간의 흐름에 따른 데이터의 변화 추이를 보여줄 때 가장 적합**

 - 점들을 선으로 이어 연속성을 강조

 - **상승 , 하락 , 정체 등의 흐름을 한눈에 파악**하기 쉬움

#### **1. 계약월별 평균 거래금액 선그래프 그리기**

```
# seaborn 라이브러리의 lineplot 사용
# x,y 데이터 범위를 자동으로 세팅해줌
# 만약 월단위 , 일자 단위로 만들고싶다면 2가지 방법이 있음
# 월단위 정수를 문자열로 바꾸기 (원본 수정 O)

# xticks 로 눈금만들기 (원본 수정 X)
# 선 데이터에 마커 넣어보기
# marker 인수에 세팅값을 넣으면 된다.
sns.lineplot(data = apt, x= '계약월' ,  y= '거래금액'
             ,estimator = 'mean' , errorbar = None,
            marker = 'o')
            
# x 축의 범위를 ticks 로 1~12 세팅 함 labels은 x축 데이터의 명칭을 바꿈 
plt.xticks(ticks = range(1,13), labels = [f'{i}월' for i in range(1,13)]);
```

**출력결과 )** 

![](https://blog.kakaocdn.net/dna/bWd8VS/dJMcagRC946/AAAAAAAAAAAAAAAAAAAAADiljT1W7AT31zo7QUN6uyG4sr0W6HwoOaRJI3V_ZjG5/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=PyAfmr392zlQn22z2ENVTeZdj1Y%3D)

#### **2. 년도 별 계약월 평균 거래금액 다중 선 그래프 그리기**

 - 년도 별 **선 스타일도 비슷하고 색, 마커도 비슷하여 보기 힘듦**

```
# hue에 '계약년도'를 추가하여 계약년도에 따른 계약월의 거래금액 선 그래프를 출력
sns.lineplot(data = apt, x= '계약월' ,  y= '거래금액' , hue = '계약년도'
             ,estimator = 'mean' , errorbar = None,
            marker = 'o')
plt.xticks(ticks = range(1,13), labels = [f'{i}월' for i in range(1,13)]);
```

**출력결과 )** 

![](https://blog.kakaocdn.net/dna/bd9Tb7/dJMcabQj49l/AAAAAAAAAAAAAAAAAAAAAG1_WoCYmHyhe4hA56Aq678G4mQEdc1G-p75GxttQdLs/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=Qw4CRYVIvwAmZrCureWYgwG6B%2Bo%3D)

#### **3. 년도 별 계약월 평균 거래금액 다중 선 그래프 그리기**

 - 보기쉽게 **년도별 선 스타일을 변경하는 로직 추가**

- **계약연도 범주 위치를 plt.legend를 사용하여 bbox_to_anchor를 통해 좌표로 원하는 위치로 옮김**

```
#style = 계약년도 별로 스타일을 바꾸고 싶다
#markers = 마커 스타일을 바꾸고싶어 True
#dashed = 선의 스타일도 바꾸고싶어 True
# 범주별 선 스타일을 바꾸고싶어 그럼 튜플형태로 내마음대로 만들 수 있다
sns.lineplot(data = apt, x= '계약월' ,  y= '거래금액' , hue = '계약년도'
             ,estimator = 'mean' , errorbar = None,
            style = '계약년도' , markers = True , dashes = True)
plt.xticks(ticks = range(1,13), labels = [f'{i}월' for i in range(1,13)])

# 범례 위치 바꾸기
# bbox_to_anchor 기준을 잡고 , 범례 위치를 str으로 받아서 지정해줌
# ncols = 범주를 가로열 개수만큼 가로로 눕힐때 사용
plt.legend(loc = 'upper center' , bbox_to_anchor = (0.5,1.2) , ncols = 5);
```

**출력결과 )** 

![](https://blog.kakaocdn.net/dna/n6nD1/dJMcai9HQPr/AAAAAAAAAAAAAAAAAAAAAI09UH39sZzn_gJwdJfQIIb3MA-Qwa3BxJQNUKoyV-O2/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=qkpsLAxbc3%2BpDIAraQKnXb%2F9uSQ%3D)

### **산점도 그래프**

 - **두 변수 사이의 상관관계를 점으로 찍어 표현할 때 사용**

 - x축과 y축에 각각 다른 수치형 변수를 배치해서 데이터의 분포와 군집을 확인

 - **점들이 일직선 형태를 띄면 강한 상관관계가 있다고 판단 , 상관계수를 시각화한 형태**

#### **1. 산점도 그래프 그리기**

```
# seaborn 라이브러리를 활용
# x : 원인이되는 연속형 변수명을 지정
# y : 결과가 되는 연속형 변수명을 지정
# fc : facecolor 점의 채우기 색을 지정
# ec : edgecolor 점의 테두리 색을 지정
# s : 점의 크기를 지정 (기본값 50)
# alpha 채우기 색의 두명도를 0~1의 실수로 지정
sns.scatterplot(data = apt , x = '전용면적' , y = '거래금액'
				,fc = '0.9'
                ,ec = '0.8'
                ,s = 10
                ,alpha = 0.5)
```

**출력결과 )** 

![](https://blog.kakaocdn.net/dna/VfGI9/dJMcaiIDPzC/AAAAAAAAAAAAAAAAAAAAAPdsKJwQAIzENxJGSzlb1JqCR5SdoCKg2AJH2ZPmM5hB/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=U7%2B9Rm5UxXXkrnHJ8hcBVzwJtlw%3D)

#### **2. 산점도 그래프에 그라데이션을 넣어서 그리기**

 - **데이터가 크면 클수록 데이터의 점의 색상도를 높임  
**

 - 데이터를 **정렬하지않으면 무작위로 크거나 작은 점 데이터를 찍기때문에 작은 데이터의 점이 큰데이터의 점을 덮어씌울수있다**

 그래서 **데이터 정렬을 먼저하고 나서 그래프를 그려야한다.**

```
# 조회했을때 세대수가 많은 데이터가 파묻혀있는걸 볼수있다
# 이때 세대수 데이터를 오름차순으로 하지않아 큰데이터를 작은데이터가 덮어씌우기때문이다
# 세대수를 오름차순 정렬한 후 출력해보자
apt = apt.sort_values(by = '세대수')
sns.scatterplot(data = apt , x = '전용면적' , y = '거래금액'
               ,hue = '세대수'
               ,ec = '0.8'
                ,palette = 'Grays'
               ,s = 10
               ,alpha = 0.5);
```

**출력결과 )** 

![](https://blog.kakaocdn.net/dna/R1Osi/dJMcagxkidF/AAAAAAAAAAAAAAAAAAAAABWAy4A_eIiPmlNrF8v-Bw5ZH45sU--2tN-3u3H1NTkL/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=KSBz%2BKIumQ6HxM2dh60iIDxgjcw%3D)

#### **3. 산점도 그래프에 선을 넣어서 그리기**

 - **산점도 그래프에 회귀직선을 추가**

 - **회귀 직선 : 여러 데이터 포인트들 사이를 관통하는 가장 최적의 직선**

```
# 산점도에 회귀직선 추가
# 회귀 직선 = 여러 데이터 포인트들 사이를 관통하는 **'가장 최적의 직선
point_prop = dict(fc = '0.9',ec = '0.8',s = 10,alpha = 0.5)
line_prop = dict(color = 'red' , lw = 1.5)

# [참고] kw = 키워드 알규먼트(keyword argument) , prop = 프로퍼티(property)
sns.regplot(data = gng , x = '전용면적' , y = '거래금액',
				scatter_kws = point_prop , line_kws = line_prop)
```

**출력결과 )** 

![](https://blog.kakaocdn.net/dna/nd6SP/dJMcahweLlB/AAAAAAAAAAAAAAAAAAAAAOvplYchmbRad58CrMI7RYR5Vp9bUPhnzUKG5dYrPLuP/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=XWiDGIWxBg6FE%2BpVFjvoHm83vKk%3D)

#### **4. 산점도 행렬을 그리기**

 - x축에 대해 많은 값들을 보고싶다면 각 변수들을 세팅

 - kind : 해당 매개변수에 reg를 등록하면 회귀직선을 표시함

 - plot_kws : 해당 키워드에 점 스타일과 선 스타일을 딕셔너리 형태로 넣어줘야함

 - 제목은 title 메서드로 만들 수 있지만 마지막 그래프에만 적용됨

 - suptitle 매개변수를 추가해야 전체 제목으로 지정가능

```
# 산점도 행렬을 그리기
sns.pairplot(data = gng,
            x_vars = ['전용면적','층','경과년수','세대수','주차대수'],
            y_vars = '거래금액',
            kind = 'reg',
            plot_kws = dict(scatter_kws = point_prop , line_kws = line_prop)).fig.suptitle(t= '산점도행렬(회귀직선추가)', fontweight ='bold', y=1.2);
```

**출력결과 )** 

![](https://blog.kakaocdn.net/dna/bP8i9M/dJMcagKRQgy/AAAAAAAAAAAAAAAAAAAAABUtMcYc2ccO5alobUoJG1HXYgF684rMdgy_x2yx4M1z/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=Pgl1oYLnZrwsX0SqlgItcKBchrE%3D)

### **히트맵 그리기**

 - 데이터의 수치를 색상의 진하기로 표현하여 패턴을 찾음

 - 가로축과 세로축이 만나는 지점의 값을 색으로 표현 , 수치가 높은 곳, 낮은곳을 즉각적으로 구분

 - 변수 별 상관계수 행렬 시각화에 사용

 - 히트맵을 왜 그리냐? :  **해당 데이터표를 통해 각 열에 대한 각 열값이 얼마나 연관이 있는지 확인하기 위함**  
 - **X-Y 간 상관계수가 높을수록 관계성이 있다**  
 - **X-X 간 상관계수가 낮을수록 서로 경쟁관계로 다중 공선성이 있어 둘중하나의 데이터는 제외시켜야한다.**  
 - 다중 공선성 : 두 데이터는 사실상 **같은 정보를 담고 있는것**

```
# 히트맵 그리기
# 스피어만 , 피어슨 계산 방식에 대해 세팅하고싶으면 method 인수에 세팅하면된다
# 표를 담는 창의 크기를 변경
plt.figure(figsize = (8,6))

# seaborn의 heatmap을 사용하여 데이터 출력
sns.heatmap(data = gng.corr(numeric_only = True),    # gng에서 연속형 변수 간 상관계수 행렬을 생성한걸 히트맵으로 그림
           cmap = 'RdYlBu',            # 팔레트 지정
           annot = True,               #주석(상관계수)를 표시해라
           annot_kws = {'size' : 8,'fontweight' : 'bold'} ,    #주석에 대한 스타일 지정
           fmt = '.2f');               #실수형 데이터의 출력 형태를 지정
```

**출력결과 )** 

![](https://blog.kakaocdn.net/dna/bOtOim/dJMcac2I0tm/AAAAAAAAAAAAAAAAAAAAAFamsXMjLVRfr__ZeXq0_0sU0dzZRCXhUDIDZ_y_uLVR/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=vfcL2ofyD3vJJ7cInBMA1EUaVMg%3D)

#### **피어슨 상관계수**

 - 가장 보편적으로 사용되는 상관계수로 , 두 변수 사이의 선형적인(직선) 관계를 측정함

 - **연속된 데이터(키 , 몸무게 , 온도 , 점수)처럼 수치로 측정되는 데이터에 사용**

 - 직선관계 : **데이터가 직선(y = ax + b) 형태로 분포할 때 가장 적합**

 - 이상치 민감 : 평균을 바탕으로 계산되어 **전체 흐름에서 크게 벗어난 이상치 하나가 있다면 수치가 왜곡될 가능성이 큼**

 - 왜 사용하는가 : **한 변수가 변할 때 다른 변수가 일정한 비율로 변하는지 수치화하기 위해 사용**, 단위에 상관없이  -1에서 1사이의 값으로 표준화된 결과를 얻을 수 있음

#### **스피어만 상관계수**

 - 실제 수치대신에 순위를 매겨서 관계를 측정하는 방식

 - 순위형/서열 데이터 : **만족도 , 성적 석차 등 순서가 있는 데이터에 적합**

 - 비선형 / 단조 관계 : 직선이 아니여도 , 한쪽이 커질 때 다른 쪽도 계속 커지기만 하면 높은 점수를 줌

 - 이상치 적합 : **실제 값의 크기 대신 순서만 비교**하여 , **갑자기 튀는 값이 있어도 결과가 크게 변하지 않음**

 - 왜 사용하는가 : **정규 분포를 따르지 않거나 , 변수 간의 관계가 직선이 아닌 곡선 형태일 때 신뢰할 수 있는 결과를 얻기 위해 사용** , **데이터가 숫자가 아닌 순위로만 주어졌을 때 필수적** 

### **대화형 시각화**

 - **사용자가 차트와 직접 상호작용하면서 데이터의 잠재된 패턴과 인사이트를 찾는 탐색적 데이터 분석**을 직관적으로 수행할 수 있도록 함

 - 툴팁 : 특정 데이터 포인트에 마우스를 올리면 상세 정보 표시

 - 줌 & 팬 : 그래프의 특정 부분을 확대 , 특정 영역으로 이동

 - 선택 : 그래프의 특정 부분을 직사각형 또는 다각형으로 선택

 - 필터링 : 범례의 특정 항목을 마우스로 클릭시 해당 항목을 그래프에 추가 및 제외

```
# 대화형 시각화를 사용하기 위해 plotly의 express 라이브러리를 import함

# 샘플 꽃잎 데이터를 불러옴
df = px.data.iris()

# 대화형 산점도 그래프를 그리기위해 plotly.express의 scatter 메서드 사용
# x , y : x축 y축에 각각 원하는 데이터 설정
# size : 각 점의 크기를 설정 , petal_length가 점점 커지면 점도 커짐
# hover_data : 마우스를 올렸을때 나타나는 툴팁상자에 추가적인 정보를 추가함
# color : 데이터를 그룹화하여 색상을 입힘(우측 상단에 범주 처럼 생성)
# title : 제목
# width : 그래프 출력창의 가로 길이
# height : 그래프 출력창의 세로 길이
fig = px.scatter(data_frame = df , x = 'sepal_width', y = 'sepal_length',
                size = 'petal_length' , hover_data = 'petal_width' , 
                color = 'species',title = 'Sepal Width & Sepal Length',
                width = 800 , height = 800)
fig.show()
```

**출력결과 )** 

![](https://blog.kakaocdn.net/dna/bR8PGs/dJMcabQj5Vm/AAAAAAAAAAAAAAAAAAAAANyqoRAZ2anuZIAYZ35jUTN4CSehpS4RUpQOC26A-868/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=xjK1Y1xTw99tJpO8UQaTo0ThFjs%3D)

[[
데이터 분석
]]


