# 논문명 : Joint COCO and Mapillary Workshop at ICCV 2019 : Keypoint Detection Challenge Track Technical Report : HintPose

### 저자명 : Sanghoon Hong, Hunchul Park, Jonghyuk Park, Sukhyun Cho, Heewoong Park

# Abstract

- 대부분의 Top down 포즈 estimation모델들은 bounding box에 한 명의 사람만 있을 것을 가정을 한다 -> 이것이 항상 맞는 것은 아님
- 논문에서 2가지 아이디어를 소개한다
	- 1. instance cue
	- 2. recurrent refinement

# Introduction
- 위와 같은 문제를 해결하기 위한 2가지 기법
	- 1. instance cue 를 input에 추가하여 box안에 있는 target person을 specifiy 해준다
	- 2. Recurrent network를 이용하여 이전 hops에서 얻은 결과값을 활용하여 예측값을 사용하는 것

# Hintpose
- HRNet이 open sourced code에서 SOTA 모델이기 때문에 baseline Architecture로 채택한다 
- 그 후 input refinement(정제) block을 사용하여 모델이 외부 instance cue를 target person에게 활용할 수 있게 한다
- 또한 output of the network과 중간 feature map 층에 feedback connection 만들어 모델이 output을 만드는데 이전 단계의 prediction을 활용할 수 있게 한다
- 두 적용 모두, 저자들은 두개의 Conv Layer with residual connection 2개를 추가하여 ImageNet 데이터셋을 pretrained model을 사용할 수 있었다
![plot](https://user-images.githubusercontent.com/69032315/147301452-4e4472ab-03c2-40c7-b03e-95b06fe3f0a1.png)

- Instance Cue
	- 두 명 혹은 그 이상의 사람이 bounding box안에 있을 경우 target person을 지정해 주기위해서 cropped image와 instance cue embedding이 사용된다
	- Embedding = Single channel Gaussian heatmap(peak located on a target person)
	- Input refinement block : 이미지의 feature와 instance cue embedding을 종합하고 두 featuremap을 element wise summation을 진행해 준다

	- 학습 과정에서는 Instance cue는 ground-truth keypoints나 instance segmentation map 로부터 추출된다
	- Inference 과정에서는 instance cue는 다른 instance segmentation 이나 keypoint estimation model들의 결과를 활용할 수 있다. 또한 다른 simple network을 train 시키는 것도 가능하다 

- Recurrent Refinement 
	- Instance cue가 모델 밖에서의 output을 가지고 하는 것이라면 모델 안에서의 output을 가지고 target person에 대한 hint를 얻을 수 있다
	- 2개의 1x1 conv block을 baseline network에 더해주었다 -> 하나는 이전의 feature map 의 정보를 update하기 위해서 이고 / 하나는 다음 hop을 위해서 의미 있는 정보를 구하기 위해서이다. 
	- connection을 HRNet의 layer1 뒤에 실시하였다 -> 이러면 improved features가 다른 scale의 HRNet에서부터 구할 수 있다 + smaller memory footprint

Experiment
- Training & Eval Detail
- Training 
	- Instance cue를 생성하기 위해서 무작위로 joint를 골랐다 그리고 x,y position을 augmenting해주었다 
	- Recurrent refinement model은 3번의 hops로 eval 된다, 그리고 모든 결과를 ground-truth heatmap과 MSE 값을 계산하여 최소화 시켜준다


