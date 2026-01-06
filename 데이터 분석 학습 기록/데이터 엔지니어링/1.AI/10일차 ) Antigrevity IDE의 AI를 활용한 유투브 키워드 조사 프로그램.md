## **글 쓴 이유**

 - Antigrevity를 혼자서 이것저것 활용하며 AI와 함께 Youtube API를 사용해서 유투브 데이터를 읽고 , 해당 데이터를 기반으로 원하는 정보를 출력시키고 싶어 한번 해보았음
   
## **설계 동작 설정** 

 - 먼저 youtube api key를 넣은 환경파일 .env를 만듦

 - youtube_search.py를 만들어 주 기능을 동작하도록 함

 1. 기동 시 키워드를 인자값으로 받음

 2. youtube api key가 담긴 파일 .env를 불러와 key를 스트링 형태로 저장

 3. youtube api key를 사용해서 youtube api와 연동 , 실패 시 에러를 뱉고 종료

 4. 연동 성공 시 키워드를 쿼리로 넣어 키워드에 해당하는 영상 데이터 정보를 가져옴

 5. 영상 ID , 영상 명 , 영상 업로드 날짜 , 영상 업로드 채널 , 영상 조회 수를 저장해서 csv 파일로 생성시킴

 6. 콘솔 창에는 업로드 일자 , 총 업로드된 영상 수 , 총 조회 수를 출력

## **프로그램 작성 시 필요한 라이브러리 조사**

 - os : 환경 변수가 담긴 파일에 접근하기 위해 사용

 - load_dotenv , dotenv : 환경 변수가 담긴 파일을 읽기 위해 사용   

 - build , googleapiclint.discovery : Google API 서비르 객체를 생성하기 위해 사용함 , api key를 입력해서 객체 생성 요청함

 - datetime : 날짜 및 시간을 다루기 위해 사용

 - pandas : 데이터 분석 및 조작을 위해 사용 -> 조회한 영상정보를 데이터 프레임으로 만들고 일별 계산을 위해 사용

## **AI에게 역할 지정 후 원하는 소스를 받아옴**

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

## **실행 결과 (youtube_search.py 실행 결과 확인)**

 - python3 명령을 사용해서 .py 파일 실행

 - 실행시 원하는 키워드 "흑백요리사"를 넣어 요청 후 결과 출력

![](https://blog.kakaocdn.net/dna/vWF6g/dJMcabiAyXa/AAAAAAAAAAAAAAAAAAAAAP25pWE_FL-FSkd8uh5C9uxJGs86DAEUpDCwuhOdQxr-/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=B77jbgTZ51LA1DwvUdfbycHp4QU%3D)

## **실행 결과 (생성 CSV 파일 확인)**

 - 요청한대로 video_id , 영상 제목 , 업로드 날짜 , 채널 이름 , 조회 수 출력 확인

![](https://blog.kakaocdn.net/dna/ctjTvG/dJMcaa42Dry/AAAAAAAAAAAAAAAAAAAAAOoF3Dd8xdEc_cs1RPuQL7fqg48o4SS_hCxmjqiqpEXN/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=bYH6gkywfpxlhZje%2Fk2vrytZ8zo%3D)
[[1.AI]]