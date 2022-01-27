논문명 : Pitching Motion Matching Based on Pose Similarity Using Dynamic Time Warping(2020 IEEE – Consumer Electronics)

저자명 : Ryohei Osawa, Takaai Ishikawa, Hiroshi Watanbe

모르는 단어 :

전문 단어 :


# Abstract
- 자세의 유사도를 구하기 위해서는 여러 개의 motion을 매치 시키는 것이 중요하다
- 논문에서는 2개의 pitching 모션을 기반으로 DTW(Dynamic Time Warping) 방법론을 사용해서 유사도를 구해준다


# Introduction
- 여러 개의 비디오 모션의 타이밍을 매칭 시켜주는 것이 굉장히 중요하다
- 논문에서는 OpenPose를 활용하여 투수의 body keypoints를 추측한 다음 포즈의 유사도를 구해주는 방식을 채택하였다
- 두 피칭 모션을 매칭시켜 주기 위해서 Dynamic Time Warping을 사용해 주었다

# Related Work
- Dynamic Time Warping
	- 개념 정리 추천 사이트 : https://syj9700.tistory.com/m/58
	- DTW는 두 Sequence의 각 element들이 무조건 적으로 매칭이 되어야 되는 알고리즘이기 때문에 만약 Noise가 포함되어 있다면 결과를 정확하지 않게 만들 수 있다

- Pitching Motion Matching Using DTW
	- 두 영상을 매칭 시켜 주기 위해서는 두 Sequence의 공통된 `시작`과 `끝` 부분을 설정해주는 것이 중요하다(어느 정도의 유사도 이상을 가지는 값들 만을 매칭시켜 주는 것으로 해서 이 문제를 해결)
	- Open Pose를 사용 후 DTW를 사용하는 것의 2가지 취약점
		- 1. Open Pose의 keypoint가 잘못 검출되면 정확도가 떨어짐
		- 2. 왼손잡이와 오른손잡이의 유사성을 구하는 것이 어려움


# Proposed Method
- A. Vector Data Creation
	- 25개의 keypoint들을 찾는 Open-Pose model을 활용 -> 12개(Neck, shoulders, elbows, hips, middle of the waist, knees, ankles)만 사용(12개의 요소들만 꾸준히 검출되기 때문에 안정성을 위해서)
	- Open-Pose 모델에서 제공하는 Confidence Score를 사용해준다(두 Sequence의 Confidence Score중 작은 값을 채택한다)
	- 각 프레임당 66개의 vector data가 body keypoints data(OpenPose)가 획득된다(각 백터는 length, angle, confidence value등 3개의 요소로 구성된다)

- B. Calculation of Pose Similarity
	- 이 방법론의 목적은 두 Pitching Motion의 타이밍을 matching 시켜주는 것이다
	- 두 프레임의 유사도는 앞서 구해준 66개의 vector를 활용해서 구해진다
	- 왼손잡이와 오른손 잡이를 구분해주기 위해서 다음과 같은 방법론을 사용해 준다
		- 공이 어느 손에서 떠나는가에 따라 두 비디오의 손이 다르면 keypoint vector를 반대의 위치에 있는 값들과 비교를 하여 유사도를 구해준다
	- ※각 vector의 difference(DTW Matrix의 원소들의 의미) = `Angular difference(각도의 차이점)` and `length difference between two vectors(keypoints의 위치의 차이점)의 Product`※
	- 또한 두 Keypoint의 Confidence Score중 하나라도 Threshold보다 다르다면 해당 백터는 Constant값을 할당해준다(Open pose의 부정확성을 억제 시켜주기 위해서)

- C. Matching by Dynamic Time Warping
	- 두 비디오의 프레임을 맞추어 주기 위해서 저자들은 `시작점`과 `끝점`을 자동적으로 찾아주는 DTW 알고리즘을 사용해주었다
	- 두개의 비디오를 비교해줄 때 하나는 `Model Sequence` 다른 하나는 ‘Input Sequence`로 명명해준다
	- `Model Sequence`의 시작점과 끝점은 주관적으로(기준 없이) 정해지고 ‘Input Sequence`의 시작점과 끝점은 `Model Sequence`의 유사도에 따라서 정해진다 -> 즉 `Model Sequence`가 기준점이 되고 `Input Sequence`는 비교점이 된다 -> 즉 `Model Sequence`는 하나의 피칭 모션만을 가지고 있지만 `Input Sequence`의 피칭 모션은 두개가 될 수도 있다
	- 다음은 M elements를 가진 `Model Sequence`와 N elements를 가진 `Input Sequence`의 Frame Matching의 과정이다
		- 1. Matrices D and E of (M+1) x (N+1)을 준비한다
    - ![plot](https://user-images.githubusercontent.com/69032315/151386826-c0978a09-a304-449d-8428-83001bd26d7d.png)
    - 2. 각 Matrices D와 E의 원소들을 구해준다 
			-   ![plot](https://user-images.githubusercontent.com/69032315/151386901-764f357d-2823-40d2-833b-73056bf06aca.png)
			- 위의 값에서 s(m,n)은 m 프레임과 n프레임의 포즈 유사도를 의미한다
			-  ![plot](https://user-images.githubusercontent.com/69032315/151386918-3108127d-5316-4337-8d38-b7e7f1261379.png)
			- 위의 식을 활용하여 D matrix의 값들을 구해준다
			-  ![plot](https://user-images.githubusercontent.com/69032315/151386932-03ce0456-b14b-4461-8e96-0e6b8f463ac3.png)
			-  ![plot](https://user-images.githubusercontent.com/69032315/151386946-86168bd4-26a0-492a-b18f-19b1f709a85b.png) = 옆의 함수 값의 최소값이라는 의미, 즉 D(m,n)은 E(m,n)값 + 1을 할당해준다
		- 3. 마지막으로 Matrix D의 가장 밑의 Row의 최소값을 연결해 나감으로 해서 frame을 매칭시켜준다








