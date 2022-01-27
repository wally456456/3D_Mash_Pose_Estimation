# 논문명 : 동영상에서 추출한 키포인트 정보의 동적 시간워핑(DTW)을 이용한 인체 동작 유사도의 정량화 기법

# 저자명 : 임준석, 김진헌

# Introduction
- OpenPose를 이용한 값에 DTW를 이용해 준다
- 14개의 주요부위를 사용하였다

# Method
- 1. 백터 형시의 동작 데이터 생성
	- OpenPose를 이용하여 관절에 대한 추정을 해주었다
	- 관절의 번호는 다음 사진과 같이 할당되었다
	-  ![plot](https://user-images.githubusercontent.com/69032315/151387198-7b705519-59df-432b-a265-7a1c77309245.png)
	- 두개의 동작 기준으로 총 28(2x14)개의 벡터가 생성된다

- 2, 백터 비교를 이용한 스코어와 신뢰도 정량화
	- 두 동작의 유사한 정도의 score, score를 신뢰할 수 있는 정도의 신뢰도 두가지를 표현해준다(0~1의 범위, %로 표현해준다) <- 두 벡터간 각도 차이를 score로 정량화 하기 위해서 코사인 유사도와 유클리드 거리 공식이 사용된다

	- 비교를 진행할 때 동일 부위에 대한 두 벡터간 각도 차이를 정량화하기 위하여 코사인 유사도와 유클리드 거리 공식을 사용하였다
	- 유클리드 거리를 계산하기 전 벡터의 크기를 통일하기 위해서 L2 정규화를 진행해줌
	-  ![plot](https://user-images.githubusercontent.com/69032315/151387297-1e911da7-5177-470a-a50f-b0191fa0f668.png)(L2 Norm)
	- 코사인 유사도의 경우 두 부위에 대한 각도의 차이가 작을수록 1에 가까워지고 차이가 클수록 -1에 수렴
	-  ![plot](https://user-images.githubusercontent.com/69032315/151387387-02d0b2d5-a9ad-4cb5-872b-8c94a07da4fd.png)(6 = 코사인 유사도, 7 = 유클리드)
		- 시범자와 참가자의 동작 데이터는 각각 P1, P2, 각 i번째 부위에 대한 백터 =  ![plot](https://user-images.githubusercontent.com/69032315/151387414-05cabaa9-b428-427b-adb4-c9444efe279a.png)
	- P1,P2의 i번째 부위에 대한 스코어 ![plot](https://user-images.githubusercontent.com/69032315/151387437-4f1e48c5-0748-46c2-9b67-d94e68959376.png) 는 다음과 같은 식으로 정의가능 
	-  ![plot](https://user-images.githubusercontent.com/69032315/151387458-5aa026f0-3e91-4cbc-9674-c3651a87def0.png)(i번째 부위에 대한 스코어)
	- 최종 스코어 = 추정에 필요한 모든 부위 스코어를 평균 낸 것
	- ![plot](https://user-images.githubusercontent.com/69032315/151387470-7b07be59-3051-4e28-a46a-f8061fc00a5a.png) (최종스코어)

- 3. DTW를 이용한 동작 지연시간 측정
	- i 번째 부위에 대한 값들을 두 시퀀스 사이의 값들을 비교하며 DTW를 사용해 주었다









