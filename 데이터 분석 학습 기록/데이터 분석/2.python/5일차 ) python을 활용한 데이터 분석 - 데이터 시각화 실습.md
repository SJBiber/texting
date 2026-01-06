[[2.python]]
## **글 쓴 이유**

 - 데이터 시각화에 대한 설명을 했으니 이제 파이썬을 사용해서 직접 그래프를 그려보도록 하겠음

 - python 기반 오픈소스 라이브러리인 matplotlib ,seaborn을 사용하여 그래프를 그리겠음

### **1. 시각화할 csv 파일 고르기**

 - 먼저 통계데이터를 시각화하기에 앞서 어떤 데이터를 시각화할지 찾아야한다

 - 글쓴이는 통계 데이터를 제공해주는 공공데이터 포털에서 데이터를 구했다( [공공데이터포털](https://www.data.go.kr/) )

 - **경찰청 범죄 발생 지역별 통계 데이터를 이용해서 그래프화 진행** 하겠다.

### **2. csv 파읽 읽고 리스트 확인**

```
import pandas as pd
import numpy as np
import os
from plt_rcs import *

os.getcwd()
os.chdir('../silsup')

# 범죄 대분류 , 범죄 중분류를 멀티 인덱싱을 하기위해 index_col 을 [0,1]로 세팅 후 리스트를 불러옴
crime_t = pd.read_csv('National_Police_Agency_Crime_Statistics_20241231.csv' ,
                    encoding = 'EUC-KR',
                    index_col = [0,1])
```

 - 다음과 같이 데이터를 읽고 원하는 그래프화의 편의를 위해 전처리를 진행했다

 - 해당 csv데이터에서는 대분류 , 중분류가 각각 되어있어 index_col 인수를 통해 해당 분류를 멀티 인뎅싱 해주었다

![](https://blog.kakaocdn.net/dna/bcgP10/dJMcajgsagq/AAAAAAAAAAAAAAAAAAAAAOBiclpXsyHONUhiXeAInH__EjIqzkqbxUF0dBCCQB3C/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=bJaEbNXovr1yEnRn%2B%2F9sdiASZXA%3D)

```
#행 이름에 서울이 들어가는 것만 추출
seoul_data = crime_t.filter(like = '서울')

#멀티 인덱스라 대분류 : 강력범죄 , 중분류 : 살인기수 로 데이터를 오름차순으로 추출
target_data = seoul_data.loc[('강력범죄','살인기수')].sort_values()

#bar 그래프 그림
sns.barplot(x=target_data.index , y=target_data.values ,hue = target_data.index ,palette = mypal )
plt.xticks(rotation = 90)
plt.ylim(0,9)
plt.xlim(-1,25)
plt.title('서울 지역별 살인기수 발생 현황')
```

![](https://blog.kakaocdn.net/dna/LW1UZ/dJMcaajyMZ3/AAAAAAAAAAAAAAAAAAAAAJcKwSmluTdK_pgnFnjRJ56jVDMYX8-kNlV-I9lzrRUf/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=L45e%2FG8shUZwVbBkc3yTMCWOF6U%3D)

좋아요공감

공유하기

게시글 관리