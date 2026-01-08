## **글 쓴 이유**

 - 이번 데이터 엔지니어링 부분은 대부분 antigravity IDE에 탑재되어 있는 AI와 같이 개발을 진행하는 방식으로 함

 - 모든 소스는 AI가 제공해주었지만 해당 하는 소스의 내용을 파악하기 위해 정리하고자 작성

## **목적**

 - Youtube 에서 제공되는 Youtube API V3를 사용해보고자 함

 - 검색 기반 데이터 수집 : 특정 키워드에 대한 영상 메타데이터 자동 수집

 - 카테코리별 데이터 수집 : 주식 ,게임 , 노래 ,흑백요리사 등 관심사 별 데이터셋을 CSV 파일로 추출

## **목표 설정 및 결과**

 - 원하는 키워드를 입력하고 키워드에 해당하는 영상들의 정보를 추출해 csv로 변환 

 - cmd 창에는 일별로 해당 키워드 영상의 업로드 수 , 키워드 영상들의 누적 조회수 합계를 출력

## **사용된 기술**

 - 언어 : Python 3.12

 - API : Youtube Data API v3

 - 사용된 파이썬 라이브러리 : 'google-api-python-client' , 'pandas' , 'python-dotenv'

## **동작 설명**

 1. 프로세스 실행시키면 input 값으로 원하는 키워드를 입력

 2. api 데이터 수집을 위해 API_KEY 체크

 3. 성공 시 api 연동 / 실패 시 종료

 4. 원하는 데이터 개수 만큼(내부에 1000개로 설정) youtube.serch().list(조회 조건) 함수를 사용해서 

영상들을 받아옴

 * 조회 조건 : [키워드] , [id, 기본정보 파트를 가져옴] , [요청개수] , [비디오타입] , [최신날짜순 정렬] 

 5. 받아온 응답에 영상 리스트가 전달되어 get 을 사용해서 리스트에 저장

 6. 리스트에 있는 영상 정보(영상 고유번호 , 제목 , 업로드 날짜 , 채널)를 하나하나 추출해서 append 함

 7.  각 영상의 상세정보를 얻기위해 youtube.videos().list() 함수를 사용해서 해당 영상 고유번호의 정보를 가져옴

 8. 해당 영상의 정보에서 조회수를 가져옴

 9. 기존에 append 했던 비디오 정보에 비디오 조회수 컬럼을 추가해서 넣어줌

 10. 크롤링된 데이터를 일별로 계산하여 print로 출력

## **소스**

