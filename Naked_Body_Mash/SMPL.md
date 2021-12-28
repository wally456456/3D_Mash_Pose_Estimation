# 논문 제목 : SMPL: A Skinned Multi-Person Linear Model
### 저자명 : Matthew Loper, Naureen Mahmood, Javier Romero, Gerard Pons-Moll,  Michael J. Black

## Prefix
  - 전문 단어 : 
    - rigging : 피사체를 2가지 representation으로 표현한 것(character : mesh or skin)&(bones or rig : hierarchical set of interconnected parts
    - blend shapes : 3D computer animation의 한 방법이며 mesh의 변형된 버전을 vertex position series로 저장하는 것이다 -> 각각의 애니메이션 key frame work가 각각이 자리에 보관법을 이용하여 메꾸는 방식이다

# Abstract
- Skinned vertex based Model, 자연스러운 인간의 자세를 추정한다
- 모델의 파라메터는 rest pose template, blend weights, pose-dependent blend shape, identity-dependent blend shape, regressor from vertices to joint location 들이다.
	- `rest pose template` : 인간의 자세의 default 값
	- `blend weights` : 여러 뼈들과 character의 skin은 대부분 연관이 되어있으며 각각이 scaling factor가 존재하는데 이를 vertex weights 혹은 blend weights라고 한다(한마디로 뼈들과 skin 사이의 연관성의 척도 정도로 생각하면 될 듯) 
	- `pose dependent blend shapes` : 자세에 따라 바뀌는 blend shapes
	- `identity dependent blend shapes` : 객체의 특성에 따라 바뀌는 blend shapes
	- `regressor from vertices to joint location` : vertices(점들)에서 joint(관절)의 위치를 회귀하는 것

- 이전의 모델과는 다르게 저자들은 `pose dependent blend shapes`는 pose rotation matrices의 선형 함수를 사용하여 정의 되었다 -> 많은 사람들의 다양한 pose를 학습할 수 있게 한다

# Introduction
	- LBS(Linear blend skinning) : 하나의 vertices의 이동으로 인해 다른 vertices와 mesh 또한 같이 이동하게 되는데 이를 반영해주는 기법
	- Triangle deformation : 삼각형으로 된 mesh를 변형시키는 것

- 저자들은 blend shapes를 standard 한 skinning(skin을 입혀주는) 작업의 한계점을 고치려고 train 시켜준다
- 개별적인 blend shapes of identity, soft tissue dynamic(움직임에 다이나믹하게 반응하는 세포들)의 고려는 rest template에 blend skinning 작업이 들어가기 전에 적용된다
- 저자들의 중요 approach는 `pose blend shape`를 part rotation matrices들의 선형적 처리를 통해 정의한다는 것이다 -> rotation matrics의 요소(element)들이 제한되어 있기 때문에 변형의 결과 또한 제한 시켜 주기 때문에 일반화가 잘됨 
- 저자들의 목적함수는 per-vertex(vertex 마다) mesh와 model의 차이를 규제함(penalize)으로써 데이터로부터 훈련이 가능하게 한다

- 1786개의 고해상도 3D scan을 사용하여 다른 피사체들의 다양한 포즈들을 학습하였다
- 위에서 언급한 `blend weights` `pose-dependent blend shapes` `mean template shape(rest pose)` `regressor from shape to joint location` 을 최적화하여 error를 최소화하는 방식으로 학습
	- `Joint Regressor`는 joint의 위치를 예측을 통해 body shape를 추측하게 된다, 그리고 `Joint Regressor`는 `pose dependent` 변형을 자연스럽게 해주기 위해 중요하다

- CAEASR 데이터셋에 PCA를 활용한 결과로 남자, 여자모델의 선형모델을 학습시킨다
- 학습 과정
	- template mesh(rest pose를 취한 mesh)를 각 scan과 pose에 맞게 등록을 하고 정규화 시켜준다(vertex-based shape model을 훈련시킬 때 중요한 요소) -> 이것을 PCA진행해준 결과는 body shape blend shape이다

- Ablation으로 Dyna model을 활용한 soft-tissue dynamic성을 높이는 연구를 진행해 주었다
	- 모델의 명은 DMPL
- 비교과정
	- 1.Dyna 모델과의 비교를 위하여 SMPL과 Dyna의 training mesh의 차이를 계산하여 rest pose로 변환을 시켜준 다음 PCA를 적용시켜 차원을 낮추어 주었다(Dyna 모델에서 사용되는 값과 SMPL에 사용되는 값이 다르므로 호환이 맞게 변형을 시켜준 듯)
	- 2. 신체부위의 각도의 속도와 가속도, dynamic deformation의 속도를 통하여 soft-tissue model을 학습시켜준다

# Related Work
- Blend Skinning
	- 사람의 뼈 구조로 mesh를 만드는 방법
	- mesh surface에 있는 각 vertex는 이웃한 뼈들에 weight들을 적용하여 얻은 값으로 추출됨
	- 이전의 method들은 quaternion(x,y,z,w 로 이루어진 vector ->각 성분은 축이나 각도를 의미 X -> 하나의 벡터(x,y,z)와 스칼라(w,roll을 표현) , dual-quaternion skinning, spherical skinning등이 있었는데 이러한 방법론들은 부자연스러운 결과를 도출할 때가 있었다

- Auto-rigging 
	- 피사체를 2가지 representation으로 표현한 것(character : mesh or skin)&(bones or rig : hierarchical set of interconnected parts
	- 기존의 방법론들은 space of body shape의 고려를 해주지 않았다는 것이다

- Blend shapes
	- 3D computer animation의 한 방법이며 mesh의 변형된 버전을 vertex position series로 저장하는 것이다 -> 각각의 애니메이션 key frame work가 각각이 자리에 보관법을 이용하여 메꾸는 방식이다
	- 저자들은 weighted pose-space deformation(WPSD) (rest pose에 맞게 correction을 거친 다음 기존의 skinning equation(LBS)를 적용시켜주는 방식) 와 유사한 방법론을 쓴다
		- 이 아이디어는 corrective shapes(sculpts(조각)) 를 특정 key pose에 맞게 교정을 해주는 것이다 -> 이렇게 영점을 맞추어 놓으면 변형이 일어나도 잘 수행되게 된다(Default값과 exemplar pose와의 거리를 RBF(Radial Basis Function)을 활용하여 구하여 weight를 구해서 반영해줄 수 있기 때문이다)

- Learning pose models
	- PSD(Pose space deformation model)은 등록된 3D scans의 몸통과 팔에 집중을 한다-> key pose들의 변형을 저장해 두었다가 해당 pose가 나오면 interpolation을 진행해준다 -> 나왔던 포즈가 다시 나오면 성능이 좋지만 새로운 포즈에 대한 일반화가 많이 부족하다
	- 이와 같은 현상을 해결하기 위해서 PCA를 이용하여 차원을 낮추는 과정을 거치는 방법론, non-linear skinning method를 활용한 linear approximation 추측 등이 나왔다
	- 저자들이 사용할 방법론은 joints들과 surface의 연관성을 활용하는 것이다 -> 저자들은 deformation gradient들을 활용한 rotation regression approach를 진행해주고 vertex representation으로 변환되는 과정을 사용한다

- Learning pose and shape models
	- 이전의 모델들은 body shape에 대한 공간을 characterize 시키긴 하였지만 pose 에 따른 body shape의 변화를 설명하지는 않았다
	- 저자들이 제안하는 것은 vertex based model이다

# Model formulation
- SMPL 모델은 identity-dependent shape, non-rigid pose-dependent shape로 구성된다 
	- corrective blend shapes(기준이 되는 shape)를 설정하고 시작한다
	- 하나의 blend shape는 N = 6890 vertices 와 K = 23개의 joints들로 구성되어 있다
	- mesh는 두 성별에 같은 배치를 가지고 있고 공간적에 따라 다른 해상도를 가지고 있고 신체부위에 따른 세분화가 되어있고 initial blend weights와 skeletal rig의 정보를 가지고 있다
![plot](https://user-images.githubusercontent.com/69032315/147596865-b6aeda1c-9e1e-4a82-91ab-0296189a2f6d.png)
![plot](https://user-images.githubusercontent.com/69032315/147596870-a679a7a7-fa23-44a8-b470-e7d0ab0cb374.png)

- 다음과 같이 파라메터 정의 
- N(mean template shape) = 병합된 ![plot](https://user-images.githubusercontent.com/69032315/147596941-1bba53c4-dbb1-4153-92dd-15fca6a90abb.png)
 (vertices), ![plot](https://user-images.githubusercontent.com/69032315/147596944-b8a517fd-6d73-467d-9fa2-39369549aba9.png)  in zero pose
- ![plot](https://user-images.githubusercontent.com/69032315/147596997-2881c72c-739e-45da-9f6c-d8472e3246d1.png)![plot](https://user-images.githubusercontent.com/69032315/147597000-f1515419-8bde-4922-9681-628d9ad01725.png) (set of blend weights)

-  ![plot](https://user-images.githubusercontent.com/69032315/147597032-8e2f3501-a622-4331-a13d-520bc8c19a6a.png) = blend shape function,
 	- Input: ![plot](https://user-images.githubusercontent.com/69032315/147597047-de797325-731b-4c26-86d2-16eb1a0bcfee.png)  = vector of shape parameters, 
	- Output : blend shape sculpting the subject identity

-  ![plot](https://user-images.githubusercontent.com/69032315/147597081-242422a2-c38a-4fc1-9c23-d896b62d7c96.png)![plot](https://user-images.githubusercontent.com/69032315/147597087-a07fdeec-37da-4ec3-99de-721efacf09fe.png)  = K joints들의 location을 예측하는 함수
	-   ![plot](https://user-images.githubusercontent.com/69032315/147597106-a0699771-acd5-4ae2-af65-3107fcfdc5d3.png) = shape parameter 함수
- ![plot](https://user-images.githubusercontent.com/69032315/147597144-6cffe80a-4b7d-4be3-8fb5-001d57460054.png)  = pose-dependent blend shape 함수, pose dependent deformation의 효과를 고려하는 함수
  - Input : ![plot](https://user-images.githubusercontent.com/69032315/147597176-cfac3d87-6404-493c-ab23-9c74bf087697.png)
 (vector of pose parameters)
- 위의 (c ) 에서 보이는 것 처럼 corrective blend shapes은 위의 변수들과 함께 rest pose 에 추가되었다 

- ![plot](https://user-images.githubusercontent.com/69032315/147597215-6ae54f01-ed6a-4dab-b25a-880379512e24.png) (linear or dual-quaternion)은 vertices들을 joint 중심들에 rotate시키기 위해서 작용이 된다 with smoothing defined by the blend weights

- 결과 : ![plot](https://user-images.githubusercontent.com/69032315/147597225-108dd496-a283-480d-beb8-e3715a7b3e86.png)  (vertices들의 shape와 pose parameters를 매핑해 줌)

- ※ 앞으로의 함수들은 LBS, DQBS skinning method를 사용한 것이고 보편적으로 skinning method는 black box같이 과정을 알 수 없는 것으로 생각한다

- ※ 저자들의 목적은 ![plot](https://user-images.githubusercontent.com/69032315/147597244-1b0b1c90-928f-40f1-b132-e3635e786c3d.png) 을 학습하여 mesh를 학습하는 것이다
- ※ learned pose blend shapes은 자세의 변화에서 일어나는 blend skinning function 과 static soft-tissue deformation 둘 다의 error 모두 교정한다

- Blend Skinning 
	- 아래에는 LBS version을 설명한다 
	- Mesh = bold capital letters, Blend shapes = lowercase bold letters(특정 vertex의 vector를 represent한다)
	
	- body의 자세는 standard skeletal rig로 ![plot](https://user-images.githubusercontent.com/69032315/147597266-5ca69ada-adf9-4a61-accb-4392c9ce66ac.png)정의된다,  (해당 joint 부위(k)와 해부학적으로 상위 신체 부위간 상대적인 rotation의 axis-angle representation을 뜻한다)
    - axis-angle representation ![plot](https://user-images.githubusercontent.com/69032315/147597285-ac4d8ff9-2cf3-4255-8bbd-2113e66d5503.png)
  - 저자들의 rig는 K=23개의 관절이기 때문에 pose ![plot](https://user-images.githubusercontent.com/69032315/147597314-f49271e0-c370-46f0-8041-98dc6650ff57.png)
 는  ![plot](https://user-images.githubusercontent.com/69032315/147597322-a4f4415f-8bc5-4b42-9142-a07d3f63b8b3.png)개의 파라메터로 정의됩니다(각 파트마다 3개 + root orientation을 표현하는 3)
  - ![plot](https://user-images.githubusercontent.com/69032315/147597360-93140c50-3913-41fa-9f7d-77baf6568a03.png) (unit norm axis of rotation)(length를 나누어 줌으로 rotation을 구하는 것) -> 이후 모든 joint의 axis angle은 Rodrigues formula를 활용하여 rotation matrix로 변환이 된다
    - Rodrigues formula 
![plot](https://user-images.githubusercontent.com/69032315/147597441-ebe89745-706e-4b26-bb07-6d09d634e0e7.png)

![plot](https://user-images.githubusercontent.com/69032315/147597457-f99dfdc2-efd6-4b96-ab3a-d3378db7301c.png)
  - ![plot](https://user-images.githubusercontent.com/69032315/147597473-7ed0db57-5f7f-49d7-8585-25ef0d274139.png) = skew symmetric matrix of 3-vector ![plot](https://user-images.githubusercontent.com/69032315/147597480-1c99b4a3-bd89-4e22-b6cf-27b36ab55e64.png)  ,  ![plot](https://user-images.githubusercontent.com/69032315/147597484-c082fd01-a633-4888-bb7d-8ea5f1f147fc.png) = 3x3 identity matrix
![plot](https://user-images.githubusercontent.com/69032315/147597527-c2a7d6ec-e6b5-4cf5-b5bc-93eda93360a6.png)(반대칭 행렬(skew symmetric matrix))

  - ![plot](https://user-images.githubusercontent.com/69032315/147597567-f920825e-059f-42f8-aa69-34c7fa1588ce.png)에서  ![plot](https://user-images.githubusercontent.com/69032315/147597577-3bea926c-98f2-4227-8a53-ee2ef3fab8ca.png)  = vertices in the rest pose를 의미, J = joint locations,   = pose, W = blend weights를 의미하고 posed vertices를 output
  -  ![plot](https://user-images.githubusercontent.com/69032315/147597609-9e288140-a5ef-4777-8a5f-3f51a60e4422.png)의 하나의 vertex ![plot](https://user-images.githubusercontent.com/69032315/147597611-0d8811e4-3b76-4787-a8c7-856ad9fc7472.png) 는  ![plot](https://user-images.githubusercontent.com/69032315/147597616-7912a133-52b6-4f2c-a23e-bb70caca67b7.png)로 변환된다 다음의 수식으로
![plot](https://user-images.githubusercontent.com/69032315/147597645-57a30a61-8049-450d-a605-ac834bc0842e.png)
  -  ![plot](https://user-images.githubusercontent.com/69032315/147597671-cd18286b-3897-452f-8720-17faf17085ca.png) = blend weight matrix W의 element(신체부위 k의 rotation이 i에 얼마나 영향을 줄 것인가)
  -  ![plot](https://user-images.githubusercontent.com/69032315/147597697-6e4ae587-320f-407a-a5f6-694902771f3f.png)= joint j의 local 3x3 rotation matrix
  -  ![plot](https://user-images.githubusercontent.com/69032315/147597725-b5ccf9e3-1fd1-4844-9113-803adfe71ac4.png) = world transformation of joint k
  -  ![image](https://user-images.githubusercontent.com/69032315/147597755-d8b60786-dc6f-4034-86e0-8c469b676a72.png)=  ![plot](https://user-images.githubusercontent.com/69032315/147597764-3024e2ec-5a02-44e1-be85-974292746710.png)(rest pose)의 영향을 제외한 transformation 
  - J : 각 3element vector of J 는  ![plot](https://user-images.githubusercontent.com/69032315/147597777-8a1a588f-72e0-4762-8611-20af5d1d1010.png) (single joint center j)
  - ![plot](https://user-images.githubusercontent.com/69032315/147597806-ffddd6f8-d8c3-4da0-8ab4-9b45ffbd1f16.png)  = joint k의 정렬된 set of joint ancestors 
	- ※ 계산을 가능하게 하기 위해서 W는 sparse 하고 최대로 4개의 parts를 영향을 주어 vertex에 영향을 줄 수 있다

  - 다른 산업의 method들과 호환성을 높여 주기 위하여 basic skinning function을 유지하는 대신 template을 수정시켜주어 joint location들을 예측하게 된다, 저자들의 모델 ![plot](https://user-images.githubusercontent.com/69032315/147597836-2f15ef43-f3d4-4dee-8744-f41147a5a410.png)  는 다음과 같이 표현이 가능하다

![plot](https://user-images.githubusercontent.com/69032315/147597850-b55bcf4d-3325-40e1-b067-850eb6cf921f.png)

  - ![plot](https://user-images.githubusercontent.com/69032315/147597865-244c2038-3e03-453e-9f62-aa3e37a54318.png)  = template에서부터 갈라지는 것을 표현한 vertices들의 vector -> 저자들은 이것을 shape 와 pose blend shapes로 지정한다
  
  - 다음과 같이 ![plot](https://user-images.githubusercontent.com/69032315/147597939-2f45ec49-35d4-49fa-9a02-454108e85154.png) 를 변환된다
![plot](https://user-images.githubusercontent.com/69032315/147597949-cc63cc8c-b854-4b17-ae0b-c61a4728bdf2.png)
  -  ![plot](https://user-images.githubusercontent.com/69032315/147597959-41776417-e605-46f8-8a90-108757e77ba3.png) = ![plot](https://user-images.githubusercontent.com/69032315/147597973-768a6fd2-1ce3-46c6-8387-f8ad7279b408.png) 의 vertices 이고 vertex  ![plot](https://user-images.githubusercontent.com/69032315/147597992-d65e4b3e-fb26-4f4a-bf14-f34b5d57657a.png)의 shape과 pose blend shape의 offset을 나타내는 것이다, 따라서 joint의 중심들은 이제 body shape의 함수이고 template mesh는 blend skinning으로 변형되어 이제는 pose 와 shape의 함수이다
  
  - 아래에 각 term을 구체적으로 설명한다
	- Blend shapes : 각 사람들의 body shape는 선형 함수인 ![plot](https://user-images.githubusercontent.com/69032315/147598015-fe47b88a-fcfe-4756-82ac-33c4bf73c211.png) 로 표현된다

![plot](https://user-images.githubusercontent.com/69032315/147598044-3c0b7627-461d-4450-a02f-620f3539000d.png)
  - ![plot](https://user-images.githubusercontent.com/69032315/147598094-ee9e0918-2a5b-4674-9232-76d931e983d8.png) , ![plot](https://user-images.githubusercontent.com/69032315/147598105-27f40940-c235-4143-b37f-97f3edde9647.png)는 linear shape의 coefficient 값

  -  ![plot](https://user-images.githubusercontent.com/69032315/147598154-042f36e7-2e4f-4468-a5a6-366d7ea267f2.png) = orthonormal(직교) principal components of shape displacement 
  - ![image](https://user-images.githubusercontent.com/69032315/147598173-64a9b6ca-616f-4f29-8cd8-e0f20893c213.png) = maxtrix of all such shape displacement이면 선형함수인 ![plot](https://user-images.githubusercontent.com/69032315/147598198-8d74dd98-119e-468f-9aad-c9ceb19e8b2f.png)
 는 matrix S로 완전히 정의 가능하다 또한 S는 registered training mesh로부터 학습된 것이다
 
  - semicolon의 오른쪽에 있는 값들은 learned parameter를 의미한다, 왼쪽에 있는 것은 animator가 지정해준 parameter이다
	- notation을 편하게 하기 위해 명시적으로 training 되지 않은 parameter들을 learned되었다고 표현한다
	- 그림 3(b)는  ![plot](https://user-images.githubusercontent.com/69032315/147598242-bca5a538-1883-48f4-bfa3-e97bae43805a.png)에 shape, blend shape를 새로운 body shape를 생성하기위해 적용해준 것을 읨한다

- Pose blend shapes
	-  ![plot](https://user-images.githubusercontent.com/69032315/147598272-a4f49319-b51d-4e29-b8d1-dafa0e01a5b9.png)를 pose vector ![plot](https://user-images.githubusercontent.com/69032315/147598290-92fae679-cdd1-4ea4-8cdc-eb34921dda43.png)를 part relative rotation matrices ![plot](https://user-images.githubusercontent.com/69032315/147598305-b411b0cb-21d7-4e71-9838-d22a32a95a82.png) 의 결합으로 mapping시켜주는 함수이다
	- 저자들의 rig가 23개의 관절을 가지고 있으므로  ![plot](https://user-images.githubusercontent.com/69032315/147598427-d389feea-6ddd-4f2a-94ac-e37cbd0515f8.png)의 vector 길이는 (23x9 = 207)이다
	- ![plot](https://user-images.githubusercontent.com/69032315/147598452-7e0b82b4-e617-4f42-9fa4-dc72e13b3a66.png) 의 성분들은 joint angles의 sin and cosine 값들로 이루어져 있으므로  ![plot](https://user-images.githubusercontent.com/69032315/147598469-5be7e607-286a-497f-85cf-10861a32c4e2.png)는 과 non-linear한 관계이다
	- 저자들의 연구가 이전의 연구들과 다른 점은 pose blend shapes의 영향을 ![plot](https://user-images.githubusercontent.com/69032315/147598504-1fc8355e-3182-407a-8051-dd3ccd35a7be.png)![plot](https://user-images.githubusercontent.com/69032315/147598514-8e189223-1bbd-432d-b281-1cf087f5bc5d.png)  에서 linear하게 만들어 주는 것이다
		- ![plot](https://user-images.githubusercontent.com/69032315/147598527-55dbe1ab-cd2c-467a-abfc-ed2d7037c515.png) = rest pose를 의미한다 
	- ![plot](https://user-images.githubusercontent.com/69032315/147598540-96704dcf-6d99-4950-9b45-e1f33848e671.png)  을 ![plot](https://user-images.githubusercontent.com/69032315/147598554-89e8ce99-58cb-4fcc-a365-09e6f3b91291.png) ![plot](https://user-images.githubusercontent.com/69032315/147598568-86769923-ab0e-4fc3-bdd6-feb946faf2b5.png) 성분이라고 정의하면 rest template의 vertex deviation은 
![plot](https://user-images.githubusercontent.com/69032315/147598592-ff15e592-ecde-4284-a7d0-e31a5d28eca4.png)로 표현할 수 있다
	-  ![plot](https://user-images.githubusercontent.com/69032315/147598615-e1c10161-7113-4971-8fa2-7845a5ad1aa0.png) = blend shapes, vertex displacement의 vector
	-  ![plot](https://user-images.githubusercontent.com/69032315/147598630-cb3b3488-c721-43c8-9821-879ee63148e4.png) = 207개의 모든 pose blend shape
	- 이렇게 하면 pose blend shape 함수인 ![plot](https://user-images.githubusercontent.com/69032315/147598649-9117b18d-c357-43f6-ad36-1481aa7c3720.png)는 matrix P로 완전히 정의 될 수 있다
	- ※ rest pose rotation vector ![plot](https://user-images.githubusercontent.com/69032315/147598662-aac25306-e75c-49f6-b1db-3f282ddbbbea.png) 를 subtract해주는 것은 rest pose에서의 pose blend shape에 대한 기여가 0이라는 것을 주의할 것(animation을 할 때 중요함)

- Joint locations
	- body shape에 따라 joint location도 다르다
	- 각 joint들은 rest pose의 3D location들로 represent된다
	- joint 들을 body shape의 함수 ![plot](https://user-images.githubusercontent.com/69032315/147598689-5d0d5690-0797-4627-8472-9f1ab4e03f14.png) 로 정의한다
![plot](https://user-images.githubusercontent.com/69032315/147598695-892237e1-fb29-4cd4-bed8-2c4049e19ac5.png)
	- J = rest vertices를 rest joints들로 변환해주는 matrix

	- regression matrix J를 많은 다른 사람들과 다양한 포즈들을 활용하여 학습한다

- SMPL model
	- 위와 같은 Equation들을 통해 SMPL model의 parameter들을 ![plot](https://user-images.githubusercontent.com/69032315/147598732-9c7e5caa-56ea-440a-a682-0a8f09bdb314.png)
 로 완전히 준비할 수 있다
	- 학습이 진행되고 나서 바뀐 포즈들은 고정이 되고 새로운 body shapes와 pose들은  와  의 변화로 인해 만들어진다

- 최종적으로 SMPL은 다음과 같이 정의된다

















