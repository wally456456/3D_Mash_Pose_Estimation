- 
	- { (registration specific parameter)}, { , (subject specific parameter)}, {W,P(global specific parameter)}를 번갈아 가며 최적화 시켜준다(한번에 학습X) -> 그 후 행렬 J를 얻는다
	- 위의 과정을 수행시키기 위해서 1개의 data term과 4개의 regularization term이 사용됨
 
	-  , s(j) = j 번째 registration의 index number
	-  = pose training set 의 mesh의 개수,  = pose training set의 subject의 수
	-   = sets of all rest pose , joints
