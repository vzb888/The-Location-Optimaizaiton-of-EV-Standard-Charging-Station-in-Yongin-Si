# 2022 BIGCONTEST


## 🚘 용인시 전기차  완속 충전소 입지 선정 모델 개발


### 📅 프로젝트 진행기간

> 2022.9. 22 ~ 2022. 10. 14
> 

### 📔 프로젝트 내용

> 용인시 지역 특성을 반영한 객관적인 최적의 충전소 입지선정지수 개발 및 이를 통한 충전소 최적화 모델 구축
> 

### 💪 역할

- 여민희 : 데이터 수집, EDA, Preprocessing, PPT, MCLP model 구현
- 유한솔 : 데이터 수집, EDA(QGIS), 기획서, PPT, MCLP model 구현
- 윤병윤 : 데이터 수집, 기획서, MCLP model 구현
- 이재엽 : 데이터 수집, Preprocessing, Correlation Matrix, MCLP model 구현
- 전은성 : 데이터 수집, Preprocessing, Correlation Matrix, MCLP model 구현

### 🗄️ 데이터셋

- **빅콘테스트 데이터 셋**
    - ev_app_resident(2022.6.1~ 2022. 6.30 까지의 용인시 거주자의 전기차 앱실행 고객 수)
    - ev_app_activity(2022.6.1~ 2022. 6.30 까지의 용인시 유동인구의 전기차 앱실행 고객 수)
    - ev_app_activity_resident(2022.6.1~ 2022. 6.30 까지의 용인시 거주자의 타지역 전기차 앱실행 고객 수)
