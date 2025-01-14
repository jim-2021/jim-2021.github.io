## 최적화 알고리즘

- 최적화 알고리즘이 무엇인가?
- 딥러닝에서 최적화 알고리즘이 왜 필요한가?


### 최적화 이론

- 가능한 모든 해 중 **최적의 해**를 찾는 문제를 해결하는 이론이다.
- 최적화 문제는 부등식 및 등식 제약 조건을 지키면서, **목적 함수(손실함수)가 최소가 되게 하는 $x$를 찾는 문제이다.**
- **딥러닝에서는 대부분 제약조건은 사용하지 않는다.**
- 최소화 문제 $\leftrightarrow$ 최대화 문제 : $f(x), -f(x)$

![JPG](/assets/images/IMG_1205.JPG)

#### 최적화 문제를 푸는 방법 2 가지 : 분석적 방법(Analytical method)과 수치적 방법(Numerical method)
- 분석적 방법 : 함수의 모든 구간을 수식으로 알 때 사용하는 수식적인 해석 방법이다.
- 수치적 방법 : 함수의 형태와 수식을 알지 못할 때 사용하는 계산적인 해석 방법이다.
- 딥러닝에서는 수치적 최적화 기법을 사용한다. 

#### Global vs Local solution
- Global solution(전역 솔루션) : 
- Local solution(지역 솔루션) : 

### 딥러닝과 최적화 이론

최적화 알고리즘 기법은 딥러닝의 학습 과정에서 중요한 요소이다.

![JPG](/assets/images/IMG_1204.JPG)

- 딥러닝 학습 과정에서 손실함수가 최소가 되도록 **파라미터를 업데이트할 때, 이 업데이트를 어떻게 해야 할 지 알려주는 것이 최적화 기법이다.**
- 네트워크 구조는 형태를 예측할 수 없는 복잡한 함수이기 때문에 **수치적 최적화 기법**을 사용한다.
- 즉, 딥러닝에서의 학습을 **손실 함수가 최소가 되게 하는 파라미터(w)를 구하는 최적화 문제**로 볼 수 있다.
- 학습을 잘 시키기 위해서 최적화 알고리즘이 필요하다!!

## Gradient Descent (경사하강법)

- 수치적 최적화 기법을 사용해서 최적의 파라미터를 찾을 때, 가장 많이 사용되는 것이 Gradient Descent이다.
