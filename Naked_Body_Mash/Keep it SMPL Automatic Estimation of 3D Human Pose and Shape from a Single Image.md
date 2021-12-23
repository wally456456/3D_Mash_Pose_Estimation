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
Stage 1. 2D joints를 CNN(DeepCut : bounding box annotation이 있는 데이터 셋에 pixelwise object segmentation )을 활용하여 추정한다


Stage 2. 3D pose를 추정하고 2D joints를 활용하여 SMPL 모델로 3D를 만들어낸다


