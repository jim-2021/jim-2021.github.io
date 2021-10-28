## KFold Cross Validation

- 모델의 성능을 평가하는 방법
- 모델은 고정한 채 데이터만 바꿔가면서 모델의 성능을 평가

- train 데이터의 $R^2$가 1인데 validation 데이터도 성능이 좋게 나왔다면?
    - 과적합이 맞는 것 같긴 하지만 애매한 부분이 있다. 운좋게 데이터가 잘 선택되서 검증데이터도 성능이 좋게 나온 걸 수도 있잖아!
    - validation 데이터가 높아도 믿으면 안 됨!

- 데이터를 바꿔가면서 모델의 성능을 평가하자!

- KFold Cross Validation은 train과 validation 데이터를 모두 바꾸면서 모델의 성능을 확인한다.

- 좀 더 명확하게 과적합을 확인하려면 train 데이터를 고정하고, validation 데이터만 바꾸면서 모델 성능을 확인해야 한다.


### 머신러닝의 목적 : unseen data에 대한 predict

public leaderboard에 맞춘 모델을 만들면 validation에 과적합 됨 -> 위험.. private leaderboard에 안 맞을 수 있음!

우리가 쓰는 test 데이터는 사실상 validation 데이터이다.

test 데이터는 없는 데이터(unseen data)

=> cross validation

## 하이퍼 파라미터 튜닝
 
모델들의 성능을 올려놓고 비교 -> 하이퍼 파라미터 튜닝

### 1. GridSearchCV
- 예시(SVM)
```
param_grid = [
   {'C': [1, 10, 100, 1000], 'kernel': ['linear']},
   {'C': [1, 10, 100, 1000], 'gamma': [0.001, 0.0001], 'kernel': ['rbf']}, => 8번 
  ]
```
- 단점 : 최적의 c가 50이면 안 나옴! 
- 장점 : 편하다 / 내가 찾고싶은 파라미터만 넣어서 볼 수 있다.

### 2. Randomized Parameter Optimization
- 장점 : GridSearchCV로 찾지 못하는 걸 찾아줌
- 단점 : 많이 넣어줘야 한다. -> 개오래걸림 
    - 랜덤을 뭐로 해줄거?(하이퍼 파라미터 분포를 뭐로 가정할 건지) -> 이걸 알면 참 좋지..
    - 분포 : 유니폼(uniform)으로 많이 함
