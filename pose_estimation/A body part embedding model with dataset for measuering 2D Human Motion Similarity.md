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

- Losses
	- ![plot](https://user-images.githubusercontent.com/69032315/147312724-673db9dd-fe7f-451d-ab4a-742dfdc89fc5.png) = ![plot](https://user-images.githubusercontent.com/69032315/147312750-8ef71b62-ebab-4560-8437-7b5f98a21ee9.png) 에서 얻은 ![plot](https://user-images.githubusercontent.com/69032315/147312767-42296f09-9cc5-44d4-98a5-3020c436ecda.png)의 motion embedding result
	- 이렇게 한다면 triplet loss 는 다음과 같이 정의된다
![plot](https://user-images.githubusercontent.com/69032315/147312809-e75d5908-866e-42dc-a01a-a95b1678448f.png)
	- d(…) = distance metric,   ![plot](https://user-images.githubusercontent.com/69032315/147312837-352b8658-99d4-4fb9-b9b1-37704198d319.png)
= ![plot](https://user-images.githubusercontent.com/69032315/147312849-2d4a6bb4-2d6c-49b8-afe1-7807c7e6d88b.png)
과  ![plot](https://user-images.githubusercontent.com/69032315/147312864-f6e9d14a-bae9-4954-833e-1492203d0111.png)의 margin(차이) 
	- ![plot](https://user-images.githubusercontent.com/69032315/147312893-4e732a24-8359-451e-9841-2ab536ae5242.png) = hinge function(두 class 사이의margin을 최대로 하게하는 function)
	- Triplet loss는 reference 와 positive sample의 거리를 가깝게 reference와 negative sample은 멀리 배치하게 하지만 어떻게 ref 와 pos가 유사한지에 대한 정보를 가지고 있지는 않다	
	- 이 한계점을 극복하기 위해서 motion variation score를 loss term에 고려하여 같은 category의 행동의 sample들에게 적용시켜 주었다
![plot](https://user-images.githubusercontent.com/69032315/147312930-247d55b9-101e-4b10-95a4-d6a7615514ae.png)
![plot](https://user-images.githubusercontent.com/69032315/147312935-e84eb3f5-f4ba-4630-92e1-5e38a1df611d.png)
	- motion variation loss는 positive, semi-positive sample를 motion variation에서 정의해준 일정 distance project(투영)시켜준다 -> skeleton을 움직이게 할 수 있는 Energy, Distance, Height 와 같은 variable이 존재한다고 가정한다

	- Fig 6에서  ![plot](https://user-images.githubusercontent.com/69032315/147313002-d5973d69-d227-45af-a4f4-80900c05d2a7.png)
을 각 요소가 각 variable들 간에 motion m 에 해당하는 특성적 벡터라고 정의한다.
	- m과 m’’가 같은 motion class에 속하기 때문에 ![plot](https://user-images.githubusercontent.com/69032315/147313012-d56683f9-0eb9-4925-b0c8-32c19efd66e5.png)
 은 같은  ![plot](https://user-images.githubusercontent.com/69032315/147313017-873d261a-1623-40e0-bc9b-03f49148c740.png)개의 variable 수를 가지고 있다
 	- ![plot](https://user-images.githubusercontent.com/69032315/147313054-aedc5b90-e52a-4000-932c-1d378873ee2f.png)between m과 m’’ 은 다음과 같이 정의된다
	![plot](https://user-images.githubusercontent.com/69032315/147313070-19284e37-983f-4e9f-b50a-5e2c58c1a8b7.png)

	- Motion variation loss 인 ![plot](https://user-images.githubusercontent.com/69032315/147313103-a6c1c6ad-160d-483e-b20a-70c548125802.png) 은 다음과 같이 정의된다
![plot](https://user-images.githubusercontent.com/69032315/147313144-b35e6372-8646-409c-9e2f-80c60f6b21c2.png)
	- d(…) = distance metric, 하이퍼파라메터 Alpha, Beta = 1, 0.1
	- 이러한 loss를 설정함으로써 positive and semi-positive samples 사이의 motion embedding vectors 가 characteristic vector에 의존적인 관계를 형성하게 만든다

	- (1)을 구했던 것 처럼  ![plot](https://user-images.githubusercontent.com/69032315/147313178-87b3adf0-9ac3-4df7-8c94-d3f9f6efaef0.png)
 도 구할 수 있다
	- 최종 similarity loss term :
	![plot](https://user-images.githubusercontent.com/69032315/147313190-bb83feb7-3fa5-4133-aad7-f8b2461bc4d5.png)
	- ![plot](https://user-images.githubusercontent.com/69032315/147313204-523b64bc-583b-484f-86e8-1d41e106975d.png)(![plot](https://user-images.githubusercontent.com/69032315/147313223-6222de41-bcff-48e8-bc49-b15a017d97ab.png)
 의 output)은 motion, skeleton, camera view의 concat된 embedding vector를  ![plot](https://user-images.githubusercontent.com/69032315/147313238-c2577a35-83f2-4da7-8466-5eac7bf7630c.png)
에 제공함으로써 얻을 수 있다

### Summary of measuring motion similarity
![plot](https://user-images.githubusercontent.com/69032315/147313244-d9f6d5d2-1567-41fe-a6eb-7a256f0fdeba.png)








