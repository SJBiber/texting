## **글 쓴 이유**

 - 금일은 9일차에 진행한 전체적인 내용 복기와 git 사용 , Postgre DB인 Supabase 구성을 진행함

 - 이전 내용들은 전 일차에 작성한 내용과 전부 같기때문에 금일 새로배운 Postgre DB Supabase를 설명하도록 하겠음

## **Postgre DB 란 ?**

 - 오픈소스 관계형 데이터베이스(RDBMS)

 - 무료이면서도 유료 DB(Oracle . Altibase 등)에 뒤쳐지지않는 기능을 제공

 - 데이터의 안정성을 강력하게 유지한 상태로 설계하기가 가능함 (설계 엄격)

 - 단순한 텍스트나 숫자뿐만 아니라 . Json , 위치정보 , 이미지 벡터 데이터 등 아주 다양한 형태를 처리 할 수 있음

 - 사용자가 직접 기능을 추가하거나 확장하기가 쉬움

## **백터 DB 란 ?**

 - 전통적인 DB가 숫자나 글자를 저장한다면 , 백터 DB는 의미를 저장함

 - 원리 : AI가 문장이나 이미지를 숫자의 배열(백터)로 변환하면 , 백터 DB는 이 숫자들 사이의 거리를 계산해 가장 비슷한 의미를 가진 데이터를 찾아냄

 - 왜 사용하는가 ? 챗봇이 내 질문과 관련된 문서를 찾을 때(**RAG) , 혹은 사진으로 비슷한 상품을 찾을 때 필수적임

## **RAG(Retrieval Augmented Generation) 란 ?**

 - AI 서비스(챗봇)을 만들 때 핵심이 되는 기술 , 검색 증강 생성이라고 함

 - 일반 AI는 공부한 지식만 가지고 정보를 알려줘 할루시네이션이 발생 할 수 있음 ,RAG 방식의 AI는 기존에 학습된 정보를 데이터 베이스에 저장하고 , 모르거나 애매한 내용이 나오면 데이터 베이스를 조사해서 정보를 전달함

#### **RAG 의 동작 원리**

 - Retrieval (탐색) : 사용자가 질문 요청을 하면 AI가 데이터 베이스(ex. 백터 DB)에서 관련 있는 문서를 먼저 찾아옴

 - Augmented (증강) : 찾아온 정보를 원래 질문과 합침

 - Generation (생성) : 합쳐진 정보를 바탕으로 Open AI 같은 모델이 갑변을 만들어냄

#### **왜 RAG를 사용하는가 ?**

 - 최신 정보를 제공해준다 , AI 모델을 새로 학습시키지 않아도 , DB에 뉴스나 문서를 넣기만 하면 바로 반영됨

 - 할루시네이션(거짓말) 방지를 해준다 . 가끔 AI에게 애매한 질문을 하게된다면 엉뚱한 소리를 하게 되는데 해당 현상을 줄여줌

 - 내부 데이터 활용 . 회사의 대외비 문서 , 개인적인 일기장을 바탕으로 답변 할 수 있게 학습시킬수 있음

## **Supabase 란 ?**

![](https://blog.kakaocdn.net/dna/bfv5mD/dJMcacBNpUz/AAAAAAAAAAAAAAAAAAAAABq2BHM5yhvjiLdcOa3lSQg78pg38qkaE4Wa0vBnnp7o/img.jpg?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=adbVWG4gcEmdX73iHvkHkdmUluQ%3D)

 - 개발에 필요한 모든 Backend 기능을 패키지로 빌려주는 서비스

 - Firebase(구글의 백엔드 서비스)의 오픈소스 대체제라고 불림

#### **Supabase 특징**

 - PostgreSQL을 그대로 사용함

 - Supabase는 테이블만 만들면 API 주소를 자동으로 생성해줌

 - 앱 / 웹 서비스를 만들 때 필요한 기능 Auth , Storage , Edge Functions , Realtime 등 제공해줌

 - 최신 AI 트랜드에 맞춰 백터 데이터를 저장하고 검색하는 기능을 기본적으로 제공함 , RAG 시스템을 만들 때 Supabase 하나로 끝낼 수 있음

 - DBeaver** 와의 호환성이 좋음

 - 파이썬 친화적임 , SQLAlchemy나 pandas와 연동해서 데이터를 수집하고 가공해서 넣기에 최적화 되어 있음

 - 비용효율성이 뛰어남 , 일단 무료로 시작 가능하고 , 사용한 만큼만 비용을 내기 때문에 개인 프로젝트나 스타트업 기업 단계에서 최고

## **DBeaver 란 ?**

![](https://blog.kakaocdn.net/dna/CM6bX/dJMb9958B5p/AAAAAAAAAAAAAAAAAAAAANBqL9vPzyKlGnczUP2yvkdqK9s60ThCtK4V5JkiABIt/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1769871599&allow_ip=&allow_referer=&signature=O7wfJJSVouCJ3TOrmkPyOhVF1ac%3D)

 - 쉽게 말해서 데이터베이스의 엑셀, 탐색기

 - 데이터 베이스내 파일들이 있지만 직접 확인 하기 어려움 , DBeaver를 사용하므로써 해당 파일을 볼수있게 해주며 관리 또한 제공해주는 관리 도구임

#### **DBeaver 특징**

 - 거의 모든 종류의 DB를 하나의 프로그램에서 관리할 수 있게 해줌

 - 접근성이 좋은 GUI를 제공해주어 엑셀 같은 뷰를 제공하여 표 형식으로 데이터를 바로 보고 수정도 가능

 - ER 다이어그램을 제공해주어 테이블 사이의 관계를 그림으로 그려주어 구조를 한눈에 파악이 가능

 - 쿼리를 짤 때 자동 완성 기능을 SQL 에디터로 제공해서 빠르고 정확하게 코딩 가능

 - Supabase 웹 화면에서도 데이터를 볼 수 있지만 DBeaver를 사용해서 수만 건의 데이터를 더 빠르게 처리하거나 , 다른 파일을 내보내기가 휠씬 편함

[[3.DB]]