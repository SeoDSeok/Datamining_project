# Datamining_project
***

## 0. 주제 소개
한국해양교통안전공단에 따르면, 해양사고는 계속해서 증가하고 있는 추세이다. 이에 따른 인명피해도 종종 나오고 있는 상황이라고 한다. 이에 과거에 있었던 사고 정보를 통해
어떤 조건하에서 이러한 사고를 조사해야 하는지에 관한 분류기를 만들어 보고자 하였다. 사실 해양사고의 유형을 보면 어느 정도 원인이 예상 가능한 것처럼 보이지만 
여기서는 사고가 난 지점, 선 종, 월별, 시간대별 등등의 기상상태의외에 사고의 원인을 파악해보고자 하였고, 실제로 기상상태의외에 영향을 주고 있는 몇몇 속성들을 발견할 수 있었다. 이에 대한 전체적인 project의 진행순서는 다음과 같다.

해양사고 추이 : <https://www.komsa.or.kr/kor/sub03_040401.do>
***
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
![image](https://github.com/SeoDSeok/Datamining_project/assets/122199258/9c44eb86-fdc9-42c6-8cb3-6e5ddb5157b5)

## 6. additional_EDA 
task가 변경됨에 따라 추가적인 EDA를 진행 disploy과 countplot을 이용하여 분포 확인
x로 설정한 것에 따라 분포가 다름을 확인할 수 있음

*** 이 이후부터는 위에 첨부된 project_data5.csv를 가지고 진행함 ***

## 7. Modeling
여기서는 대표적으로 SVM과 RandomForest를 사용하여 분류를 진행해보았다. 
dataset이 imbalance하여 stratify를 이용하여 dataset분할
train_all : test = 8:2, train : val = 8:2로 진행
7_1 SVM 
알고리즘 자체가 하이퍼파라미터 튜닝과, scaling에 대해 민감하기 때문에 그 과정을 지키면서 수행하고자 하였다.

      1. 전처리 과정에서는 MinMaxScaler를 이용하였는데 이는 범주형 변수가 이미 one-hot encoding으로 되어 있어 [0,1]에 값을 갖는 상황이기에 
      2. 연속형 변수들도 이와 같은 boundary에 있도록 하기 위해 그렇게 진행하였다.
      3. 대표적인 hyperparameter인 C, gamma를 활용하여 튜닝을 진행하였다.
      4. C = 100, gamma = 0.01이 best라 판단하여 이 모델을 가지고 test set에 대한 성능을 측정하였다.
      5. accuracy : 48.4%, f1_score : 34.8%, confusion_matrix는 아래 그림과 같다.
 ![image](https://github.com/SeoDSeok/Datamining_project/assets/122199258/def26d36-ff92-4c85-99a5-7c61fbba3be8)
      
  여기서 label은 각각 '관리문제', '기상상태', '기타', '운항과실', '취급불량 및 결함'순
      
7_2 RandomForest
앙상블 기반의 알고리즘으로 높은 성능을 기대하며 수행

      1. 전처리과정이 특별히 필요없는 알고리즘이라 그대로 (scaling 조정)진행하지 않고 
      2. 대표적인 hyperparameter인 n_estimator로 튜닝 진행  -> n_estimator가 증가할 수록 val_accuracy증가하다가 800에서 1000넘어갈 때 감소
      3. 이에 n_estimator = 800으로 final model를 택하였다.
      4. accuracy : 54.3%, f1_score : 47.79%, confusion_matrix는 아래 그림과 같다.
      
  ![image](https://github.com/SeoDSeok/Datamining_project/assets/122199258/bc6ba6d0-561a-45ed-9a73-ef5ea2515447)
      
   여기서 label은 각각 '관리문제', '기상상태', '기타', '운항과실', '취급불량 및 결함'순

## 8. addtional_modeling
kaggle에서도 높은 성능을 보이고 자주 사용한다는 Light GBM을 가지고 추가적인 실험 진행

      1. randomForest와 같은 이유로 전처리를 진행하지 않음.
      2. num_leaves(하나의 트리가 가질 수 있는 최대 리프 개수), (learning_rate : 부스팅스탭 반복할 때 학습률)를 가지고 하이퍼파라미터 튜닝 진행
      3. learning_rate=0.003, num_leaves=25로 최종 결과 도출
      4. accuracy : 45.3%, f1_score : 19.3%, confusion_matrix는 아래 그림과 같다.
  ![image](https://github.com/SeoDSeok/Datamining_project/assets/122199258/87218c27-f56d-4c3a-ba3a-81137463f86f)
      
   여기서 label은 각각 '관리문제', '기상상태', '기타', '운항과실', '취급불량 및 결함'순
      
## 9. 실험결과에 대한 해석 (Post processing)
9_1 데이터에 대한 설명
최종적으로 택한 task는 5-class classification으로 각 class는 다음과 같다.

            1. 취급불량 및 결함 7038 - 41%
            2. 운항과실 5759 - 34%
            3. 관리문제 2212 - 13#
            4. 기타 1131 - 6%
            5. 기상문제 697 - 4%
            총 16847
데이터는 imbalance 되어 있으며 이것이 전체적인 실험결과에 영향을 준 것으로 보인다.

9_2 모델에 대한 결과
9_2_1 SVM
1. confusion matrix를 삺펴보면 기타라고 판단하는 경우가 거의 없음. 
기타문제가 200여건이 있으나, 이를 기타라고 판단하는 경우가 거의 없었다. 우리가 정한 기타에는 원인미상, 기타로 판단된 부분이므로 
원인미상이 실제로 모델에서도 원인미상으로 판단하는 것으로 해석해 볼 수 있다.
2. 운항과실로 인한 사고를 운항과실, 취급불ㄹ량 및 결함의 사유와 헷갈려하는 경향이 있다.
두가지 calss를 다른 원인으로 예측하는 경우가 현저히 적음을 확인
3. 취급불량 및 결함으로 인한 사고는 그렇게 예측될 확률이 크다.
아무래도 원래 데이터 셋에서 가장 많은 양이 있는 것이 한 몫을 하는 것으로 판단할 수 있다.

9_2_2 RandomForest
1. SVM과 달리 기타로 예측하는 경우가 조금이나마 생겼다.
이것이 정확도를 높이는데 기여한 것으로 보임.
원인미상인 사건에서 어떠한 패턴을 발견할 수 있을 것이라는 기대

2. 운항과실로 또는 취급불량 및 결함이라고 예측한 경우
그 2가지 class에 대한 정확도는 2437/2864 = 85%라는 높은 결과를 보여줌. 
이 역시도 원래 데이터 셋에서 구성비율이 높은 원인이라는 한계

3. 기상문제, 관리문제
feature importance에서 높은 영향을 주는 것으로 판단 되어지는 것이 기상이었는데 기상문제에 대한 정확도는 62%이다.
또한 관리문제에 대한 정확도도 62%로 측정된다.
따라서 분류기의 결과가 기상문제 or 관리문제로 판단한다면, 이에 대한 대비 혹은 주의를 할 필요가 있음을 상기시키는 것이 도움이 될 것

9_2_3 LightGBM
1. SVM과 마찬가지로 기타라고 판단하는 경우가 거의 없다.
랜덤포레스트를 제외하고 기타를 잘 예측하지 못한다.
실제로 원인미상이라고 분류되어지는 것에 대한 합당성을 제시할 수 있을것으로 예상
그러나 모델에 전체적인 성능은 낮아질 수 밖에 없음

2. 기상상태에 대한 예측을 하는 경우도 없다.
데이터에 주요 컬럼이 기상상태임에도 SVM과 랜덤포레스트와는 달리 기상상태에 대한 예상도 하지 않음

3. 관리문제라고 분류기가 판단할 경우
관리문제에 한하여 73%의 정확도를 보이지만 이는 거의 관리문제로 예측하는 경우가 많은 영향도 큼

* LightBGM에서는 f1-score도 낮은 것으로 보아 모델 자체에 대한 신뢰성에 대해 의심해 볼 필요가 존재

## 10. 최종결론

구할 수 있는 데이터에 한계가 있었기에, 분석 측면에서도 한계점이 많이 존재했다. 또한, 해양사고가 일어나지 않은 경우에 대한 데이터가 있었다면 사고가 일어났을 때와
안 일어났을 때에 구분이 되어서 좀 더 나은 분석이 진행될 수 있었을 것이라 판단된다. 그래도 해양사고와 그 원인에 대한 현황을 파악하고, 원인 분석을 시도해 본 것에 의의를 두고 최소한 우리가 진행한 모델링에서는 기상이라고 하는 원인도 사고에 clitical한 영향을 주는지에 대한 의문이 생겼다. (RandomForest에서의 feature importance 기준으로는 물론 높지만 다른 원인에 비해 상대적으로 큰 것! 실제로 기상과 관련된 칼럼만 가지고 실험해보면 오히려 정확도가 낮아짐을 확인함.) 우리가 갖고 있는 데이터에 한해서는 예측이 큰 정확도를 갖지는 못함
향후에 imbalance를 처리하는 기법(undersampling, oversampling)을 적용해 보거나 기상 상태에 대해서 더 정확한 mapping 후에 분석이 필요해 보인다.
또한 ligntBGM에서의 성능이 낮게 나왔는지 더불어 XGBoost, CatBosst등의 boosting 알고리즘도 추가적으로 진행해 볼 필요가 있다.



