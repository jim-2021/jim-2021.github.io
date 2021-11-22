## Neural Network 기본
1. 퍼셉트론
2. 활성함수
3. 손실함수
4. 신경망 구조

## 퍼셉트론 (Perceptron)

- 프랑크 로젠블라트(Frank Rosenblatt)가 1958년 고안한 알고리즘이다.
- 신경망(딥러닝)의 기원이 되는 알고리즘

- 여러 신호를 입력으로 받아 하나의 신호로 종합하여 출력한다. 이 신호를 통해 '흐른다/흐르지 않는다.'(1 또는 0) 라는 정보를 출력으로 전달한다.

<img src = "./image/딥러닝의 이해/IMG_1171.JPG" width="40%">

- 원을 뉴런 또는 노드라고 한다.
- 여러 입력신호에 각각의 고유한 가중치가 곱해진다. 
- 가중치는 각 입력신호가 결과에 주는 영향력을 조절하는 요소로, 가중치가 클수록 해당 신호가 그만큼 더 중요하다는 것을 뜻한다.


- 퍼셉트론은 수학적으로 **내적**과 **sign 함수**로 간단하게 표현할 수 있다.
    - $y = sign(x^Tw)$
    - sign 함수(계단함수)를 사용하기 때문에 결과가 1과 -1로만 나온다. 
    
#### 퍼셉트론의 동작

<img src = "./image/딥러닝의 이해/IMG_1172.JPG" width="70%">

- 위 그림에서 보듯이 입력인 x1과 x2에 대한 직선을 그을 수 있다. 이때 우리가 관심을 가져아 할 미지수(파라미터)는 w(가중치)와 b(편향)이다.
- 데이터가 직선 위에 있으면 1, 아래 있으면 -1로 분류가 가능하기 때문 퍼셉트론을 통해 이진 분류 문제를 풀 수 있다.
- 편향 b
     - '한쪽으로 치우쳐 균형을 깬다'라는 의미를 담고 있다. 입력이 모두 0이어도 결과로 편향값을 출력할 수 있도록 한다. 즉 직선이 원점만 지나는 것을 막아준다.


#### 퍼셉트론의 학습
- 퍼셉트론의 가중치 업데이트 수식
    - $w_{t+1} = w_t + \eta(y - \hat{y})x$


- 전체 알고리즘
```
initialize_w(random) # 랜덤으로 w 지정
for _ in range(max_iter):  # w를 몇 번 수정할 것인지
    for x, y in zip(X, Y):
         h = dot_product(x, w)  # 내적
         y_ = activation_func(h)  # +1, -1로 나눠주는 sign 함수
         w = w + eta * (y - y_) * x  # w(가중치) 없데이트
```
1. 임의로 선을 긋는다.
2. 입력을 하나씩 넣어서 출력을 내본다.
3. 정답과 비교해서 틀린 경우 선을 옮겨 다시 긋는다.

### 논리 회로

#### AND, OR 게이트
- AND : 입력 신호 모두가 1이여야 출력이 1이 되는 논리 회로. 모두가 True여야 True
- OR : 입력 신호 중 하나 이상이 1이면 출력이 1이 되는 논리 회로. 하나만 True여도 True
<img src = "./image/딥러닝의 이해/and or gate 그래프.JPG" width="60%">

- 위 그림을 보면 x, y축은 그대로 있고, 직선을 이동하면서 AND, OR을 구현하고 있다. 
- 직선을 이동하기 위해서는 파라미터(w, b) 값을 조정해야 한다.
- 즉, 퍼셉트론을 학습을 통해서 적절한 파라미터 값을 조정하여 AND, OR을 구현할 수 있다.

#### XOR 게이트
- XOR 게이트는 베타적 논리합 이라는 논리 회로이다.
- 앞서 살펴본 단층 퍼셉트론으로는 XOR 게이트를 구현할 수 없다. 
- 퍼셉트론은 직선으로 나뉜 두 영역을 만드는데, XOR은 직선으로 두 영역을 나눌 수 없기 때문이다.
<img src = "./image/딥러닝의 이해/IMG_1174.JPG" width="60%">

### 다층 퍼셉트론 (Multi-Layer Perceptron : MLP)

직선 한 개로 나눌 수 없다면 직선 2개를 이용하면 된다!

즉 단층 퍼셉트론으로는 XOR을 구현할 수 없지만, 다층 퍼셉트론으로는 XOR을 구현할 수 있다.

<img src = "./image/딥러닝의 이해/IMG_1175.JPG" width="40%">

- 첫번째 계층(input layer) - 두번째 계층(hidden layer) - 세번째 계층(output layer)
- 두번째 계층(hidden layer)을 넣어서 **선을 2개 긋는 효과**를 준다.
- 단층 퍼셉트론은 앞서 봤듯이 hidden layer가 없다.

**퍼셉트론은 가중치(w)와 편향(b)을 파라미터로 한다.**

단층 퍼셉트론은 직선형 영역만 표현할 수 있고, 다층 퍼셉트론은 비선형 영역도 표현할 수 있다.

**학습**이란 적절한 파라미터를 정하는 작업이고, 이 작업을 컴퓨터가 자동으로 하도록 한 것이 딥러닝(신경망 학습)이다. 사람은 퍼셉트론의 구조(모델)를 고민하고 컴퓨터에 학습할 데이터를 넣어준다.