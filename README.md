# study-cohort
study &amp; summary about cohort analysis

# 코호트 분석 cohort analysis
- 특정 기간동안 공통된 특성이나 경험을 갖는 사용자 집단을 분석하는 방법
- 처음 방문한 날짜, 특정한 캠페인 또는 마케팅으로 인한 유입 등 특정한 조건에 해당되는 사용자를 그룹화하고, 시간 흐름에 따른 패턴(행동, 목적 등)을 추적한다.
- **코호트 분석의 두 가지 조건 : 동일한 경험, 동일한 기간**
- 파이썬으로 직접 계산할 수도 있고, 웹에서 코호트 분석 서비스를 이용할 수도 있다. 

### 코호트 분석의 장점
- 주로 이커머스나 웹이나 앱 서비스 분야에서 고객의 행동 패턴을 분석하기위해서 사용된다.
- **Retention 분석** : 고객 유지율, 특히 이커머스에서 고객 행동의 패턴을 분석하는 지표로 사용된다. 또한 고객의 이탈 시점을 파악할 수 있다.
- 특정한 고객 집단의 특성 분석 : 여러가지 동일한 경험의 조건을 조합하여, 특정한 고객 집단을 만들고, 이 고객 집단에 대한 인사이트를 얻을 수 있다. 

### 코호트 분석에서 얻을 수 있는 지표들
- 고객 유지율
- 고객 이탈율
- 사용자 유지율
- 시간 경과에 따른 마케팅 캠페인 효과 분석
- 신규 회원가입자의 유지율
- 유입 출처에 따른 고객 가치

### 코호트 지표
- 주로 히트맵 형태이다.
- 어떤 조건으로 분석하느냐에 따라서 가로, 세로, 대각선 행의 의미가 달라질 수 있다.
- 현재 기준일로부터 7일간 A 고객 집단의 패턴을 분석한다고 했을 때
    - 가로행 : 일자별 패턴의 변화 (A 집단의 고객의 행동 패턴이 얼마나 유지되고 있는지)
    - 세로열 : 일자별 고객 방문수
    - 대각선 : 기준일별 1,2,3..일이 경과한 지점의 데이터이므로, 같은 날의 고객 데이터로 볼 수 있다. 한 대각선이 1230 방문자 데이터 일 때, 각 행별로 서로 다른 기준일로부터 특정기간이 지난 후 재방문한 데이터를 의미한다. 1228 방문한 사람은 2틀 후 얼마나 방문했는지, 1229에 방문한 사람은 하루 후 얼마나 방문했는지를 1230 대각선으로 알 수 있다.

# 코호트 분석 방법

### 1. 데이터 임포트
- 데이터 확인 : 데이터 사이즈, 사용할 변수 컬럼 확인(특징 벡터)
- 데이터 전처리 : null 데이터 처리, 불필요한 컬럼 확인
    - 분석의 대상이 되는 변수를 정하고, 이 변수를 어떻게 코호트 집단으로 만들 것인지 확인

### 2. 주문날짜를 년-월로 변경 : OrderMonth
- 주문날짜 또는 로그인날짜 등을 특정한 형식으로(년-월) 변환
    - 또는 분석에 사용할 형태로 변경

### 3. 고객별 첫 주문날짜 컬럼 만들기 : CohortMonth
- 동일한 경험 그룹의 기준이 된다. 
    - 같은 달에 주문한 사람, 같은 제품을 주문한 사람, 같은 시간에 로그인 한 사람 등
- 고객별 여러 주문날짜 또는 로그인 날짜 중에서 가장 처음 행동한 날짜 데이터와 같다.
- 이 데이터를 기준으로 특정한 기간을 구분하고, 시간의 흐름에 따른 지표의 변화를 통한 인사이트를 추출할 수 있다.

### 4.  분석용 테이블 만들기
- 첫 주문날짜 컬럼과 일별 주문날짜 컬럼을 사용하여 groupby 한다.
    - 첫 주문날짜 별, 일별 주문날짜별 테이블이 만들어진다.
- userid, 주문금액 등의 컬럼을 counting, sum, min, max, mean 등으로 계산한다.
- 첫 주문날짜가 2022-05월인 코호트 집단을 만들고, 05월에 첫 주문한 집단의 일별 주문날짜별로 유저의 아이디 또는 유저의 수, 주문금액 총함 또는 주문횟수 등을 알 수 있다.
- 2022-05 -> 2022-06 -> 주문한 유저의 수, 주문한 금액의 총합

### 5. cohort period 만들기 : CohortPeriod
- 첫 주문날짜를 기준으로 다음 주문날짜에 대한 단위길이 구간
    - 2022-05월에 주문한 사람이(코호트 집단) 그 다음에는 언제 주문했는지 단위구간으로 나누어서 파악한다.
    - 첫 주문날짜를 기준으로 0, 1, 2, 3, 4, 5....
- 첫 주문날짜가 뒤일 수록 이 구간이 짧아진다.
    - 현재 시점(혹은 데이터의 마지막 주문날짜시점)까지 구간을 만들기 때문
    - 2022-01 : 1월, 2월, 3월, 4월, 5월
    - 2022-03 : 3월, 4월, 5월
- CohortPeriod가 코호트 그룹의 행동 패턴을 파악하기 위한 시간 흐름에 해당한다.    

### 6.  Retention Matrix
- 첫 주문날짜와 일별 주문날짜 구간을 두 축으로 하는 테이블을 만든다.
    - CohortMonth와 CohortPeriod를 x, y 축으로 한다.
- **첫 주문날짜와 첫 번째 구간의 값으로 전체 데이터를 나눈다.**
    - 즉 첫 주문날짜로부터 각 구간의 비중이 어떻게 달라지는 알 수 있다.
    - 첫 주문날짜의 금액과 다음 구간에서의 주문금액이 얼마나 변하는지 알 수 있다.
    - 첫 주문날짜의 방문자수가 다음 구간에서의 방문자와 얼마나 변했는지 알 수 있다.

### 7. 데이터 시각화를 통한 인사이트 추출
- **heatmap**
    - 코호트 집단의 행동 패턴을 시간의 경과에 따라 어떻게 변화하는지 알 수 있다.
    - 여기에서 얻은 인사이트는 비즈니스의 특성에 따라서 다르게 해석될 수 있다.
- **snake plot**
    - RFM 테이블을 만들고 등급별 RFM 수치를 비교할 수 있다.
- **thseus**    
    - 코호트 분석 테이블의 패턴을 통하여 다음 기간에 대한 예측을 돕는 오픈 라이브러리
    - 누적 변화 그래프, 패턴 그래프를 반환 해준다.
    - 실시간 데이터 보다 이미 생성된 데이터에 대한 어느정도 예측이 가능하다. 베타버전

### 8. 예측 또는 다양한 수치 분석을 통한 인사이트 추출
- 예측 : 코호트 분석 기간 이후 패턴 예측
- RFMT : Recency, Frequency, MonetaryValue, Tenure를 수치를 통한 고객 등급 구분 
- KMeans : K-Means 그룹화하여 군집별 특성 데이터 분석

### 요약
- 데이터 임포트
- 날짜 데이터 전처리
    - 분석에 필요한 invoice 날짜 데이터 추출
- 고객별 첫주문 날짜 데이터 생성 : cohortMonth
- 고객별 첫주문 날짜와 개별 주문날짜의 차이 계산 : CohortPeriod
- 첫주문 월 기준으로 그룹화한 후, 시간경과에 따른 비율 계산 : Retention Matrix
- RFMT 분석
- K-Means 클러스터링 분석
- 데이터 시각화 : plot, heatmap, snakeplot(lineplot)

