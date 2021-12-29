# 논문 제목 : SMPLicit_Topology-aware_generative model for clothed people.md
### 저자명 : Enric Corona, Albert Pumarola, Guillem Alenya, Gerard Pons-Moll, Francesc Moreno-Noguer

# Abstract
- SMPLicit은 body pose, shape, clothing geometry를 관절을 이용해서 생성해주는 모델이다
- 대상이 입고 있는 옷을 교체할 수 있다 
- SMPLicit은 SMPL에 사용되는 파라메터와 의미적으로 해석가능한 clothing attributes의 latent space를 활용하여 flexibility를 구현할 수 있다
- 3D scans를 fitting하는 것 or 3D reconstruction 측면에 있어서 SOTA 모델들을 능가하는 것을 확인할 수 있다


# Introduction
- 구성요소를 대체 가능하고 저차원적 생성모델은 옷의 스타일이나 변형을 다양한 body shape와 pose에서 가능하게 한다
- 이전 연구에서는 옷을 걸치지 않은 인간의 body에는 많은 approach가 있었지만 옷을 입은 인간의 body에는 연구가 없었다
- 기존의 연구들은 옷을 표현하는 방식에 있어서 SMPL 모델에서 얻은 값에서 추가적으로 displacement 를 수행하는 방식을 택했었다 -> 이러한 approach들은 garment(옷)을 모델링하는데 가장 challenging한 요소인 types, styles, cut, deformation의 부분에서 좋은 값을 도출하지 못한다(각 요소마다의 모델들이 필요해서 실용적으로 모델을 활용할 수가 없다)
- 저자들의 SMPLicit은 low-dimensional하고 interpretable한 vector를 parameter를 활용하여 다양한 표현을 가능하게 한다
- SMPLicit은 `body pose` 와 `shape`의 상태를 고려하게 되는데 이 두개의 요인으로 clothing deformation(의상 변형)을 style(cloth category) + cut(e.g. sleeve length)로 통제하는 body geometry 함수로 예측(구축)할 수 있다
- 상의, 하의, 신발, 머리카락 모델을 따로 구축
- 2가지 활용가능 Tasks : 1. 3D scans of dressed people, 3D reconstruction


Related work

- Generative cloth models
	- 기존의 연구들은 옷의 부분을 estimate하는 것에서 성능이 좋지 못하거나 각 clothing의 종류에 맞추어 모델들을 훈련하는 것에서 오는 복잡성 때문에 한계를 가지고 있었다
	- 저자들의 approach는 single low-dimensional parametric 모델을 사용하여 geometric 특성, topology, cut에 대한 특성들을 표현할 수 있게 한다

- Reconstructing clothed humans from images
	- 3D Mesh를 활용하는 모델들은 대부분 SMPL을 활용하게 되는데 SMPL의 output이 3D body mesh만을 반환하기 때문에 cloth를 따로 생성해야 한다
	- cloth를 reconstruct하기 위해서 가장 많이 사용되었던 방법은 3D mesh위에 옷을 그냥 덮는 식으로 해결되어 왔지만 이는 loose한 fit을 가진 옷들 에서는 좋지 않은 성능을 보인다
	- 저자들의 approach는 implicit function을 사용하지만 output은 multiple layers for body and garments를 포함하고 있다 -> pose와 cloth를 모두 control 가능하게 한다


