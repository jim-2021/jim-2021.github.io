## 로지스틱 회귀분석 : Logistic Regression

다중 선형 회귀분석을 수행하는데 만약 종속변수가 연속형 변수가 아니라 이진형(Binary) 변수를 사용한다면 어떻게 될까?

<img src = "./image/로지스틱회귀/이진값 그래프.JPG" width="40%">
직선이 산출 되긴 하지만 이 직선이 과연 데이터를 잘 설명할 수 있는지는 별개의 문제이다.

<img src = "./image/로지스틱회귀/회귀식.jpg" width="40%">
<center> [회귀식] </center>

우변(설명변수)의 범위에 제한이 없기 때문에 좌변(종속변수) 역시 범위의 제한을 받지 않아야 한다. $\Rightarrow$ $[-\infty, \infty]$

그런데 이진형 변수의 경우 0과 1로 범위의 제한을 받기 때문에 적절하지 않다. $\Rightarrow$ $\{0, 1\}$

**좌변의 범위를 우변과 같이 $[-\infty, \infty]$로 맞춰줘야 한다!**

<로지스틱 회귀분석> 
#### 목적
- 이진형(0/1)의 형태를 갖는 종속변수(분류문제)에 대해 회귀식의 형태로 모형을 추정하는 것
- Q. 왜 우변을 회귀식의 형태로 고집하는 거야?
    - A. 회귀계수 해석이 용이하기 때문!
        - 1. 변수의 통계적 유의성 확인(유의확률 산출 -> 대응하는 변수의 중요도 판별)
        - 2. 변수의 증감과 성공확률의 관계 이해(부호 -> 증감 판별)
        
#### 속성
- 종속변수 Y를 그대로 사용하는 것이 아니라 Y에 대한 로짓 함수(logit function)을 회귀식의 종속변수로 사용
- 로짓함수는 설명변수의 선형결합으로 표현될 수 있음
- 로짓함수의 값은 종속변수에 대한 성공 확률로 역산될 수 있으며, 따라서 분류 문제에 적용할 수 있음

### 승산(Odds)

#### <확률>
- $P(y=1) \in [0, 1]$ 
- p : 성공범주(class = 1)에 속할 확률
    - *관례상 1번 범주가 더 중요한 범주
    
<img src = "./image/로지스틱회귀/확률값 그래프.JPG" width="40%">

- 문제점 : 회귀식에 적용할 경우 확률이 가져서는 안 되는 값(1보다 큰 값, 0보다 작은 값)을 추정할 수 있음


#### <승산(Odds)>
<img src = "./image/로지스틱회귀/승산 식.JPG" width="20%">

- 확률값이 0부터 1로 변화함에 따라 승산은 $0 \sim \infty$의 값을 가진다.
- $Odds \in [0, \infty]$

<img src = "./image/로지스틱회귀/승산 그래프.JPG" width="40%">

- 문제점
    - 여전히 범위에 제약이 존재함 (0 < odds < $\infty$)
    - 비대칭성(Asymmetric)
    
    
#### <로그 승산>

"승산(Odds)에 로그를 취하자!"
<img src = "./image/로지스틱회귀/로그 승산 식.JPG" width="40%">
- 확률값이 0부터 1까지 변화함에 따라 로그 승산은 $-\infty \sim \infty$의 값을 가지며 대칭이다.
- $\log(Odds) \in [-\infty, \infty]$
<img src = "./image/로지스틱회귀/로그 승산 그래프.JPG" width="40%">

- 범위의 제약이 없어짐 $\rightarrow$ **우변의 회귀식이 가진 범위와 일치**
- 대칭성 확보
- 성공확률 P가 작으면 음수값을 갖고, 성공확률 P가 크면 양수값을 가짐 $\Rightarrow$ 성공확률과 정방향으로 비례

### 로지스틱 회귀분석 식
- 로그 승산(Log Odds)을 이용한 회귀분석 식
<img src = "./image/로지스틱회귀/전체식.JPG" width="70%">

