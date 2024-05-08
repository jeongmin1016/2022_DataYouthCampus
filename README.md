# 2022_DataYouthCampus
[프로젝트] 해충 판독에 대한 영상 처리 모델과 농약 추천 서비스 구현 : Few-shot, Transfer-Learning, Domain-Adaptation

# 해충 판독에 대한 영상 처리 모델과 농약 추천 서비스 구현
<img width="706" alt="main" src="https://user-images.githubusercontent.com/101931446/186851768-403404ce-58e5-4457-a0e7-6d31b49fd5d0.png">
2022 데이터청년캠퍼스 고려대학교 4조 프로젝트

## OUR PROJECT 
- 주제 : 청년 귀농인과 주말 농장 운영자를 위한 해충 판독 영상 처리 시스템 및 농약 추천 서비스 구현

## 사용 STACK
- Model : [Few-Shot Learning](https://github.com/DYC4/farm-protector/blob/main/models/few-shot.ipynb), [Transfer Learning](https://github.com/DYC4/farm-protector/blob/main/models/transfer-learning.ipynb), [Domain Adaptation](https://github.com/DYC4/farm-protector/blob/main/models/DA.ipynb)
- SERVER : AWS
- Back-end : Flask, Python, MySQL
- Front-end : HTML, CSS, Java Script

## FILE 

- [insects-20220826T061102Z-001](https://github.com/DYC4/farm-protector/tree/main/insect/insects-20220826T061102Z-001/insects) TL 사전학습을 위한 해충 데이터 파일 
- [models](https://github.com/DYC4/farm-protector/tree/main/models) DA, Few shot, TL 모델
- [pest](https://github.com/DYC4/farm-protector/tree/main/pest) DA, Few shot, TL 모델 예측을 위한 해충 데이터 파일
- [pt](https://github.com/DYC4/farm-protector/tree/main/pt) insect와 pest로 학습 완료된 TL 모델
- [static](https://github.com/DYC4/farm-protector/tree/main/static) CSS, IMG, JAVASCRIPT 정적파일
- [templates](https://github.com/DYC4/farm-protector/tree/main/templates) HTML 파일

## OUR SERVICE
### 분석 배경

![image](https://github.com/jeongmin1016/2022_DataYouthCampus/assets/109460178/8c229dad-9bf3-4377-978c-9d7d596cc58f)


첫번째로 병해충으로 인한 농부의 피해가 증가하고 있습니다. 5년 사이 병해충 피해로
인한 농부의 피해면적이 7.6배 증가하였고, 2020년 농가 피해액은 약 342억원에 달했습니
다. 엎친 데 덮친 격으로, 기상이상, 농업환경 변화, 작물 재배양식 다양화로 돌발해충들이 등장하면서 심각성은 더욱 커지고 있습니다. 여기서 돌발해충이란 시기나 장소에 한정되지 않고 돌발적으로 발생하여 농작물이나 일부 산림에 피해를 주는 토착 또는 외래해충을 말합니다. 
<br/><br/>
이러한 심각성에도 불구하고 우리나라에는 병해충 전문가가 부족한 상황입니다. 경상대학교 식물의학과 교수님께서는 농기원이나 농기센터에 관련 전문가가 거의 없는 상황을 지적하기도 했습니다.
전문가를 고용하지 않고 검색을 통해 해충에 대해 알아보려고 하니, 검색 결과 사과 관련 해충이 수십개입니다. 이런 많은 종류의 해충을 일일이 확인하고 대조하여 찾기에는 시간과 노력이 많이 필요해보입니다.

### 데이터 수집 및 전처리
<img width="863" alt="활용데이터" src="https://user-images.githubusercontent.com/101931446/186854010-32117259-bd01-4184-bf78-9538ae5fce21.png">

### 모델링
'적은 데이터'에도 준수한 성능을 낼 수 있는 모델이 필요합니다. <br/>

**Transfer Learning** 
- 1'st Resnet50
  - 딱정벌레, 바퀴벌레, 잠자리 3개 클래스로 구성된 데이터로 재학습(train data : 839 / test data : 180)
  - 마지막 layer를 제외한 나머지 모델 freezing하여 featrue 정보 온전히 유지하여 중요 feature 효과적 학습
  - 1차 학습 결과 : 약 70%
- 2'nd Resnet50
  - Data Augmentation, 노이즈 추가, 흑백전환, 좌우 반전 등을 통해 데이터 크기 6배로 늘려 사용
  - Freezing 해제해 해충데이터셋 학습 및 예측 진행
  - 2차 학습 결과 : 약 60%

        
<br/>                
**Domain Adaptation**                 
- source domain은 train dataset을 target domain은 test dataset을 학습
- feature extractor layer는 4개 convolution층으로 모델 설계
- GRL lambdaf를 domain classifier에 곱해 feature extractor weigh을 업데이트하도록 설계
- 학습 결과 : 약 47.5%       

          
<br/>          
**Few Shot**               
- 거리 기반 학습 채택
- resnet50의 pretrain된 convolution network를 기반으로 prototype network 사용해 모델 설계
- support set : query set = 7 : 3
  'support > query' 성립해야 성능이 좋다는 연구 결과 참고
- 학습 결과 : 38.50%

  
### 서비스 소개 및 구성도
<img width="863" alt="시스템구성도" src="https://user-images.githubusercontent.com/101931446/186854765-a2291a1a-fbe9-4d63-bdd3-e85956291f7a.png">
<br/>
<img width="863" alt="서비스 소개" src="https://user-images.githubusercontent.com/101931446/186854282-2b338c83-3cb5-44d6-873d-e52a152988db.png">

"우리의 서비스"란에 사용자가 직접 입력하여 사진과 정보를 넣습니다. 이때 작물 이름을 정확히 확인하고 싶다면 리스트를 확인할 수 있습니다. 가나다 순서대로 작물이름이 정렬되어 있는 리스트이며 사용자가 편리하게 찾을 수 있습니다. 마지막으로 결과지에서 해당 해충에 대한 상위 5개의 농약과 함께 확인이 가능합니다.

