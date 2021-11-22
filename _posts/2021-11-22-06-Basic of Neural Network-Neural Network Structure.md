## 신경망 구조 (Neural Network Architecture)

Neural Network(신경망)는 다양한 구조(Architecture)를 가질 수 있다. 기초적인 것들을 알아보도록 하자.

<img src = "./image/신경망 구조/IMG_1198.JPG" width="75%">


### 뉴런 : Neural Network의 기본 구조
<img src = "./image/신경망 구조/IMG_1200.JPG" width="60%">

- Neural Network는 뉴런을 기본 단위로 하고, 이를 조합해서 복잡한 구조를 이룬다.

- Neural Network를 표현할 때, Node와 Edge를 사용한다.
    - Node : 단일 뉴런 연산
    - Edge : 뉴런의 연결성
<img src = "./image/신경망 구조/IMG_1199.JPG" width="70%">

### Fully-Connected Layer (전결합 계층)

- 두 계층 간의 모든 뉴런이 연결되어 있는 계층이다.
- Neural Network의 가장 기본이 되는 표현이다.
- = Dense Layer
<img src = "./image/신경망 구조/IMG_1203.JPG" width="40%">

### Shallow Neural Network (얕은 신경망)

- 입력계층(input layer), 은닉계층(hidden layer), 출력계층(output layer)의 3개의 계층으로 이루어진 신경망이다.
- 각 계층은 Fully-Connected Layer로 이루어져 있다.

### Deep Neural Network (심층 신경망 ; DNN)

- 은닉계층이 여러 개인 신경망을 말한다.
- 보통 5개 이상의 계층이 있는 경우 'Deep'하다고 표현한다.

<img src = "./image/신경망 구조/IMG_1202.JPG" width="60%">
