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