```
import os
from googleapiclient.discovery import build
from datetime import datetime
import pandas as pd
from dotenv import load_dotenv

# .env 파일에서 환경 변수(API 키 등)를 로드합니다.
load_dotenv()

# YouTube API 설정
# 구글 클라우드 콘솔(Google Cloud Console)에서 발급받은 API 키를 사용합니다.
API_KEY = os.getenv('YOUTUBE_API_KEY')
YOUTUBE_API_SERVICE_NAME = 'youtube'
YOUTUBE_API_VERSION = 'v3'

def youtube_search(query, max_results=1000):
    """
    YouTube에서 키워드를 검색하고 영상 목록과 상세 통계 데이터를 가져오는 함수입니다.
    기본적으로 50개 이상의 결과를 가져오기 위해 페이지네이션(nextPageToken)을 사용합니다.
    
    :param query: 검색할 키워드 (문자열)
    :param max_results: 가져올 최대 검색 결과 개수 (기본값 1000)
    :return: 수집된 데이터가 담긴 Pandas DataFrame
    """
    if not API_KEY:
        print("에러: YOUTUBE_API_KEY가 설정되지 않았습니다. .env 파일을 확인해주세요.")
        return None

    # YouTube API 서비스 객체 생성
    youtube = build(YOUTUBE_API_SERVICE_NAME, YOUTUBE_API_VERSION, developerKey=API_KEY)

    videos = []
    video_ids = []
    next_page_token = None
    
    # max_results 개수에 도달할 때까지 반복 호출
    # YouTube API의 search().list 메서드는 한 번에 최대 50개까지만 반환할 수 있습니다.
    while len(videos) < max_results:
        # 이번 루프에서 요청할 개수 계산 (남은 개수가 50보다 작으면 그만큼만 요청)
        results_to_fetch = min(50, max_results - len(videos))
        
        # 1. 키워드 검색 수행
        search_response = youtube.search().list(
            q=query,                # 검색어
            part='id,snippet',      # 가져올 파트 (ID와 기본 정보)
            maxResults=results_to_fetch, # 요청 개수
            type='video',           # 비디오 타입만 검색
            order='date',           # 최신 날짜순 정렬
            pageToken=next_page_token # 다음 페이지를 가져오기 위한 토큰
        ).execute()

        current_items = search_response.get('items', [])
        if not current_items:
            break

        # 검색 결과에서 비디오 기본 정보 추출
        for item in current_items:
            video_ids.append(item['id']['videoId'])
            videos.append({
                'video_id': item['id']['videoId'],
                'title': item['snippet']['title'],
                'published_at': item['snippet']['publishedAt'],
                'channel_title': item['snippet']['channelTitle']
            })

        # 다음 페이지 토큰 확인 (더 이상 결과가 없으면 종료)
        next_page_token = search_response.get('nextPageToken')
        if not next_page_token:
            break

    # 2. 영상 상세 정보(조회수) 가져오기
    # search API는 조회수를 직접 반환하지 않으므로 videos().list API를 별도로 호출해야 합니다.
    # 이 API 역시 한 번에 최대 50개의 ID만 처리할 수 있습니다.
    stats_map = {}
    for i in range(0, len(video_ids), 50):
        chunk_ids = video_ids[i:i+50]
        stats_response = youtube.videos().list(
            part='statistics',
            id=','.join(chunk_ids)
        ).execute()
        
        # 비디오 ID별로 조회수를 맵핑 (조회수가 비공개인 경우 0으로 처리)
        for item in stats_response.get('items', []):
            stats_map[item['id']] = item['statistics'].get('viewCount', 0)

    # 3. 비디오 데이터에 조회수 추가
    for video in videos:
        video['view_count'] = int(stats_map.get(video['video_id'], 0))

    return pd.DataFrame(videos)

def analyze_data(df):
    """
    수집된 데이터를 Pandas를 활용해 일별로 분석하고 통계를 내는 함수입니다.
    
    :param df: 수집된 영상 정보가 담긴 DataFrame
    :return: 일별 분석 결과 리포트 (DataFrame)
    """
    if df is None or df.empty:
        print("분석할 데이터가 없습니다.")
        return

    # 날짜 형식 변환 (시간 정보 제외하고 날짜만 남김: YYYY-MM-DD)
    df['published_at'] = pd.to_datetime(df['published_at']).dt.date

    # 일별 업로드 된 영상 수 계산
    daily_uploads = df.groupby('published_at').size().reset_index(name='upload_count')
    
    # 일별 등록된 모든 영상의 누적 조회수 합계 계산
    daily_views = df.groupby('published_at')['view_count'].sum().reset_index(name='total_views')

    # 업로드 수와 조회수 데이터를 날짜 기준으로 병합
    result = pd.merge(daily_uploads, daily_views, on='published_at')
    
    # 콘솔에 분석 결과 출력
    print("\n--- 일별 분석 결과 ---")
    print(result)
    
    print("\n--- 전체 요약 ---")
    print(f"총 영상 수: {len(df)}개")
    print(f"전체 누적 조회수: {df['view_count'].sum():,}회")
    
    return result

if __name__ == "__main__":
    # 사용자로부터 키워드 입력 받기
    keyword = input("검색할 키워드를 입력하세요: ")
    
    # 데이터 수집 시작
    data = youtube_search(keyword)
    
    if data is not None:
        # 데이터 분석 수행
        analyze_data(data)
        
        # 수집된 원본 데이터를 CSV 파일로 저장
        # utf-8-sig 인코딩을 사용하여 엑셀에서 한글이 깨지지 않도록 함
        data.to_csv(f'youtube_{keyword}_results.csv', index=False, encoding='utf-8-sig')
        print(f"\n파일 저장 완료: youtube_{keyword}_results.csv")
```

## **결과물** 

