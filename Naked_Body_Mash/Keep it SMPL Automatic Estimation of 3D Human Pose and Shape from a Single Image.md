# 논문명 : Keep it SMPL Automatic Estimation of 3D Human Pose and Shape from a Single Image(ECCV 2016)
### (저자명 : Fedrica Bogo, Augjoo Kanazawa, Christoph Lassner, Peter Gehler, Javier Romero, Michael J.Blcak)


# Abstract
- 3D 사람의 자세를 자동으로 구하는 첫번째 연구
- 3D mesh를 2D joints 만으로 추정한다
- 2D에서 3D를 추출하기 위해서는 인간의 신체의 복잡도, Articulation(표현), occlusion(폐색), 옷, 빛, inherent ambiguity 때문에 Task가 힘듦
- 이러한 문제점을 해결하기 위해 CNN-based 모델과 Deepcut을 활용하고 (bottom-up 방식) 2D body joint의 위치를 예측한다
- 다음 (top-down 방식) 최근에 공개된 통계적인 모델(SMPL)을 2D joint에 사용한다
- 목적함수를 최소화해 projected 3D model joint와 detected 2D joints간의 error를 규제한다


# Introduction

- Task 를 2가지 stage로 해결한다
    - Stage 1. 2D joints를 CNN(DeepCut : bounding box annotation이 있는 데이터 셋에 pixelwise object segmentation )을 활용하여 추정한다
    
    
    -Stage 2. 3D pose를 추정하고 2D joints를 활용하여 SMPL 모델로 3D를 만들어낸다

- 이러한 모든 과정을 SMPLify라고 칭하며 기존의 bottom up estimation(CNN) + top down verification(generative model SMPL)

- 저자의 approach는 수천개의 3D scans를 사용하여 높은 질의 3D human body model을 만들었고 사람들 개개인의 다양한 statistics of shape variation과 포즈에 따라 어떻게 변형되는가에 대한 일반화가 잘된다
- SMPL body model은 human body shape에 대한 방대한 양의 정보를 뽑기 때문에 적은 양의 데이터에서도 fitting이 잘되는 것을 알 수 있다

- Generative 3D model의 장점 : interpenetration(상호 간섭)을 설명할 수 있다


