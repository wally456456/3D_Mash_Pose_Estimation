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



    
    - Output의 형식 : triangulated surface   <- 6890개의 vertices 