# SMPLicit
![plot](https://user-images.githubusercontent.com/69032315/147692996-2b4330e9-02c0-41df-ab80-916565804f81.png)
- Vertex Based SMPL vs SMPLicit
	- SMPL에서 얻은 특정 ![plot](https://user-images.githubusercontent.com/69032315/147693030-25def392-07df-466f-89de-d92febd1e72e.png) ( human body)에서 시작하여 옷을 생성한다
	  - `Beta = shape`, `Seta = pose`
	- SMPL의 Function
![plot](https://user-images.githubusercontent.com/69032315/147693051-35b0d0fd-70c2-4637-b869-22accfc0d961.png)
 
		- N(vertices)와 V(body mesh)를 pose와 shape로 예측하는 것
	
	- 저자들의 기여점은 vertex model에서 분리시킨 T-pose를 잡은 옷을 예측하는 SMPLicit-core를 만들었다는 것이다. 이 SMPLicit-core의 implicit function은 다음과 같다
 
 ![plot](https://user-images.githubusercontent.com/69032315/147693078-15cf67c6-32be-4a24-9d66-724b2bb2fa88.png)
 
 
	- 저자들은  ![plot](https://user-images.githubusercontent.com/69032315/147693109-3dc994c8-7d76-4bca-b29d-db6f1c3db8d5.png)(given point)에서부터 clothing surface까지의 unsigned distance(주어지지 않은 거리)를 예측한다 -> point들을 충분히 Sampling을 해준 후 distance field와 running Marching Cubes를 thresholding함으로써 원하는 mesh를 reconstruct해줄 수 있다
			- Marching cubes : 3차원 스칼라장의 등고표면에 대한 다각화된 표면표시를 생성하기 위한 알고리즘  
				- 스칼라장 : 수학과 물리학에서, 스칼라장(scalar field)은 (국소) 유클리드 공간의 각 점에 스칼라를 대응시킨 것이다. 예를 들어 3차원 공간 상의 온도 분포나 호수의 수압 분포 또는 공간상의 전위 분포나 위치 에너지 분포 등이 스칼라장에 해당된다.

	- Shape측면에서는 ![plot](https://user-images.githubusercontent.com/69032315/147693161-423bf7b9-e8a8-49c9-a7ae-e5f8966198a3.png) (cut, style)을 의미하는 두 파라메터를 통제하고자 한다
	- 논문의 중요한 부분은 아니지만 pose-dependent deformation를 모델링하기 위한 point-based displacement field도 학습하고 SMPL skinning과 함께 활용해주어 garment가 자세를 취하게 한다
	- 모델의 output은 body위에 있는 posed meshes ![plot](https://user-images.githubusercontent.com/69032315/147693179-cf1f8321-1be1-44a2-a580-7aaf0f4828d8.png) 이다
  
 ![plot](https://user-images.githubusercontent.com/69032315/147693198-496dc1ac-9a87-4247-b3dd-65ab9c844bbc.png)

- SMPLicit-Core Formulation
	- 이 부분에서 `cut과 `style`, `body shape`를 통제할 수 있는 두개의 latent space의 학습과정을 설명한다 -> 두개를 합쳤을 때 이 representation은 옷을 생성하거나 통제할 때 다양한 topology를 하나의 모델에서 가능하게 한다

	- Clothing cut : 저자들은 output clothing cut(예를 들어 옷의 길이나 통의 크기)을 통제를 목표로 한다(여기서 clothing cut을 저자들은 `옷에 가려진 body의 area`로 명명한다)
		- `cut`의 latent space를 학습하기 위해서 garment-body의 구조로 된 pair로 된 training set을 통해 U(UV body occlusion image)를 계산해준다 
			- 각 SMPL body UV map의 pixel값의 body vertex가 옷에 가려져 있으면 값을 1로하고 옷에 가려져 있지 않으면 0으로 한다 -> latent vector![plot](https://user-images.githubusercontent.com/69032315/147693268-286ca54f-a7b9-4f8a-bc4c-a9d3d051dd95.png)
  에 occlusion image를 매핑하기 위해서 image encoder ![plot](https://user-images.githubusercontent.com/69032315/147693280-e28746d6-b0b4-4a2a-a7ba-4c5100b4f856.png)
  를 훈련시킨다 
  - (예시) ![plot](https://user-images.githubusercontent.com/69032315/147693291-e51cd10a-33eb-4148-b3a9-e57c5a923190.png)
	- Clothing style : 옷은 다르지만 같은 U(body occlusion image)인 경우일 때 옷의 
	- tightness, low-frequency wrinkles등 디테일이 달라질 수 있다 -> 따라서 <Deepsdf: Learning continuous signed distance functions for shape representation. > 논문에서 auto encoder과정을 사용한  ![plot](https://user-images.githubusercontent.com/69032315/147693347-b055ca37-790f-47a4-9099-a999ebe372e6.png)
를 추가해 준다 

		-  ![plot](https://user-images.githubusercontent.com/69032315/147693433-99bf3ce1-48a3-47ab-a924-3d3b28fc0bc0.png)파라메터들은 완전히 옷의 cut과 style을 설명할 수 있게 된다

	- Body shape 
		- Body shape에 맞춘 model을 만들기 위해 body에 연관된 points들을 encode 시켜준다 -> 각 옷들에 따라 ground truth model(e.g. torso vertices for upper-body clothes)과 가장 가까운 SMPL vertices를 식별하고 T-pose로 되어있는 body에 분포되어있는 K(vertex clusters) ![plot](https://user-images.githubusercontent.com/69032315/147693449-ba408ded-e430-4ce7-9537-98df3e17e5a1.png)
 를 구한다 -> 공간 ![plot](https://user-images.githubusercontent.com/69032315/147693463-32591f95-07f8-4910-b240-6aa21df49571.png)
 의 3D point를 body relative encoding ![plot](https://user-images.githubusercontent.com/69032315/147693481-a97d9521-2307-431c-aed5-20058c1a875a.png)
 에 매핑시켜준다(rows가 ![plot](https://user-images.githubusercontent.com/69032315/147693489-8b324f61-dd96-4e98-8b45-dbd1ecf70d78.png)
 클러스터에 displacement를 저장한 채로)
			- 이 over-parameterized representation은 network가 body boundaries를 추론할 수 있게하고 경험적으로 유클리디언이나 Barycentric distance보다 좋은 성능을 보이는 것을 확인할 수 있었다

	- Output representation : 3D generative clothing model의 가장 어려운 과정은 training garments를 register(적용인듯) 시켜주는 것이다 
		- 저자들의 garments는 open surface의 형태이기 때문에 unsigned distance field를 예측하는 방식을 택한다
		- p(query point)가 주어졌을 때, p의 positional encoding ![plot](https://user-images.githubusercontent.com/69032315/147693520-8aa11eda-37df-41cf-aa6c-71f0d47fed79.png)
 와 cloth parameter 를 활용해서 decoder network  ![plot](https://user-images.githubusercontent.com/69032315/147693523-e5bd2fa1-0d9e-4bc0-8ce2-2d7371239885.png)
를 학습시키고 p와 ground truth cloth surface와의 unsigned distance ![plot](https://user-images.githubusercontent.com/69032315/147693531-cff8d5d6-bd45-4a47-ad47-78175e503570.png)
 를 예측한다

- SMPLicit-core Training
	-   ![plot](https://user-images.githubusercontent.com/69032315/147693542-b5dd36b2-5474-477e-8e87-0e076ebbcadb.png)
(clothing cut image encoder) network parameter ![plot](https://user-images.githubusercontent.com/69032315/147693551-cdc058db-5814-4a5e-9d2f-a5ddc09cc064.png)
 을 학습시켜 clothing cut과 style latent parameter![plot](https://user-images.githubusercontent.com/69032315/147693597-5c185a46-9c37-49fd-a2c2-56d92e989af6.png)
 를 함께 각 training example과 decoder network ![plot](https://user-images.githubusercontent.com/69032315/147693611-796b1b2f-0b48-4254-8c93-41d26b854306.png)
 에 training 시켜준다
	- 1개의 training example과 p(sample point)는 다음과 같은 loss를 사용한다
 ![plot](https://user-images.githubusercontent.com/69032315/147693615-a31c3b17-bb05-417b-87e9-f05a985d2d51.png)

	- 학습하는 과정에서는 points를 body bounding box에 균일하게 sampling 해주고 ground-truth surface에도 균일하게 points를 sampling 해준다 -> 각 category별로 모델들을 학습시켜준다(대분류(하의,상의)는 구분해주지만 대분류 안에서는 같은 모델을 공유한다)
	- Inference 과정에서는 encoder![plot](https://user-images.githubusercontent.com/69032315/147693642-01d7641e-9fae-4345-b751-d1bfa32a3ccd.png)
  는 제거해주고 ![plot](https://user-images.githubusercontent.com/69032315/147693662-a6d70617-60e7-4973-ae41-fb84e93542a8.png)
 을 direct하게 제어해서 사용한다
	- 새로운 clothing을 smooth하게 보간하고 생성하기 위해서 latent space ![plot](https://user-images.githubusercontent.com/69032315/147693684-346bfa2e-c6c4-41b8-94f0-8d1dd2bf68e4.png)
 를 잘 분포하게 하기 위해서 두번째 loss component ![plot](https://user-images.githubusercontent.com/69032315/147693692-eebfb6cf-cfbb-460b-9e49-e29aded3e32d.png)
 를 사용한다
	- 또한 covariance Gaussian noise ![plot](https://user-images.githubusercontent.com/69032315/147693702-42d4ec2d-aa06-4339-a484-a66171842385.png)
 를 cloth representation에 추가하여 forward pass를 하기 전에 적용해준다(Input을 C(Pβ, z+ zσ 형태로 가지고 온다) -> 작은 데이터의 양을 가진 옷에 많은 도움이 된다
	- Network C와 cloth latent spaces는 앞서 정의 되었던 loss ![plot](https://user-images.githubusercontent.com/69032315/147693708-880cf05c-563f-4a8c-97c6-26cbb4f41fd1.png)
 를 최소화하면서 학습된다 (![plot](https://user-images.githubusercontent.com/69032315/147693717-531f372e-bc94-414f-ae7c-9819e84882af.png)
 는 하이퍼파라메터이다)

- SMPLicit-core Inference
	- 3D garment mesh를 생성하기 위해 저자들은 network C(·)를 T-pose를 한 body 주위의 densely sampled points에서 evaluate해준다 -> Marching Cubes를 활용하여 threshold ![plot](https://user-images.githubusercontent.com/69032315/147693744-1e207684-a71e-4abe-892c-78abedaf8542.png)
 에서 distance field의 iso-surface를 추출한다
	-  ![plot](https://user-images.githubusercontent.com/69032315/147693751-2ac7ecb9-7600-43a8-9e2d-4a9e64347113.png)
= 0.1mm 로 설정해주어 재건된 옷이 자연스럽게 보이게 만들어 준다
	- C(·) 가 unsigned distance와 ![plot](https://user-images.githubusercontent.com/69032315/147693757-5e72b080-3e0e-48a4-be0b-eacd490eca91.png)
 >0 을 예측하기 때문에 재건된 meshes는 ground truth data보다 살짝 큰 크기를 가지고 있다 
	- 요약하자면 β(body shape)에 맞는 clothes를 생성하기 위해서 
		- (1) 훈련 과정에서 얻는 모든 latent components에 single mean and covariance ![plot](https://user-images.githubusercontent.com/69032315/147693774-7b1f3079-15ff-488b-a8e1-dd36a266b2d6.png)
 를 가지는 ![plot](https://user-images.githubusercontent.com/69032315/147693776-0e686ccd-a6a0-4a52-832c-86e290e80989.png)
 를 sampling 해준다
		- (2) positional encoding ![plot](https://user-images.githubusercontent.com/69032315/147693810-994650dd-c5bc-4a96-b486-0b2ff140e6c7.png)
 를 통해서 T-pose 주위에 points들을 추정해준 다음에  ![plot](https://user-images.githubusercontent.com/69032315/147693818-6810e26d-1fb3-4dca-a123-a925c8f22660.png)
를 evaluate 해준다
		- (3) distance field를 thresholding 해준다
		- (4) marching cubes를 실행해서 mesh를 얻어낸다

- Pose dependent Deformation
	- SMPLicit-core는 T-pose로 된 SMPL모델에는 옷을 걸치게 할 수는 있지만 pose에 따라 변화하는 객체에는 입힐 수가 없다
	- 저자들은 T-pose에서의 pose deformation model을 학습한 후 SMPL에서 배운 skinning을 활용하여 mesh를 변형시켜준다 -> 이 과정에서 저자들은 TailorNet dataset의 simulated garment을 사용해주었다
		- 두번째 network를 학습시켜준다 (parameters =  ![plot](https://user-images.githubusercontent.com/69032315/147693833-73a4bd8d-572c-464f-b078-fb0ce297d8d0.png)
 (body pose),  ![plot](https://user-images.githubusercontent.com/69032315/147693837-2e24cd42-b052-47a4-a04a-2b67713d083c.png)
(learnable latent variable(![plot](https://user-images.githubusercontent.com/69032315/147693845-c39fca42-30c7-4b15-ac0a-e597eece267d.png)
 과 같은 auto-encoding 방식으로 학습))
		- 학습과정에서 저자들은 surface의 displacement field를 구하고자 하였기 때문에 T-pose의 cloth surface template의 points들만 샘플링한 model을 evaluate 해주었다)
		- 학습과정에서 input points의 위치 ![plot](https://user-images.githubusercontent.com/69032315/147693867-d32b8e1a-e632-4b54-b205-7bdd2da6c8c7.png)
 로 인코딩 해줌으로 prediction과 ground truth displacement와의 차이를 최소화 하였다
		- Inference 과정에서 저자들은 SMPLicit-core에서 재건된 mesh에 있는 P 만을 evaluate해주었고 ![plot](https://user-images.githubusercontent.com/69032315/147693885-2f2443d4-3912-4dac-8608-395f8fc87b66.png)
 해주어서 변형된 mesh 를 얻는다(아직까지는 T-pose로 되어있다) -> 그 후 Body와 deformed garment를 특정 포즈로 ![plot](https://user-images.githubusercontent.com/69032315/147693899-020eea08-0a0a-4efe-a406-236a13f9890a.png)
 를 활용하여 바꿔준다
		- 각 garment의 vertex를 가장 가까이 있는 SMPL body vertex에 맞게 변형시켜준다  -> 이 과정은 SMPLicit Function ![plot](https://user-images.githubusercontent.com/69032315/147693909-f2a9f30f-6500-49e4-9968-b9a2a16953d9.png)
 에서 결정된다(Eq(3))

# Applications of SMPLicit
- latent space에서 어떻게 garments가 interpolate되는지를 설명한다 -> cut과 style을 조정할 수 있게 된다 
- Generative properties
	- control을 유저들에게 제공하기 위해서 저자들은 Latent space에 PCA를 수행해주어 직관적으로 옷의 특성을 변화할 수 있게 하는 방향을 탐구하였다
	- Latent space ![plot](https://user-images.githubusercontent.com/69032315/147693946-42b9c5e9-df03-43b2-b792-7ed66c232b69.png)
 에 PCA를 수행해주었으며 disentanglement가 잘 되게 하려면 작은 수의 PCA components를 적용하는 것이 좋다(저자들은 4~18개를 사용하였음)

- Fitting SMPLicit on 3D scans of dressed people
	- Sizer dataset의 3D scans 데이터에 cloth segmentation을 포함한 fitting하는 과정을 설명할 것이다 
	- 가장 큰 목적은 SMPLicit-core가 unposed scan points에 0으로 evaluate하는 것이다
	- upper-body, lower-body clothes를 분할한 scan에 3D point들을 sampling해주고 또한 3D empty space에도 points들을 sampling 해준다
	-  ![plot](https://user-images.githubusercontent.com/69032315/147693970-e08d63f5-e187-4ebb-afe3-ef28874dc334.png)
 = points in the posed scan space, ![plot](https://user-images.githubusercontent.com/69032315/147693976-dd3cbd83-9897-415f-9c8a-2e886b58a4f8.png)
  = distance to the scan-> SMPLicit-core가 T-pose에 맞게 정의 되어있기 때문에 저자들은 SMPL parameter들을 활용해서 q를 SMPL 의 파라메터를 활용하여 unpose 시킨 후 body relative encoding ![plot](https://user-images.githubusercontent.com/69032315/147694002-ac1cc35a-c013-4dd3-bf56-b989ddfeb79d.png)
 (shape and pose 함수)을 얻어낸다 
	- 그 후 저자들은 모델 C가 encoding of the unposed point와 같은 거리를 같게 하도록 한다 
 ![plot](https://user-images.githubusercontent.com/69032315/147694009-364d7f09-e841-4137-ad4c-a323c8c352d8.png)

	- 저자들은 또한 scan points 와 SMPL vertices사이의 Chamfer distance, SMPL joints 와 예측된 scan joints 사이의 MSE, SMPL prior loss, regularization term for z를 최소화 하는 방향으로 최적화 시킨다 

- Fitting SMPLicit to images 
	- 과정
		- 사람을 감지하고 각 사람의 pose와 shape, 2D cloth semantic segmentation을 획득한다 -> SMPLicit을 탐지된 모든 데이터를 fitting시켜 3D clothing을 얻어낸다
		- 탐지된 모든 garment에 동일하게 T-posed SMPL 주위의 space를 sample해준다 
		- Sampling된 points를 target SMPL pose ![plot](https://user-images.githubusercontent.com/69032315/147694074-9f69a003-fbe5-49c4-9327-2aba59904086.png)
 로 변형을 시켜주고 사람의 body shape에 가려진 지점들을 제거해준다 
		- 각 포즈를 취한 포인트![plot](https://user-images.githubusercontent.com/69032315/147694089-2edb9550-8653-4bc3-b992-70b519567932.png)
 는 garment class ![plot](https://user-images.githubusercontent.com/69032315/147694094-f8f1a682-ce47-4e9d-8546-433d276075e9.png)
 (cloth), ![plot](https://user-images.githubusercontent.com/69032315/147694105-9f85dce8-4ad5-495d-baf6-65faba89989b.png)
 (background)의 semantic segmentation pixel ![plot](https://user-images.githubusercontent.com/69032315/147694123-eaa26941-4a6a-4576-90cf-49136b4eb18a.png)
 에 맞게 project된다
		- 각 point p에 다음과 같은 loss가 부과된다 
 ![plot](https://user-images.githubusercontent.com/69032315/147694133-bba30e39-0c3f-4b38-9af8-37363a98828a.png)
		- Sp가 0일 경우 model이 distance field에서 최대의 cut-off distance ![plot](https://user-images.githubusercontent.com/69032315/147694160-527b902a-13fa-4880-9798-87a4e70bd9d1.png)
 를 예측하게 한다(point를 surface밖에 가도록 강제 시켜준다) Sp가 1인 경우 반대이다
		- 많은 ![plot](https://user-images.githubusercontent.com/69032315/147694168-09d4e1b0-fb58-4f82-8126-7a3a3f8c29c9.png)
 (points)들이 같은 ![plot](https://user-images.githubusercontent.com/69032315/147694183-5daaa467-6a2c-4867-9d52-ffb69cb4a806.png)
 (픽셀)에 project될 수 있기 때문에 min 함수를 사용하여 estimate하고 있는 garment와 가장 가까운 point만을 받아드리게 한다(이것은 옷이 두껍게 표현되는 것을 방지하여 여러 겹의 cloth를 재건할 수 있게 한다)
		- 저자들은 또한 regularization loss ![plot](https://user-images.githubusercontent.com/69032315/147694203-c17f057d-3a23-4df4-93ce-2f49b3df0d40.png)
 를 추가해주고 L1과 함께 optimize 시켜준다


