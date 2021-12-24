# 논문제목 : A Body Part Embedding Model with datasets for measuring 2D Human Motion Similarity

### 저자 : JONGHYUK PARK, SUKHYUN CHO, DONGWOO KIM,  OLEKSANDR BAILO, HEEWOONG  PARK , SANGHOON  HONG, JONGHUN  PARK

# Abstract
- 큰 데이터 셋이 없었기 때문에 모션의 유사도를 측정하는 것은 다른 컴퓨터 비전 task에 비해 덜 주의를 끌었다
- 이 문제를 해결하기 위해 2가지 dataset을 제시한다
	- 1. A synthetic motion dataset for model training 
	- 2. 동작 유사도 Dataset containing human annotations of real-world video clip pair
- 동작 유사도를 얻기 위해서 인간의 다른 신체부위들을 측정해 motion embedding을 만들어내는 딥러닝 모델을 제시한다
- 제시된 motion variation loss를 통해서 작은 motion의 차이도 잡아낸다
- motion 유사도의 다른 approach 들과 human annotation task 에서 다른 방법론들에 비해 높은 성능을 발휘하고 Real-time action analysis가 가능하다 

# Introduction
- 인체의 관절은 많은 정보를 담고있다 
- human motion을 분석하는 것은 중요하지만 다음과 같은 이유로 주목을 받지 못했다
	- 1. motion의 유사도를 구하는 것은 어렵다 Why? -> 다양한 camera view에 대한 human body structure는 다양한 2D joint coordinate를 만든다. 심지어 비슷한 motion 영상에서도
	- 2. motion 유사도를 학습할 수 있는 dataset이 적다 
	- 3. 다른 모션 유사도를 측정할 수 있는 dataset이 적다 
- 이 연구는 인간의 basic motions이 담겨있는 영상을 비교한다
- body size와 appearance를 고려하지 않는다
- motion을 sequence of joint position이라고 생각하고 인간에 상호작용하거나 환경에 영향을 받는 것을 배제한다
- 인간의 motion을 5개의 신체부위로 설정을 하고 latent space에 mapping한다
- 유사도는 encoding된 motion vector들을 비교하는 것으로 측정된다
- 제안된 모델은 종합적인 motion dataset인 Adobe Maximo를 확장한 버전을 사용하였다