- **EDA**
    - [용인시 인구 및 세대현황(6월말기준)](https://www.yongin.go.kr/user/bbs/BD_selectBbs.do?q_menu=&q_clCode=1&q_lwprtClCode=&q_searchKeyTy=sj___1002&q_searchVal=&q_category=&q_bbsCode=1030&q_bbscttSn=20220727145136177&q_currPage=1&q_sortName=&q_sortOrder=&)
    - [용인시내 CCTV 설치 위치](https://www.data.go.kr/data/15013115/standard.do)
    - [용인시내 공영주차장 설치 위치](https://www.yongin.go.kr/pdata/web/parkinglot/selectParkingLotList.do)
    - [용인시 교통량](https://viewt.ktdb.go.kr/cong/map/page.do)
    - [경기도 주요도로망](https://viewt.ktdb.go.kr/cong/map/moveNetworkDownload.do)
    - [용인시 연속주제도](https://gis.yongin.go.kr/#/gisdown?mode=userarea)
- **학습 데이터**
    - [용인시 인구수 데이터 (100m 격자 단위)](https://sgis.kostat.go.kr/view/pss/openDataIntrcn)
    - 용인시 전기차 등록 수 데이터 (행정동 단위)
     출처 : 용인시(자료 직접 요청)
    - [용인시 건물 수 데이터 (100m 격자 단위)](https://sgis.kostat.go.kr/view/pss/openDataIntrcn)
    - 빅콘테스트2022 챔피언스 부문 LGU+ 전기차 충전 어플리케이션 실행 데이터
    (2022.6.1~ 2022. 6.30 까지의 용인시 거주자의 전기차 앱실행 고객수)(좌표계)
    - [전국 전기차 충전소 표준 데이터(좌표계)](https://www.data.go.kr/data/15013115/standard.do)

### ⚙️ Process

- **사용 툴** : Python, QGIS
- **EDA**
    - 행정동별로 인구수, 차량 등록 수에 따라 folium을 사용해서 용인시 지도 위에 시각화
    - 주소를 기준으로 충전소 카운트(같은 주소→  충전소1개) 후 QGIS로 지도 위에 시각화
    - 용인 시내 CCTV 설치 위치
        - 환경부 “***2021년 전기자동차 보급 및 충전인프라 구축사업 충전소 설치 운영 지침***”에 따르면,
        충전소 기물 파손 방지를 목적으로 폐쇄 회로가 설치 되어야함
        - 추가 설비를 설치를 줄임으로써 설치비용 줄이기 가능
    - 용인 시내 공영주차장 설치 위치
        - 환경부 충전소 설치 운영 지침 중 충전시설 설치를 위한 현장조사 검토항목 중 전용 주차면 지정가능한 장소 또는 다른 차량이 주차할 수 없는 장소에 전기차 충전설비가 설치 되어야 하며, 또한 장시간 주차가 가능하여야 한다고 명시되어 있다.
        - 공영주차장 설비 설치 비용이 상대적으로 저렴하며 주차 공간이 충분하기 때문에 입지 선정시 우선적으로 고려되어야 함
    - 교통량 데이터
        - 교통량이 많은 곳은 전기차 충전의 수요에 영향을 줄 것이라 예상
        - QGIS legend format(%1 ~ %2)을 토대로 classification
    - 용인시 연속주제도를 통해 충전소 주변의 환경을 고려하여 설치 불가능지역 판단
- **Data Preprocessing**
    - 각 데이터셋별로 다른 동 기준(행정동, 법정동) → 행정동으로 통일
    - Geocoding / Reverse Geocoding
        - 위도, 경도를 문자열 주소로 혹은 이를 역으로 변환
        - EDA, MCLP에 적용
    - 좌표계 변환
        - 우리나라의 기준 좌표계와 맞지 않아 UTM-K(국가지점코드)로 변환하여 격자단위 셀에 합침
    - 행정동 단위 → 100m 격자 단위로 변환
        - 100m 격자단위 건물 수 데이터를 활용해서 행정동 주소 단위인 전기차 등록 데이터 셋을 격자단위로 변환
    - 전기차 충전소 중 공용 충전소 추출
        - 이용자의 제한이 없는 개방형 충전소만 추출
        - 제외한 비개방형 충전소 : 아파트, 오피스텔, 주상복합, 빌라, 호텔
- **지수 추출**
    - 데이터를 활용하여 완충형 전기차 충전소 입지 선정을 위한 지수 개발 후, 최적의 입지 추천
<img src = "https://s3.us-west-2.amazonaws.com/secure.notion-static.com/3a25230e-8cf9-4f15-9901-091c76d6493a/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20221024%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20221024T052418Z&X-Amz-Expires=86400&X-Amz-Signature=56e48c78ee172ac770c67ee9665c9501739f64d6847b4fb002b2e2c8fe926fca&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject">        

        
    
    - 방법
        - 수요 요인, 접근성 요인, 부지 적합성 요인을 이용
        - 전기차 충전 어플리케이션 실행 위치를 전기차 충전에 대한 수요라고 가정
        - 100m 격자 당 인구 수와 전기차 등록 대수와의 상관관계를 분석하여 수요 지수를 도출

- **변수간 상관관계** (주유소 위치, 자동차 등록 대수, 인구 수, 용인시 거주자 기준 app 실행수)
    - 수요와의 관계성을 알아보고 해당 지수의 타당성을 확인, 그 비중을 가지고 x_score 도출
    (상관관계가 적을 경우 제외(0.2 이하)) → 주유소와의 거리는 전기차 충전 수요와 관계가 없다는 결론
    - 피어슨 상관계수 사용

<img src="https://s3.us-west-2.amazonaws.com/secure.notion-static.com/a4bfef8f-8630-455b-9a69-cb8535969bb2/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20221024%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20221024T051446Z&X-Amz-Expires=86400&X-Amz-Signature=b6f805132cd065c866980cd14ab688882fd9f410ab55e4daff96080a3449237a&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject">

- **MCLP**(Maximal Covering Location Problem)
: 주어진 제약조건 하에서 시설물이 커버하는 수요량을 최대화하는 위치를 선정

<img src= "https://s3.us-west-2.amazonaws.com/secure.notion-static.com/f55a3ba1-b39d-40f3-9ae2-afb1a919083d/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20221024%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20221024T051637Z&X-Amz-Expires=86400&X-Amz-Signature=6d51e2678a5869810c82fec7c3f9ec2b7e9bf61736caddead2c84c7298944b6b&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject">

### **🏅 결과**

<img src= "https://s3.us-west-2.amazonaws.com/secure.notion-static.com/593df7ba-ccef-4aac-9580-0a6c2a2bdab8/Screenshot_2022-10-21_at_11.47.46_AM.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20221024%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20221024T051659Z&X-Amz-Expires=86400&X-Amz-Signature=e233982b51fb12ded87abdefabc41e39767faa9173d875b8f606333c50024b42&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Screenshot%25202022-10-21%2520at%252011.47.46%2520AM.png%22&x-id=GetObject">

- 수요량(x_score 지수를 통해 추출)을 지도에 점으로 표시 (Legend 그림참고)
    - 채택된 후보군
        - 주황색 동그라미 : MCLP로 후보군 10개 추출
        - 보라색 동그라미  : MCLP로 후보군 15개 추출
        - 노란색 동그라미 : MCLP로 후보군 20개 추출
        - 초록색 동그라미 : MCLP로 후보군 30개 추출
    - 수요
        - 하얀색 점 : 수요 낮음
        - 연보라색 점 : 수요 중간
        - 보라색 점 : 수요 높음

### 보완점

- 입지 선정시 고려할 요인의 개수(지수)를 늘려 선정한 입지의 타당성과 신뢰도를 올려야 함.
- EDA로 활용한 교통량 데이터와 공영 주차장 위치 데이터 등을 입지선정 모델의 지수로 활용 할 수 있는 방안 모색 필요.
- 앱 실행 수 한가지만 Y값으로 설정 하였으나, 충전소 별 일일 충전량을 추가 데이터로 사용 하여 Y 값을 보완 하였다면 좀 더 명확한 수요량 지수에 대한 타당성을 확보 할 수 있을 것.
- 기존 설치된 각각 충전소의 수요와 공급량을 비교하여 가중치 조절을 해 새로운 충전소 입지를 고려 하여야 했으나, 기존 충전소 중심 반경 1km의 모든 수요를 커버한다 가정했기 때문에 결과에 대한 정밀도가 떨어짐. 충전소 별 충전기 대수를 계산 및 관련 법규 참고하여 면적별 충전소 당 설치가능 충전기 대 수의 일일 최대 커버량 만큼만 처리 하도록 보완 해야 함.


참고자료: https://stackoverflow.com/questions/51501074/implementing-mclp-in-pulp