- $\sigma(X|\beta)$ : $\beta$ 추정값을 전제한 상태에서 X라는 새로운 값이 주어졌을 때 나타나는 성공 범주에 대한 확률

- $\frac{1}{1+e^{-f(x)}} \; \Rightarrow$ 로지스틱 함수, 시그모이드 함수

### 학습 : 회귀계수 추정

**★경사하강법**

#### 우도함수(Likelihood function)
- 우도함수값 : 실제 정답 범주에 대해서 모델이 그 범주에 속할 것이라고 예측한 확률
<img src = "./image/로지스틱회귀/우도함수 표.JPG" width="70%">

1. 객체별(고객) 
    - 개별 객체의 우도함수는 해당 학습 데이터가 정답 범주에 속할 확률
    - Model A : 고객 1의 우도함수 값은 0.908, 고객 2의 우도함수 값은 0.799


2. 데이터셋 전체
    - 데이터의 생성 과정이 독립임을 가정할 수 있을 때, 전체 데이터셋의 우도함수는 개별 객체의 우도함수 값을 모두 곱한 값임
    - P(A,B) = P(A)P(B)

- 일반적으로 데이터셋의 우도함수는 매우 작은 값을 가지므로(1보다 작은 값이 계속 곱해지니까) **로그 우도함수를 주로 사용한다.**
- **우도함수 값이 클수록 좋은 모델!**

#### 최대 우도 추정법(MLE)
- Maximum likelihood estimation
- 학습 데이터의 개별 객체들이 갖는 정답 범주에 대한 확률을 극대화하자!

<개별적인 객체 1개에 대한 우도함수>
<img src = "./image/로지스틱회귀/객체 우도함수식.JPG" width="70%">

<전체 데이터셋에 대한 우도함수>
- 학습 데이터셋의 객체들이 독립적으로 발생됨을 가정할 경우 전체 데이터셋에 대한 우도함수는 다음과 같이 표현된다.
<img src = "./image/로지스틱회귀/전체 데이터셋 우도함수식.JPG" width="80%">
- 위 로그식은 보통 음수이다. 로그를 최대화하는 것보다 음의 로그함수를 최소화하는 것으로 사용 多

- 우도함수와 로그-우도함수는 회귀계수 $\beta$에 대해 비선형이므로 선형회귀분석과 같이 **명시적인 해가 존재하지 않는다.**
    - 최적화 알고리즘을 차용하여 해를 구한다.(Gradient descent 등)

### 기울기 하강 (Gradient Descent) : 최적화
for 최적의 해 찾기(회귀계수 찾기!)

**로지스틱 회귀분석은 회귀계수 $\beta$에 대한 명시적인 해가 존재하지 않기 때문에 기울기 하강법과 같은 최적화 기법을 통해서 회귀계수 $\beta$를 찾아가야 한다.**


- 파란색 선 : 미지수 w의 변화에 따른 목적함수 값의 변화
- 검은색 점 : 현재 해의 위치
- 화살표 : 목적함수를 최적화하기 위해 미지수 w가 이동해야 하는 방향
<img src = "./image/로지스틱회귀/기울기 하강 그림.JPG" width="70%">

과정을 보면
- 목적함수를 현재의 가중치값(w)에 대해 1차 미분을 수행한 뒤 아래의 절차를 따른다.

<img src = "./image/로지스틱회귀/기울기 하강 과정.JPG" width="70%">

#### 로지스틱 회귀분석의 그래프 표현
- 설명변수의 값들을 적절히 취함해서
- 시그모이드 함수를 통해 출력값을 산출

<img src = "./image/로지스틱회귀/로지스틱 회귀분석 그래프 표현.JPG" width="70%">


- 체인룰을 이용해서 w가 업데이트 되는 과정 살펴보기
<img src = "./image/로지스틱회귀/체인룰.JPG" width="70%">


- Gradient Descent에서 회귀계수가 업데이트 되는 원리
<img src = "./image/로지스틱회귀/회귀계수 업데이트 원리.JPG" width="70%">