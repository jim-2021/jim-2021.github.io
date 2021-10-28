회귀분석을 위한 통계 & 수학적 개념 이해(2)

## 미분 기초

### 미분의 개념

- 평균 변화율 : $\frac{f(b)-f(a)}{b-a}$

- 순간 변화율 (미분계수 $f\prime(a)$)
    - 정의 : 평균 변화율의 극한값
    - b점이 a점으로 한없이 가까워질 때, **a점에서의 순간변화율**
    - a점에서의 **접선의 기울기**
    
    - $f(a)\prime = \lim_{b \to a} = \frac{f(b)-f(a)}{b-a}$
    
- 도함수 : 순간변화율을 함수값으로 가지는 함수

- 미분 : 도함수를 구하는 행위

### 미분 기본 공식

- 다항함수 
    - $f(x) = c \Rightarrow f\prime(x) = 0, c는 상수$
    - $f(x) = x^n \Rightarrow f\prime(x) = nx^{n-1}, n은 자연수$
    - $f(x) = x^k \Rightarrow f\prime(x) = kx^k-1, k는 유리수(\neq 0)$
    - $\{cf(x)\}\prime = cf\prime(x)$
    - $\{f(x)\pm g(x)\}\prime = f\prime(x) \pm g\prime(x)$

- 곱의 미분
    - $\{f(x)g(x)\}\prime = f\prime(x)g(x) + f(x)g\prime(x)$

- ★합성함수 미분
    - $f(g(x))\prime = f\prime(g(x))g\prime(x)$
    
- 지수함수 미분
    - $\{e^x\}\prime = e^x$
    - $\{a^x\} = a^xlna$

- 로그함수 미분
    - $\{lnx\}\prime = \frac{1}{x}$
    - $\{log_ax\}\prime = \frac{1}{xlna}$

### 미분의 활용 - 통계에서 미분을 왜 사용하는 걸까?

<u>미분을 통해서 **극대값**과 **극소값**을 알 수 있다.</u>

![극대값극소값](/asset/image/극대값극소값.JPG)

$\rightarrow$ 미분으로 구한 도함수를 통해 미분가능한 함수의 극대값, 극소값을 알 수 있다.

$\rightarrow$ $f\prime(x)=0$, 미분값이 0이 되는 지점 

**극대값과 극소값을 왜 알아야 되는데?**

### $\Rightarrow$ Likelihood(가능도함수) - 미분의 활용1

![가능도함수](image/IMG_1060.JPG)

- **가능도함수**: 각각의 $x$가 주어졌을 때 **평균($\mu$)을 구하는 확률** $\Rightarrow$ <u>$\mu$에 대한 함수</u>
    - 확률변수의 실현값을 알 때(데이터가 있을 때) 모수($\mu$)를 추정하고자 함
    - 예시) 
    - X1 = 1 , X2 = 2 , X3 = 3개의 자료가 있을 때, 평균 $\mu_0$는 어떤 값일 가능성이 높을까?


- 확률분포함수 : $\mu$와 $\sigma$가 주어졌을 때 각각의 **$x$를 구하는 확률** $\Rightarrow$ <u>$x$에 대한 함수</u>
    - 모수를 알 때, 확률변수의 실현값($x$)을 예측하고자 함 
    
둘의 차이는?
**같은 식에 대해 변수를 다른 관점에서 본 것!**

확률분포함수는 $x$를 변수로 두고, 가능도함수는 $\mu$를 변수로 둔 것이다.

#### 확률분포함수 (Probability distribution function)

- 종류)
1. 확률밀도함수(Probability density function) 
    - 연속형 확률변수의 확률분포함수


2. 확률질량함수(Probability mass function)
    - 이산형 확률변수의 확률분포함수
    - $f(x) = P(X = x)$

**$\rightarrow$ Likelihood와 대응(모수의 관점)**

3. 누적분포함수(Cumulative distribution function)
    - 누적 확률 분포함수
    - $F(x) = P(X \leq x)$
    

#### 확률밀도함수(Probability density function)
- $X_1 = x_1, X_2 = x_2, X_3 = x_3$ 3개의 자료가 있을 때 확률분포함수
- $f(x_1, x_2, x_3) = (\frac{1}{\sqrt{2\pi\cdot1}})^3 exp(-\frac{(x_1-\mu_0)^2+(x_2-\mu_0)^2+(x_3-\mu_0)^2}{2\cdot1})$

#### 가능도함수(Likelihood)
- 동일한 함수이나 $\mu_0$를 변수로 인식
- $L(\mu_0;x_1, x_2, x_3) = (\frac{1}{\sqrt{2\pi\cdot1}})^3 exp(-\frac{(\mu_0-x_1)^2+(\mu_0-x_2)^2+(\mu_0-x_3)^2}{2\cdot1})$
- $X_1 = 1, X_2 = 2, X_3 = 3$ 일 때!
    - $L(\mu_0;1,2,3) = (\frac{1}{\sqrt{2\pi\cdot1}})^3 exp(-\frac{(\mu_0-1)^2+(\mu_0-2)^2+(\mu_0-3)^2}{2\cdot1})$
    
- 이 함수를 가장 크게 만드는 값을 가져와서 $\mu_0$를 추정한다.

### MLE (Maximum Likelihood Estimator) - 미분의 활용2

- 정의 : Likelihood를 최대로 만드는 모수의 값
    - 위 식($L(\mu_0;1,2,3)$)에서는 MLE = **2**
    - 주로 모수에 hat을 붙여서($\hat{\mu_0}$) 표현한다.
    - **Likelihood를 $\mu_0$로 미분해서 0을 만드는 값 $\Rightarrow$ 최대가 되는 지점(극대값)**

![likilihood](image/IMG_1061.JPG)

$\rightarrow$ likelihood의 접선의 기울기가 0이 되는 지점에서의 모수값

MLE를 구하려면 미분이 필요하다!

- $\mu_0$ MLE 구하기
- $\sigma^2$ MLE 구하기
