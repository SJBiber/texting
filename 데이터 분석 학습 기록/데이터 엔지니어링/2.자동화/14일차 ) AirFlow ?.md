## **글 쓴 이유**

 - 코드 기반 자동화 파이프라인 구축을 위한 툴인 AirFlow를 사용하는 시간을 가짐
 - AirFlow가 무엇인지 , 어떻게 사용하고 , 어떻게 환경을 설정하는지에 대해 간단하게 설명을 작성함

## Airflow 란 ?

- 복잡한 데이터 파이프라인과 워크플로우를 프로그래밍 방식(주로 Python 사용)으로 작성, 예약 및 모니터링하기 위한 오픈 소스 플랫폼

## Airflow의 핵심 개념 : DAG(Directred Acyclic Graph)

- Directed (방향성) : 작업의 흐름이 한 방향으로 진행됨 (A -> B -> C)
- Acyclic (비순환) : 작업이 무한 루프를 돌지않음 (A -> B -> A 처럼 돌아가지 않음)
- Graph (그래프) : 여러 작업들이 선으로 연결된 구조

## Airflow의 동작 그림

![[Pasted image 20260112172257.png]]
- Scheduler : 예약된 시간이 되면 작업을 실행하도록 명령을 내림
- Airflow UI : 사용자가 DAG 상태를 보고, 로그를 확인하고 , 수동으로 실행할 수 있는 UI 제공
- Worker : 실제로 작업을 수행하는 일꾼 (스케쥴러가 명형하면 , Worker가 코드를 실행)
- Database : DAG의 상태( 성공 , 실패 , 실행 중 )와 설정값들을 저장하는 저장소

## Airflow는 왜씀?

### Python 기반

- n8n 처럼 마우스를 드래그 앤 드롭해서 동작을 설정하는 것이 아닌 , 100% Python 코드로 파이프라인을 구성함 , 따라서 버전 관리 , 협업 , 복잡한 로직 구현이 편함

### 의존성 관리

- Task A가 성공해야만 Task B를 실행하고 , Task A가 실패하면 알림을 보내라 같은 복잡한 조건을 쉽게 설정 가능함

### 확장성

- 작업이 많아지면 Worker 서버를 늘려서 동시에 수백 , 수천개의 작업을 처리할 수 있습니다.

### 백필

- 과거 날짜의 데이터를 다시 처리해야 할 때 , 명령어 한 번으로 과거 시점의 작업을 재실행할 수 있음

## Airflow 예시 코드
-----
`from airflow import DAG` 
`from airflow.operators.bash import BashOperator` 
`from datetime import datetime` 

`'# 1. DAG 정의 (이름, 시작일, 스케줄 등)'`
`with DAG(`
    `dag_id='example_workflow',`
    `start_date=datetime(2025, 1, 1),`
    `schedule_interval='@daily', # 매일 실행`
    `catchup=False`
`) as dag:`

    ``# 2. Task 정의``
    ``task_1 = BashOperator(``
        ``task_id='print_hello',``
        ``bash_command='echo "Hello Airflow!"'``
    ``)``

    ``task_2 = BashOperator(``
        ``task_id='print_date',``
        ``bash_command='date'``
    ``)``

    ``# 3. 의존성 설정 (task_1이 끝나면 task_2 실행)``
    ``task_1 >> task_2``
-----
### 상기 코드에서 사용된 값 설명

| 항목 명                                        | 값                     | 설명                                                                                      |
| ------------------------------------------- | --------------------- | --------------------------------------------------------------------------------------- |
| dag_id                                      | 'example_workflow'    | DAG 생성시 고유한 DAG의 ID 값 (코드 관리 시 현하게 파일명과 동일하게 세팅 추천)                                     |
| start_date                                  | datetime(2025, 1, 1)  | DAG 동작의 시작일                                                                             |
| schedule_interval<br>(3버전에서는 schedule 로 바뀜) | @daily                | cron 값을 넣거나 None(트리거로 수동 작업)을 넣어서 원하는 시간대에 동작을 시킬수 있음                                   |
| catchup                                     | False                 | True : Airflow 부팅시 start_date부터 금일까지의 작업을 실행<br>False : 가장 최근의 주기만 실행하거나 다음 예정된 시간부터 실행 |
| bash_command                                | echo "Hello Airflow!" | 터미널에서 실행히킬 명령어 or 파이썬 프로그램을 실행시키고 싶다면 실행                                                |
| task_1 >> task2                             | task_1 >> task2       | task_1을 먼저 진행 후 task2를 진행                                                               |


