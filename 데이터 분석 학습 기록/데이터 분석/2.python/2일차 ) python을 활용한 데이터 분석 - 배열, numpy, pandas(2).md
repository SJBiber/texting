[[2.python]]

## **글 쓴 이유**

 - 금일은 2일차 python을 이용한 numpy관련 배열, 데이터 프레임 ,pandas에 대해 정리해보고자 함

 - python의 리스트에 인덱싱 , 슬라이싱 , 데이터프레임 등 생소한 내용이 있어 같이 정리할 예정

##  **배열의 인덱싱**

 - 배열을 생성하면 배열 데이터 순서대로 위치 정보인 index을 가진 상태로 생성됨

 **- 기본적인 배열의 구조는 리스트(List) 형식이며 , 데이터 분석 분야에서 많이 사용함!**

### **1. 기본 인덱싱**

 **- 인덱싱은 배열이나 리스트에서 특정 위치정보(인덱스)에 있는 단 하나의 요소에 접근한느 방법**

 ex) 1차원 구조

        - 인덱스는 항상 0부터 시작한다.

|   |   |   |   |   |
|---|---|---|---|---|
|인덱스|0|1|2|3|
|값|A|B|C|D|

 **- 양수 인덱싱 : 배열의 앞에서부터 0,1,2,3,.. 순으로 접근**

     -> arr[0] -> A

     -> arr[1] -> B

 **- 음수 인덱싱 : 배열의 뒤에서부터 0,-1,-2,-3... 순으로 접근**

     -> arr[-1 -> D  
     -> arr[-1 -> C

### **2. 고급 인덱싱**

#### **불리언 인뎅싱(Boolean indexing)**

 **- 배열과 같은 크기의 불리언(True , False) 배열을 사용해서 True에 해당하는 위치의 값만 필터링 하여 가져옴**

 - 조건에 맞는 요소를 쉽게 추출 할 때 사용됨!

```
lis = [80,85,95,70]
scores = np.array(object = lis)
scores[scores > 80]

# 출력
array([85, 95])

# 각 리스트의 요소를 다음과 같이 Boolean으로 처리하면
scores[[False , True , False , True]]

# 출력
array([85, 70])
```

#### **펜시 인뎅싱(Fancy indexing)  
**

 **- 접근하고 싶은 인덱스들의 리스트나 배열을 인덱스로 전달하여 , 원하는 위치의 요소들만 한 번에 가져옴**

 - 대괄호 안에 정수를 원소로 갖는 리스트를 지정하면 해당 원소를 선택한 배열을 반환

```
lis = [80,85,95,70]
scores = np.array(object = lis)

#출력하고 싶은 index번호를 담은 scores_index 생성
scores_index = [1,2,3]

# scores 리스트에 해당 index를 넣어 인덱싱함
scores[scores_index]

#출력
array([85, 95, 70])
```

## **배열의 슬라이싱**

 **- 리스트 , 튜플 , 문자열 , numpy 배열과 같은 순차적인 자료구조에서 특정 범위의 요소들을 잘라내어 새로운 부분 집합을 만드는 기법**

 **- 슬라이싱은 [start : stop : step] 형태를 사용함**

 **- 인덱싱과 달리 항상 새로운 배열(or 리스트 , 문자열)을 결과로 변환함!**

#### **기본 범위** **추출( start : stop )**

 **- start부터 시작해서 stop-1 인덱스까지 추출함**

```
# 시작 index : 0 , 마지막 추출 index : 4-1 = 3
scores[0:4]

# 출력
array([80, 85, 95, 70])

# 부가내용
# 만약 마지막 index지점을 일일히 정수형으로 넣고싶지않다면
# scores.size를 사용해서 scores 배열의 길이를 넣으면된다!
# 배열의 길이 = 총 요소의 개수
# 해당 인덱스로 조회하면 인덱스는 0부터 총 요소의 개수-1 이므로 마지막 부분 조회가능!

scores.size => 4가 출력됨

# 즉 0부터 4-1까지의 요소의 배열을 반환한다!
scores[0:scores.size]

# 출력
array([80, 85, 95, 70])
```

#### **끝까지 또는 처음부터 (:stop , start:)**

 **- start나 stop에 값을 안넣을 경우 적용됨**

```
# start , stop 모두 생략해서 전부 출력
scores[:]

# 출력
array([80, 85, 95, 70])

# start만 생략해서 처음부터 stop 까지 출력
scores[:3]

# 출력
array([80, 85, 95])

# stop만 생략해서 start 부터 끝까지 출력
scores[1:]

# 출력
array([85, 95, 70])
```

#### **간격 지정 ( : : (양수)step )**

 **- start 와 stop을 생략하고 step만 지정**

```
# 원본
scores = [80,85,95,70]

# 2칸씩 넘어가면서 배열 생성
scores[::2]

# 출력
array([80, 95])
```

#### **역순 슬라이싱 ( : : (음수)step )**

 **- start 와 stop을 생략하고 step에 음수 값을 사용하면 역순으로 출력**

```
scores = [80, 85, 95, 70]

# step에 음수를 넣어 역순으로 출력
scores[::-1]

# 출력
array([70, 95, 85, 80])
```

## **배열의 재구조화**

 **- 1차원 배열을 2차원 배열로 변환하는 법은 reshape(행 , 열) 매서드 사용하면 됨**

```
# arr1 원본
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# arr1을 2행 5열인 2차원 배열로 변환
arr1.reshape(2,5)

# 출력
array([[0, 1, 2, 3, 4],
       [5, 6, 7, 8, 9]])

# reshape에 order 매개변수에 'F'를 지정하면 원소 입력방향을 세로로 변경함
arr1.reshape(2,5,order = 'F')

# 출력
array([[0, 2, 4, 6, 8],
       [1, 3, 5, 7, 9]])
```

 **- reshape에 인수값을 행 , 열 중 하나를 -1로 하면 자동으로 지정해준다**

 - 단 행 , 열 둘다 -1로 세팅하면 오류가 발생한다

```
# case 1) 행값이 -1 : 열 개수로 행 개수를 자동으로 지정
arr1.reshape(-1,5)

# 출력
array([[0, 1, 2, 3, 4],
       [5, 6, 7, 8, 9]])

# case 2) 열값이 -1 : 행 개수로 열 개수를 자동으로 지정
arr1.reshape(10,-1)

# 출력
array([[0],
       [1],
       [2],
       [3],
       [4],
       [5],
       [6],
       [7],
       [8],
       [9]])
```

## **numpy 라이브러리의 수학 관련 속성 및 함수**

![](https://blog.kakaocdn.net/dna/bk5mQa/dJMcabW3tJe/AAAAAAAAAAAAAAAAAAAAAFTX0Ndl-SoVkEwFZ5JcoFnxMSSUhFCsU7t1Gy94_a1C/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=lGjyErmSwYQefamQPsY5UwZlpd8%3D)