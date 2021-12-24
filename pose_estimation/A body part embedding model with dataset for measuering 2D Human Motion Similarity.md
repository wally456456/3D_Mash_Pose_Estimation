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

![plot](https://user-images.githubusercontent.com/69032315/147301597-c60cffd8-2dfd-4d35-9bcc-c0332f417e92.png)
- 인간의 motion animation을 movement와 angle의 관점에서 collect해주었다
- 논문에서 제시하는 loss 는 두개의 모션의 embedding의 distance를 비율적 구해주게 해주는 것이다

- 전체적인 저자의 goal은 disentangled(구분되는) motion 임베딩을 skeleton과 camera view에서 얻는 것이다 (다른 action recognition에서 사용되는 motion similarity learning과는 차별점을 둔다)
- 5개의 신체부위로부터 얻게 되는 motion embedding과 loss function은 motion 유사도를 robust analysis를 할 수 있게 한다


# Related Work
- Motion similarity
	- 저자들은 motion similarity를 두개의 motion sequence를 직접적으로 비교하면서 구한다
	- hard negative mining required for similarity learning with Maximum Mean Discrepancy를 모델의 성능을 측정하는데 사용한다
- Datasets for measuring similarity
	- 저자들은 motion similarity annotation을 가진 20K 개의 video pair를 제시한다

- Triplet and Quadrapulet Losses
	- Triplet loss : 3개의 image를 input으로 넣는다
		- image of a person(anchor), another image of same person(positive sample), another image of a different person(negative sample)로 구성된 input image
		- loss는 anchor-positive feature에 대한 거리는 최소화하면서 anchor-negetive sample과는 거리를 최대화하게 한다
		- 다른 기존 연구들은 inter-class의 구분을 집중한다면 저자들은 latent space내의 intra-class samples들의 거리를 ground-truth motion variation labels를 활용하여 mapping하려고 한다

- Dynamic Time Warping
	- 다른 length를 가진 두 time series의 optimal alignment를 결정해주는 알고리즘이다

# Method
- motion 유사도를 평가하기 위한 unique한 motion embedding을 encode해주는 학습 기반 method를 제시한다
- 신체 전체를 활용하기 보다는 각 신체부위를 활용해서 손과 발의 움직임을 식별해 냈다
- robust calculate motion similarity를 구할 수 있는 Motion variation loss를 제시한다

- Body Part Embedding Model
	- Network Structure 	
		- ![plot](https://user-images.githubusercontent.com/69032315/147301632-8c6f28ec-eed7-4989-8f06-241e4381265c.png) M = motion, S = skeleton, C =camera view 
		- ![plot](https://user-images.githubusercontent.com/69032315/147301670-4649319e-6ebc-419f-9272-7bcb63c968af.png)![plot](https://user-images.githubusercontent.com/69032315/147301676-b1fd4472-465f-4cc8-8dc8-93a47c0000a8.png)
		- m,m’’ = same motion class but different characteristic(motion variation)
		- m’ = a motion from different class
		- s,s’ = two skeletons with different body structures
		- c,c’ = view angels when 3D motion is projected to 2D
		- M,S,C의 각 element를 고르고 combine해가지고 새로운 모션 sequence를 생성할 수 있다
	- ![plot](https://user-images.githubusercontent.com/69032315/147301697-8a83baa3-85a3-430d-ad91-e164c2c8b58b.png)= set of 2D coordinate sequences
	- J = numbers of joints of skeleton, T = time length of motion sequence
	- ![plot](https://user-images.githubusercontent.com/69032315/147301718-1315eaf1-4bc7-4772-9e86-fa226cbee082.png)= sequences of 2D joint coordinates representing same motion m with the different skeleton s and s’ at the same view angle c
	- ![plot](https://user-images.githubusercontent.com/69032315/147301739-17490aeb-cb59-4a28-a2d0-2d8ab8c5eedb.png)
	- ![plot](https://user-images.githubusercontent.com/69032315/147301750-1bf71ffa-7ee9-4efa-9c62-c5c695cc0657.png)is Decomposed into![plot](https://user-images.githubusercontent.com/69032315/147301771-4f252169-d8cd-4584-b641-2d62c20fefb0.png)![plot](https://user-images.githubusercontent.com/69032315/147301777-243b92c0-7ad2-42e8-b47f-ba5571d7f0a4.png) b = joint의 element
	- ![plot](https://user-images.githubusercontent.com/69032315/147301806-31295bf4-f387-427c-92f6-33f34f1c750f.png)가 body part motion encoder  ![plot](https://user-images.githubusercontent.com/69032315/147301819-0a12006f-17f1-47b1-8429-5442da9e5d77.png)
 과 skeleton encoder  ![plot](https://user-images.githubusercontent.com/69032315/147301824-9d9f5111-da25-45d9-8ac5-6aed116ba018.png)
에 투입되어 embedding을 만들어낸다
	- Camera view encoder ![plot](https://user-images.githubusercontent.com/69032315/147301838-0c51521f-f02e-4c10-b3d7-92454feb9da5.png)
 <- decomposed motion sequences들이 concat되어 input으로 들어가게 된다
	-   ![plot](https://user-images.githubusercontent.com/69032315/147301851-b37bfb44-132a-492b-9f31-a4ebdc37ad4e.png)![plot](https://user-images.githubusercontent.com/69032315/147301855-82ed9fb1-846f-4815-b555-c797cef68553.png), ![plot](https://user-images.githubusercontent.com/69032315/147301861-07208b40-30c3-4283-835a-489f17d85deb.png) =신체부위를 만들어내는 number of joints 

	- 과정 : 두개의 인코더(motion encoder, skeleton encoder)에서 각각이 body part b 의 모션과 skeleton feature를 추출하고 이를 Camera view encoder에 결합시킨 후 body part decoder ![plot](https://user-images.githubusercontent.com/69032315/147301893-90e9327f-6567-4997-8c4d-5e9e8cb674e7.png) 를 통해서 body part motion sequence를 재건해낸다. 

	- 5개의 신체부위를 사용하기 때문에 각 영역들은 5개의 module들을 가지고 있고 서로 공유하지 않는다 

![plot](https://user-images.githubusercontent.com/69032315/147301908-a699ed39-36c8-4b53-b36b-1c8653379b35.png)