![plot](https://user-images.githubusercontent.com/69032315/147279125-0ea2adb8-2fd0-45e5-a634-ab5d35bf1278.png) : interpenetration 예시

- 이전의 연구들은 대부분 3D sticks를 활용하였다 하지만 타당하지 못한 pose를 만들어낼 때도 많았다 -> 그 이유는 loss of depth information이 결과를 모호하게 만들기 때문이다

- 복잡,non-convex, articulated object like body의 interpenetration을 계산하는 것은 비용이 많이든다 -> body shape와 pose에 따라 달라지는 interpenetration term을 사용한다

- SMPL 은 성별에 대한 고려를 한다 -> 저자는 완전 자동화를 위해서 중립적인 Gender도 있다고 생각한다 -> 따라서 성별에 대한 정보가 있다면 구분해서 모델을 활용해주고 없다면 중립적인 Gender라 가정을 한다

- 포즈의 모호성을 해결하기 위해서 좋은 pose prior가 필요하다 

-> SMPL 모델을 활용하여 포즈에 대한 사전확률을 구해준다 

-> 여기서 얻은 요인들은 신체 부위의 상대적인 회전으로 표현되는 포즈와 함께 포즈로부터 형태를 잡는다 

-> 이 요인들을 활용하여 포괄적인 multi-modal pose prior를 학습시킨다

# Related Work

- Shape prior = 뼈의 길이에 anthropometric(인체측정학적) 제약을 한다
- Pose prior = 그럴듯한 포즈만 남기고 불가능한 포즈는 배제하는 것

- 기존에 많이 사용되었던 multi-image들이나 video sequence를 활용하는 방식이 아닌 static한 이미지를 활용한다
- 사진의 배경에 대한 정보가 없어도 된다

- 이전의 대부분의 연구들은 이 문제를 3D skeleton을 찾는 문제로 정의를 했다 왜냐하면 3D joints 를 project 시켜서 2D joints를 잘 찾을 수 있다고 생각하였기 때문 -> 따라서 이전 연구는 skeleton 이 특정 포즈에 있는 것을 ‘shape’로 정의하였다 -> 이 논문의 저자들은 3D 인간의 신체에 pose-invariant surface(자세에도 변하지 않는 표면)을 shape라 지정하고 pose와는 다른 의미로 사용한다 

- 3D pose from 2D joints 
        - 이 방법론은 모두 2D 관절과 3D 골격 사이의 알려진 연관성을 가정한다
        - 이 방법론은 사지 길이의 통계량에 따라 다른 가정을 한다
        - 이 방법론은 weak, non-existent of human shape이다 -> 저자의 방법론은 `3D pose and shape`인데      - 이것은 수천명의 사람들에게서 인체 측정학적 제약들을 추출한다 -> 3D shape를 획득할 수 있으므로`interpenetration`, `impossible poses`를 피할 수 있다

-3D pose and shape
        - 전의 Approach들은 2D joints 들로부터 3D body shape를 추정하는 방법론은 없었다 -> SMPL을 이용하여 가능하게 되었다(SCAPE랑은 다르게 explicit한 3D joints를 가지고 있다)
        - SMPL은 joints들의 위치가 어떻게 3D surface of the body와 관련되어 있는지를 정의한 다음 shape를 joints들로부터 추론하게 하는 것을 가능하게 한다
        - SMPL은 사지의 길이가 정확하게 같지 않아서 완벽하지 않을 수 있다 -> 그러나 SMPL은 해부학적 관절을 나타내는 것이 아니라 표면 정점의 기능으로 관절을 나타낸다 -> 이것은 모델 트레이닝 중에 관절과 모양을 결합시킨다(이 두 요소들을 같이 활용하는 것이 중요하다)

- Making it automatic
        -저자들은 Deepcut method를 통해 정확한 joint position estimation을 자동화 할 수 있었다

# Method

- SMPLify Overview : 단일 이미지를 인풋으로 가짐 -> DeepCut CNN을 활용하여 2D body joints ![plot](https://user-images.githubusercontent.com/69032315/147280634-7934d653-3d24-4ad8-8300-2caac17dcfd0.png)
 를 예측함 -> 각 2D joint i에 CNN은 신뢰도 값 ![plot](https://user-images.githubusercontent.com/69032315/147280658-e08ee7d3-20f4-4ea8-bcea-45f6f9a63d00.png)
를 제공한다 -> 3D body model을 fitting(SMPL) 시켜준다 이때 projected joints of the model은 robust weighted error term을 최소화 시켜준다 


- Body model은 함수  ![plot](https://user-images.githubusercontent.com/69032315/147280708-862177b1-71a6-4d4c-86e2-ba0afb92c658.png)로 정의되어 진다
    - Output의 형식 : triangulated surface   <- 6890개의 vertices 

    - Parameter 정의
    - beta = shape, seta = pose, gamma = translation
        - beta
            - 저차원의 shape space의 상관계수(training set에서 학습된) 
            - male, female gender-neutral에 대한 고려를 해준다 -> pink = gender specific, light blue = gender-neutral
        - Seta
            -  body의 pose는 23개의 joints 들로부터 정의된다
            - Seta는 신체 parts들의 상대적인 rotation의 axis-angle representation을 대표한다
            - SMPL에서는 joints는 표면 vertices들의 sparse linear combination 또는 function of the shape coefficients이다
            - Joint들은 global rigid(엄격한) transformation을 통해 임의적인 포즈에 놓일 수 있다
            
            - ![image](https://user-images.githubusercontent.com/69032315/147281052-6a6197e5-1462-478d-80db-ed1fed942295.png) = posed 3D joints
            - i = Joints,  ![plot](https://user-images.githubusercontent.com/69032315/147281537-43c2342d-3c8b-4267-b203-5e34773072c0.png) = 세타 값에서부터 유도된 global rigid transformation 
SMPL은 pose-dependent 변형을 정의한다

- SMPL 모델과 DeepCut의 skeleton은 조금의 joints에 차이가 있다 

-> Deepcut의 joint들을 가장 유사한 SMPL 조인트와 연관시킨다 

-> SMPL의 joint들을 이미지에 project하기 위해서 저자들은 perspective camera 모델을 사용한다(Denoted as K)

- Approximating Bodies with Capsules 
    - 이전의 `3D pose generating`이 되지 않았던 이유는 인체 부위들의 `interpenetration` 때문이다

    - 저자들의 모델은 이것을 탐지하고 예방할 수 있다  
    - Graphics 분야에서는 proxy geometries(texture가 입혀질 대리 도형)를 활용해서 collisions를 효과적으로 계산해 낸다 -> 저자들도 이 방법을 따르고 body surface를 set of `capsules`로 근사한다   -> 각 `capsule`들은 반지름과 축의 길이(axis length) 정보를 가지고 있다
    

- 모델학습 과정(대략적)


1. Model shape parameter들을 활용하여 Regressor를 학습시켜 위의 `capsule`화 시켜준다


2. `capsule`화 시켜준 값에  ![plot](https://user-images.githubusercontent.com/69032315/147282041-23ef169b-6c26-42cf-8692-e3188fafde11.png)
(rotation induced by the kinematic chain)를 활용하여 rotation시켜준다  

- 모델학습 과정(상세하게)
        - 1. 손가락과 발가락을 제외한 신체의 부위들에 각 부위당 1개씩 20개의 capsules들을 포즈를 취하지 않은 training body shape에 fit시켜준다 -> SMPL에 투입 시키기 위한 값
        - 2. body joints들에 손으로 labeling된 capsule들을 시작으로 radii(반지름) 와 axis lengths에 gradient based optimization을 활용하여 capsules와 body surface둘의 bidirectional distance를 최소화하게 한다
        - 3. body shape coefficient(Beta)로 Linear regressor(ridge regression)을 학습시킨다 
        - 4. regressor가 trained 되면, the procedure is iterated once more, -> `capsules`와 regressor output을 초기화 시키면서


- Objective Function
    - 3D pose and shape를 2D joints들로 fitting시켜주려면 5개의 error terms가 필요하다
1개의 joint-based data term, 3개의 pose priors, 1개의 shape prior


![plot](https://user-images.githubusercontent.com/69032315/147282177-6fa2663b-0b94-462e-b316-8c9176095c87.png)![plot](https://user-images.githubusercontent.com/69032315/147282187-12a7113f-6913-4f89-97d5-83943eef2cde.png)

- K = Camera parameters, ![plot](https://user-images.githubusercontent.com/69032315/147282228-46382171-ff22-4c5e-b373-42753afc5037.png) = scalar weights

- joint-based data term은 ![plot](https://user-images.githubusercontent.com/69032315/147282302-48add9b3-721a-4ec9-8113-7f3f0ff844f1.png)
  (estimated joints) 와  ![plot](https://user-images.githubusercontent.com/69032315/147282323-e33828d0-6239-4983-9443-322807ff13ae.png)
의 weighted 2D 거리를 고려해준다 

![plot](https://user-images.githubusercontent.com/69032315/147282336-3966be1f-587e-442c-b123-6bf4ca79b3bc.png)

- ![plot](https://user-images.githubusercontent.com/69032315/147282359-dbd232d1-fb9d-40cd-bc67-e399ed2b5cb5.png) = camera parameter인 K로부터 유도된 3D to 2D projection

- ![image](https://user-images.githubusercontent.com/69032315/147282476-24f8bcea-5b1e-45dd-8a57-7534e8abef5b.png) = CNN으로부터 얻은 각 joint들의 confidence를 통해 구한 weight -> 사진에서 잘 보이지 않는 경우 이 값은 대체적으로 낮다 -> noisy estimate를 해결하기 위해서 Geman-McClure penalty function을 이용해 주었다

- Pose prior penalizing elbows and knees that ben unnaturally:


![plot](https://user-images.githubusercontent.com/69032315/147282542-0e97d382-ac9f-41ca-a5bf-9317f24ee213.png)
        - i = 무릎과 팔꿈치의 구부러진 것이 일치하는 pose parameter(rotation)들의 합 
위의 Error term은 자연적인 제약을 벗어나는 pose를 취했을 때 강하게 제약을 가해준다
        - ![plot](https://user-images.githubusercontent.com/69032315/147282831-32de706f-3169-4dc3-8b70-75e361de50ea.png)값은 joint가 구부러지지 않았을 때 0이다 
        - Negative bending은 자연스러워서 penalize가 강하게 들어가지 않지만 positive bending은 부자연스러워서 penalized가 강하게 들어간다
        

- Pose prior를 CMU dataset을 이용하여 학습을 진행해주었다
- 포즈들이 다양할 수 있기 때문에 multi-modal(양방향 통신) nature of the data 를 잘 대표하는 것이 중요하다. 그리고 동시에 prior를 계산적으로 tractable하게 만드는 것도 중요하다
- Prior를 구하는 과정
1.SMPL를 CMU dataset에 활용하여 Pose를 얻음

2. Mixture of Gaussian에 1million poses, spanning 100 subjects들을 fitting 시켜준다

3. max 함수를 활용하여 mixture of gaussians의 근사치를 구한다 -> mixture model을 직접적으로 optimization framework에 사용하면 계산적으로 문제가 되기 때문이다

![plot](https://user-images.githubusercontent.com/69032315/147283007-f30dbb54-089a-4f25-a13c-30f2f5c581fc.png)

![plot](https://user-images.githubusercontent.com/69032315/147283034-fff23cb4-f0d1-4abd-ac8d-729c80f054f2.png) = mixture model weights of N = 8 Gaussians

![plot](https://user-images.githubusercontent.com/69032315/147283060-12a3e94e-040d-4e6c-bb0b-a44a7e8d1793.png) 값이 not differentiable 이더라도 minimum energy가 바뀌는 부분에서 Jacobian of the mode with minimum energy를 활용해서 Jacobian을 구해줘서 optimizer step 에 영향을 준다


- Interpenetration error term 
    - ![image](https://user-images.githubusercontent.com/69032315/147283092-d912abd9-5ff7-47d6-8e98-c94ac9a7c7d2.png) = `capsule`들의 교차 지점들을 계산하기 힘들기 때문에 부피가 `capsule`을 sphere(구) with centers로 단순화 시킨 것 

    - Seta = capsule axis, Beta = radius(반지름)
    
    - 각 sphere마다 3D isotropic(등방성) Gaussian  ![plot](https://user-images.githubusercontent.com/69032315/147283209-119a15b6-1360-46b3-8c19-62d3bbbfb4dd.png)
를 고려해준다 그리고 a scaled version of the integral of the products of Gaussians corresponding to incompatible parts 로 penalty를 정의한다    

![plot](https://user-images.githubusercontent.com/69032315/147283243-c0e54b52-ced0-4147-a0df-941ae6b8c1fa.png)
    - i = Spheres
    - ![plot](https://user-images.githubusercontent.com/69032315/147283275-05b44393-9b0b-476e-8849-9ab09b8ab677.png) = spheres incompatible with i
    - 위의 term은 penalize를 하긴 하지만 interpenetration을 strictly avoid하지는 못한다 
    - 위의 term은 shape를 optimizing할 때 사용하지 않는다 -> 이 term이 body shape에 적용되면 body shape가 interpenetration을 피하기위해서 홀쭉 해진다 
    
![plot](https://user-images.githubusercontent.com/69032315/147283348-ab1bdb30-f141-4bdb-abee-35f8ed1258bd.png) = Shape prior

![plot](https://user-images.githubusercontent.com/69032315/147283372-405ca9a3-ef28-4e14-a82f-08787ff9f0e5.png)

![plot](https://user-images.githubusercontent.com/69032315/147283463-e1d0122b-1197-43fa-a936-195ba69d04e3.png) = SMPL training set에서 얻은 shape에 대한 PCA 분석을 하여 얻어낸 값에 squared singular values를 적용한 diagonal matrix 

# Optimization

-저자는 camera translation과 body orientation을 모른다고 가정을 한다.
- Camera의 focal length의 값이나 Camera focal length의 rough estimate는 안다고 가정한다

- Camera translation(equivalently gamma) 를 image의 plane에 평행하다고 가정을 하고 초기화 해준다

- Depth를 Ratio of similar triangles를 통해 예측한다 
    - Ratio of similar triangles -> SMPL의 shape와 예측된 2D joints들의 평균으로 구한 몸통의 length
-> 이 가정이 항상 맞지는 않으니 torso joints에 camera translation과 body orientation에 관한 ![plot](https://user-images.githubusercontent.com/69032315/147283584-a93914df-f159-40c7-bbba-7445d026d80d.png)
 를 최소화 시켜 depth 값을 최대한 정제 시켜준다 
 
- Camera translation을 추정한 후 위에 첫번째 loss term을 최소화 시키는 model에 투입 시켜준다  (실험적으로 해보았을 때  값을 크게 시작하고 점차적으로 줄여 나가는 것이 local minima를 피하는 좋은 방법이다)

- 옆에서 객체가 찍힌 경우 어느 방향으로 body가 향해 있는지를 평가하는 것은 모호하다 -> CNN 모델에서 예측한 2D 어깨의 joints들이 threshold 아래의 값이라면 2가지의 initialization기법을 시도한다
    - 1. 몸을 180도 돌린다 
    - 2.  ![plot](https://user-images.githubusercontent.com/69032315/147283700-4f76078b-9862-405d-9a05-33ebc4b62244.png)
 값이 가장 낮은 angle을 고른다