```
검색할 키워드를 입력하세요: 흑백요리사

--- 일별 분석 결과 ---
   published_at  upload_count  total_views
0    2024-10-03             1      6635058
1    2024-10-07             1      3165779
2    2024-10-08             3      7485088
3    2024-10-09             2     17078308
4    2024-10-10             3     24563132
5    2024-10-13             1       761106
6    2024-10-15             1       550516
7    2024-10-19             1      4288744
8    2024-10-27             1      9770353
9    2024-11-03             1      4012105
10   2024-11-07             1      9711479
11   2024-11-09             1      1363185
12   2024-11-24             1      2686047
13   2024-12-22             1       325736
14   2025-03-27             1      2378121
15   2025-07-22             1      4134501
16   2025-07-24             1      8231536
17   2025-07-27             1      1986227
18   2025-08-03             1        43691
19   2025-08-19             1      8539545
20   2025-08-29             1       698187
21   2025-09-04             1      1752023
22   2025-10-18             1       967402
23   2025-10-23             2      3078963
24   2025-10-24             1       270659
25   2025-10-29             1      1564344
26   2025-11-16             1      1483129
27   2025-11-18             2      1650768
28   2025-12-02             1       381268
29   2025-12-03             1       406938
30   2025-12-04             2      3325533
31   2025-12-07             2      2896696
32   2025-12-09             1       902985
33   2025-12-12             1      1303535
34   2025-12-13             1      1291666
35   2025-12-14             1       456410
36   2025-12-16            17     32233141
37   2025-12-17            31     66116201
38   2025-12-18            17     36132125
39   2025-12-19             8      9768899
40   2025-12-20            11     20429078
41   2025-12-21             8      8185510
42   2025-12-22             6     12856588
43   2025-12-23            28     46327706
44   2025-12-24            30     44708834
45   2025-12-25            18     24833511
46   2025-12-26            16     19838030
47   2025-12-27            23     20561520
48   2025-12-28            16     16613563
49   2025-12-29            15     24694694
50   2025-12-30            42     46981107
51   2025-12-31            29     14861905
52   2026-01-01            13     13145709
53   2026-01-02            18     12411298
54   2026-01-03            17      9322905
55   2026-01-04            21     23847412
56   2026-01-05             4      2358452
57   2026-01-06            18      9218584
58   2026-01-07            16      1885614
59   2026-01-08             8        43067

--- 전체 요약 ---
총 영상 수: 475개
전체 누적 조회수: 657,516,216회

파일 저장 완료: youtube_흑백요리사_results.csv
```

![](https://blog.kakaocdn.net/dna/cXfxt3/dJMcacBOn95/AAAAAAAAAAAAAAAAAAAAAHj3nskXRlGet4LqzTCXEn2u6BPJ6e51_T5FoElcG62p/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=BC5r8HjR%2B%2BHkBMmB5zxDf7AoCyY%3D)

## **회고**

 - ai와 같이 소스 작성을 진행했는데 버그는 없었지만 예외처리가 없다던가 , 지워지지않은 더미 소스가 몇몇 발견되어  ai가 소스를 만들어주어도 사람이 한번더 더블 체크하는 습관이 중요하다고 느낌

 - 내가 무엇을 만드려고 하고 , 어떤 분석 , 어떤 결과를 도출하려는지 명확한 제시를 해야 한다는것을 느낌

 - API라는것이 나에게는 생소하고 처음 접하는 부분이라 새로운 도전과 새로운 영역에 대한 인사이트를 기를수있어서 좋았음

 - 다음프로젝트는 해당 CSV를 읽어서 일별 누적 조회수를 bar chart로 표현해보도록 하겠음

 - 추가로 youtube 제공 라이브러리에 대한 함수들의 인자값은 글 마지막에 출처를 남기도록 하겠음 (해당 부분을 참고해서 학습진행) 

## 참고

 - youtube api video 라이브러리  : [https://developers.google.com/youtube/v3/docs/videos/list?hl=ko&_gl=1*o0hfy3*_up*MQ..*_ga*MTUyOTU4ODI2MC4xNzY3ODY1ODU5*_ga_SM8HXJ53K2*czE3Njc4NjU4NTkkbzEkZzAkdDE3Njc4NjU4NTkkajYwJGwwJGgw#usage](https://developers.google.com/youtube/v3/docs/videos/list?hl=ko&_gl=1*o0hfy3*_up*MQ..*_ga*MTUyOTU4ODI2MC4xNzY3ODY1ODU5*_ga_SM8HXJ53K2*czE3Njc4NjU4NTkkbzEkZzAkdDE3Njc4NjU4NTkkajYwJGwwJGgw#usage)

[[1.AI]] [[API]]
