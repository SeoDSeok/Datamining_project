# Datamining_project

## 1. data collection <br>
### 1_1 공공데이터 포털의 해양사고 데이터 <br>
![download](https://github.com/SeoDSeok/Datamining_project/assets/122199258/113a6841-0639-420a-b9ec-183cdfd8c7a9) <br>

https://www.data.go.kr/data/3043630/fileData.do

### 1_2 기상청 날씨데이터 <br>
![download](https://github.com/SeoDSeok/Datamining_project/assets/122199258/f05763a8-5d51-4b31-baba-2d663718dbf1) <br>

https://data.kma.go.kr/data/sea/selectBuoyRltmList.do?pgmNo=52
## 2. data merging <br>
### 2_1 해양사고 데이터 
해양사고 데이터는 2011 ~ 2021년 년도 별로 csv파일로 올라와 있음을 확인 
모든 데이터를 합치기 위해 칼럼명을 일치해주는 작업을 거침 (칼럼을 rename 해줌)
ex) 기상특보(2011 ~ 2018), 기상상태(2019 ~ 2021) 같은 칼럼인데 칼럼명이 다름  

### 2_2 기상청 날씨 데이터
기상청 데이터의 '지점'(날씨를 측정한 곳)이 코드로 나와 있어서 '지점'을 해당 위치로 변환 
또한 결측치(null)값들은 시간 별로 큰 차이가 없을것이라 예상하여 결측치를 근처 시간대의 날씨를 이용하여 채움 (missing value imputation)

### 2_3 최종 데이터
1_1 에서 수집된 해양사고 데이터와 1_2 에서 수집된 기상청 날씨데이터를 합쳐서 하나의 dataframe으로 만들고자 함.
겹치는 칼럼이 없어서 기준을 새우고자 1_1 발생일시와 1_2 일시에서 '발생한 날짜'를 추출하여 칼럼명 'key'로, '발생한 시간'추출하여 '시간대별'로 칼럼명을 정함
또한 지점과 관할해경서를 이용하여 발생지점을 연결 시켜주고자 비슷한 위치를 발생지점으로 취급하기로 함.
최종적으로 'key', '시간대별'. '지점'을 mapping key로 하여 데이터를 합침

## 3. data preprocessing
위에 data_collection_preprocessing 과 data_merge와 동시에 진행함

## 4. EDA 
EDA 파일 참조

## 5. data analysis 
5_1 : 범주형 one_hot encoding을 적용하지 않아서 발생한오류 
5_2 : 오류를 하고 randomforest를 기반으로 모델링 후 분석 --> accuracy가 max 37%로 매우 낮음
행정안전부 국가기록원 기준으로 발생원인을 5가지 calss로 변경후 5-class - classification으로 task를 변경!

## 6. additional_EDA 
task가 변경됨에 따라 추가적인 EDA를 진행 disploy과 countplot을 이용하여 분포 확인
x로 설정한 것에 따라 분포가 다름을 확인할 수 있음





