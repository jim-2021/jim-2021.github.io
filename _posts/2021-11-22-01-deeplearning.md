## 딥러닝이란?

기계학습(Machine Learning)의 한 분야인 인공신경망에 기반하여, 많은 양의 데이터를 학습해 뛰어난 성능을 이끌어내는 연구 분야이다.


### 딥러닝으로 할 수 있는 것들

분류, 회귀, 물체 검출(Object Detection), 영상 분할(Image Segmentaion), 영상 초해상도(Image Super Resolution), 강화학습 등 다양한 것들이 있다.


### 딥러닝의 구성 요소

크게 학습 단계와 테스트 단계로 나눠서 살펴보면
- 학습단계(Training Phase)
<img src = "./image/딥러닝의 이해/IMG_1168.JPG" width="60%">

입력과 출력(정답)으로 이루어진 학습 데이터셋을 네트워크 구조에 넣어서 학습을 시킨다.

학습을 통해 나온 출력($\hat{y}$)을 정답($y$)과 비교하여 손실함수(Loss Function)를 구한다. 

손실함수를 통해 학습을 얼마나 못했는지 측정하고 이를 개선하기 위해 알고리즘 최적화 기법을 사용한다. 

최적화 기법으로 수정한 것을 다시 네트워크 구조에 넣어서 학습을 시킨다. 

이를 반복해서 손실함수를 가장 작게 만드는 방향으로 모델을 만든다. 

- 테스트 단계(Test Phase)
<img src = "./image/딥러닝의 이해/IMG_1169.JPG" width="60%">

학습이 완료된 네트워크에 테스트 데이터를 입력해서 평가지표를 통해 모델의 성능을 측정한다.

- 알고리즘 최적화 vs 파라미터 최적화??

## 딥러닝의 역사. 주요 개념. 

딥러닝의 역사를 살펴보면서 딥러닝의 주요 개념들에 대해 간단하게 알아보자.

최초의 인공신경망 개념이 소개되고, 퍼셉트론이 등장했다.
- 최초의 인공신경망 개념 : McCulloch and Pitts, 1943
- Rosenblatt의 퍼셉트론 구조 : Rosenblatt, 1958

### 1. 퍼셉트론(Perceptron)

- 신경 세포를 이진 출력의 단순 논리 게이트로 해석하여 고안한 것이다.
- 입력 - 가중치 - 활성함수 - 출력
<img src = "./image/딥러닝의 이해/IMG_1171.JPG" width="40%">
- 퍼셉트론은 수학적으로 **내적**과 **sign 함수**로 간단하게 표현할 수 있다.
    - $y = sign(x^Tw)$

#### 퍼셉트론의 동작
- x1과 x2에 대한 직선을 그을 수 있다. 이때 우리가 관심을 가져아 할 미지수(파라미터)는 w와 b이다.
<img src = "./image/딥러닝의 이해/IMG_1172.JPG" width="70%">
- 퍼셉트론의 학습
    1. 임의로 선을 긋는다.
    2. 입력을 하나씩 넣어서 출력을 내본다.
    3. 정답과 비교해서 틀린 경우 선을 옮겨 다시 긋는다.
- 퍼셉트론의 가중치 업데이트 수식
    - $w_{t+1} = w_t + \eta(y - \hat{y})x$

#### ADALINE (Adaptive Linear Element)
- How Many? : Rosenblatt의 퍼셉트론
    - 퍼셉트론은 데이터가 단순히 직선의 위에 있는 것이 몇 개, 아래에 있는 것이 몇개인지 그 수를 비교해서 error를 구한다.
    - 이는 가중치 업데이트에 사용하는 활성함수가 계단함수이기 때문이다.(위에 있으면 1, 아래에 있으면 -1)

- How Much? : ADALINE(Adaptive Linear Element) 퍼셉트론
    - 계단함수가 아니라 선형함수를 사용한 것이 **ADALINE(Adaptive Linear Element) 퍼셉트론**이다.
    - 위에 있는 것이 얼마나 위쪽으로 있는지, 아래 있는 것이 얼마나 아래쪽으로 있는지를 비교해서 error를 구한다.

<img src = "./image/딥러닝의 이해/IMG_1173.JPG" width="70%">

#### 그런데 퍼셉트론은 XOR 문제를 못 푼다! $\rightarrow$ 첫번째 딥러닝 암흑기

- XOR 문제
<img src = "./image/딥러닝의 이해/IMG_1174.JPG" width="60%">

- 선 1개로는 분류 불가능 $\rightarrow$ 퍼셉트론 무의미하다!

### 2. 다층 퍼셉트론 (첫번째 Breakthrough)
- 그러면 선을 2개 이용해보자!
- **Linear Classifier를 쌓아서 non-linear classification을 하자! $\Rightarrow$ Multi-Layer Perceptron(MLP, 1986)**
<img src = "./image/딥러닝의 이해/IMG_1175.JPG" width="40%">
- 첫번째 계층(input layer) - 두번째 계층(hidden layer) - 세번째 계층(output layer)
- 두번째 계층(hidden layer)을 넣어서 선을 2개 긋는 효과를 준다.

### 3. 역전파 알고리즘
<img src = "./image/딥러닝의 이해/IMG_1176.JPG" width="40%">
<center> [오류 역전파 알고리즘(Back Propagation Algorithm, BP)] </center>

- 학습이 어려워서 정방향과 **역방향**을 거쳐서 더 잘 학습하도록 만든 것이다.
- MLP와 BP 알고리즘으로 필기숫자 인식(MNIST) 문제를 해결했다.(LeCun, 1989)

### 4. 기울기 소실 문제 $\rightarrow$ 두번째 딥러닝 암흑기
- 역전파 알고리즘은 <u>입력</u>, <u>은닉</u>, <u>출력</u> 계층 이렇게 짧게 있을 때만 괜찮다.
- 계층이 깊어질 수록(은닉계층 多) 학습이 어려운 기울기 소실(Vanishing Gradient) 문제가 발생한다.

### 5. 심층 믿음 신경망 (두번째 Breakthrough)
- RBM(Restricted Boltzmann Machine) 비지도 학습법
- RBM을 쌓아 올린 DBN (Deep Belief Network) (Hinton, 2006)

- 퍼셉트론 수식이랑 동작 부분은 뺄까? 퍼셉트론만 따로 정리하는 부부에 넣을까?

- 따로 빼서 정리하지 않을 거면 그냥 여기에 두고!
