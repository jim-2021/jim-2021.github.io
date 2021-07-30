---
layout: single
title:  "Python 기초 문법(3) - Function"
---


## Function이란?

함수는 특정 기능을 구현한 코드 묶음이다. 
반복되는 코드의 묶음으로써 효율적으로 코드를 작성할 수 있게 해준다.

똑같은 구조를 가진 코드는 보통 한 가지의 기능으로 묶이고, 이 기능을 코드로 묶어서 함수로 만든다.

즉 함수를 쓰는 이유는 **재사용성** 이라고 할 수 있다.

```
def 함수이름(param1, param2, ... ):
        <statement1>
        <statement2>
      return
```

## 1. 함수 정의

- 함수의 선언과 호출
- 사용자 정의 함수(user define function)


```python
# 함수 선언 
def add(a, b):
    
    # 입력받은 a, b를 더한 값을 돌려주는 함수.
    c = a + b
    return c

# 함수 호출
add(5, 10)
```




    15



함수의 재사용성을 확인해보자!<br>
아래와 같이 점수에 따라 등급을 부여해주는 코드가 있는데, 다음에 이 코드가 또 필요할 경우 같은 코드를 또 써줘야 한다. 


```python
point = 85

if point >= 90:
    print("A")
elif point >= 80:
    print("B")
else:
    print("C")
```

이렇게 반복되는 코드를 함수를 통해 하나로 묶어줄 수 있다.


```python
# 함수 선언
def grade(point):
    if point >= 90:
        print("A")
    elif point >= 80:
        print("B")
    else:
        print("C")
```


```python
# 함수 호출
grade(85)
```

    B
    


```python
grade(60)
```

    C
    

**똑같은 코드가 2번 이상 반복될 때, 함수로 구현해야겠다고 판단할 수 있다.** 

## 2. 파라미터와 아규먼트

- 파라미터(parameter) : 함수를 선언할 때 호출에서 보내주는 데이터를 받는 변수
- 아규먼트(argument) : 함수를 호출할 때 함수에서 보내주는 데이터


```python
def add2(num1, num2=10, num3=20):  # 파라미터 : num1, num2, num3
    print(num1 - num2 + num3)

# 디폴트 파라미터 : 아규먼트의 갯수가 부족해서 데이터가 없어도 설정한 디폴트 값으로 실행됨
# num2=10, num3=20
```


```python
add2(1, 2)  # 아규먼트 : 1, 2  # 1 - 2 + 20
```

    19
    


```python
add2(5, num3=50)   # 키워드 아규먼트 : 특정 파라미터를 지정해서 바꿔준다.
```

    45
    

정확한 용어 구분은 중요하지만, 보통 **파라미터라고 총칭**한다.

**중요!**<br>
- **input -- (Function) ---> output 의 구조**
- **어떤 input parameter가 들어가서, 어떤 ouput parameter가 나오는지 주목해야한다!**

### 3. 리턴(Retrun)

- 함수를 실행한 결과를 저장한다.
- 중간에 함수의 코드 실행을 종료시킨다.
- return(명령어)


```python
def add(a, b):
    print(a + b)
    
result = add(3, 6)
print(result)
```

    9
    None
    

- 9 -> 함수를 호출할 때 실행되는 'print(a + b)'에서 호출한 것이다.

- None -> 함수를 호출하면 출력해주는 코드는 있는데 함수를 실행한 결과를 (result에) 보내주는 코드가 없기 때문에! => return 함수를 사용해야 한다.


```python
def add2(a, b):
    return(a + b)

result = add2(3, 6)
print(result)
```

    9
    

return으로 위에서 작동된 결과 데이터를 저장해야 그것을 가지고 추가적인 코드를 작성할 수 있다.


```python
def grade2(point):
    if point >= 90:
        return "A"
    elif point >= 80:
        return "B"
    else:
        return "C"
```


```python
# 추가 코드
point = 95
result = grade2(point)
print(result)
```

    A
    


```python
# 추가 코드
if result == 'A':
    print("Pass")
else:
    print("Fail")
```

    Pass
    

함수에서 return 코드가 실행되면 함수의 코드 실행이 종료된다.
<br>
for문의 break와 비슷하다고 생각할 수 있다.


```python
def say(msg):
    if msg == 'end':
        return
    print(msg)
    
say('hi')
```

    hi
    


```python
print(say('end'))
```

    None
    

### 4. 함수 정의의 다양한 형태

#### 1) 함수 parameter와 return이 모두 존재하는 경우.


```python
def add(a, b):
    return a + b

def get_loss(model, metric):
    ...
    ...
    ...
    return model.predict(result, metric=[metric])

# 가장 좋은 방법! 
# 명시적으로 어떤 데이터가 들어갔는지, 나오는지 알고 있으면 내부 코드를 보지 않아고 어떤 기능인지 파악할 수 있다.
```

#### 2) 함수 parameter는 없고 return이 존재하는 경우.


```python
def get_data():
    data = pd.read_csv("quiz.csv")
    return data

# 데이터를 불러오는데 그 데이터가 정해져 있음!
```

#### 3) 함수 parameter는 있는데 return이 없는 경우.


```python
# return이 없는 경우는 대부분 print()로 해서 결과를 보여준다.
def print_song(song): 
    print(song)        # 출력
    
# return이 필요 없음
def save_data(path, data):
    data.co_csv(path)  # 저장 : 데이터를 받아서 해당 경로에 저장해 주는 코드 
```

#### 4) 함수 parameter도 없고 return도 없는 경우.


```python
# 매우 단순한 코드
def say_hi():
    print("Hi!")
    
say_hi()

# 텍스트 파일을 저장하는 함수 : 지정된 데이터를 지정된 경로에 저장하는 코드 
def save_txt():
    with open("quiz.txt", 'w') as f:  # 텍스트 파일의 이름이 정해져 있음
        f.write(txt)
```

    Hi!
    

### 5. 함수의 입력 parameter의 개수를 모를 때!
- `*args`, `**kwargs`
- 함수를 호출할 때 아규먼트와 키워드 아규먼트의 갯수를 특정지을 수 없을 때 사용한다.


```python
# *args
def add_many(*args): # arguments라고 한다. # *(asterisk)를 앞에 붙이는 것으로 여러개의 parameter를 받아서 tuple로 변환하여 준다.   
    
    print(args, type(args))
    total = 0
    for num in args:
        total += num
    return total
    
# *args : 파라미터가 여러개 들어와도 자동으로 튜플로 만들어 준다. 
# 연속형 데이터니까 for문을 돌려 모든 것을 돌릴 수 있다!

add_many(1, 2, 3, 4)  # 아규먼트의 갯수와 상관없이 함수를 실행!
```

    (1, 2, 3, 4) <class 'tuple'>
    




    10




```python
# **kwargs
def add_many2(*args, **kwargs): 
    
    print(args, type(args))
    print(kwargs, type(kwargs))
    return sum(args) + sum(list(kwargs.values()))
    

add_many2(1, 2, 3, 4, num1=11, num2=20)
```

    (1, 2, 3, 4) <class 'tuple'>
    {'num1': 11, 'num2': 20} <class 'dict'>
    




    41



`*args`, `**kwargs` 를 파라미터로 설정해 놓으면 아규먼트나 키워드 아규먼트를 자유자재로 사용이 가능하다.

### 6. 파라미터에 대해 조금 더 알아보자!

- 함수에서 사용되는 변수들에게는 **효력 범위**와 **수명**이 있다.
> 효력 범위 : range, 수명 : lifetime

- 지역 변수 : local variable

    > 함수 내부에서 사용되는 파라미터는 함수 외부에 영향을 미치지 않는다.<br> 
    > 함수를 다 사용하고 나면 없어진다. <br>&nbsp;&nbsp; -> 기능적인 이유 : 메모리 잡아 먹지 않도록 하기 위함


```python
def change_name(name):
    
    # local variable, 지역 변수
    name = "lee"

    return name

name = "kim"
name = change_name(name)
print(name)
```

    lee
    


```python
def change_name(name):
    
    # local variable, 지역 변수
    name = "lee" 
    print("in funcion :", name)  # 내부에서만 사용됨
    
    #return name

name = "kim"  # global, 전역 변수
#name = change_name(name)
change_name(name)
print(name)
```

    in funcion : lee
    kim
    

- 전역 변수는 안 쓰는 것이 좋다. 왜냐하면 사용을 하던 안 하던 선언이 되기 때문에 메모리를 낭비할 수 있기 때문이다.

- 지역 변수는 함수를 호출하지 않으면 선언되지 않기 때문에 메모리를 낭비하지 않는다.

### 7. Lambda 함수

- 굉장히 간단한 함수가 있는 경우, 한 줄짜리 함수로 간편하게 사용할 수 있다.
- 삼항연산
> inline fuction, def라는 명령어 없이 간단하게 정의해서 사용할 수 있다.<br>

- lambda 함수와 반복문을 통해 함수의 정의없이 다양한 프로그래밍이 가능하다.


```python
def add(a, b):
    return a + b

# lambda 함수로 바꾸자!   # lambda 파라미터: return
add2 = lambda a, b: a+b   # a, b는 input parameter, a+b는 return되는 output parameter.

print(add(5, 10))
print(add2(5, 10))
```

    15
    15
    


```python
def return_length(s):
    return len(s)

return_length2 = lambda s: len(s)

print(return_length("Good Morning"))
print(return_length2("Good Morning"))
```

    12
    12
    

람다함수를 함수의 파라미터에 넣어서 사용할 수도 있다.<br>
sort 함수를 사용해서 리스트의 원소들을 사전순, 원소들의 길이에 따라 정렬해보자


```python
strings = ['Jenny', 'Lisa', 'jessica', 'Chu']

strings.sort(key=lambda s:len(s))  # sort되는 원소들을 파라미터로 넣어서 나오는 아웃풋 결과로 sort      
strings
```




    ['Chu', 'Lisa', 'Jenny', 'jessica']



### 8. 파이썬에 이미 정의되어 있는 함수


```python
# 수학 계산
import math  # 수학 기능들을 모아놓은 라이브러리

# 절대값, 올림, 내림
print(abs(-5))
print(math.ceil(2.5))  # 라이브러리 이름.함수 이름
print(math.floor(4.3))

# sin, cos
print(math.sin(1))
print(math.cos(1))
```

    5
    3
    4
    0.8414709848078965
    0.5403023058681398
    


```python
# 복권 숫자 만들기
import random
random.sample(range(1, 46), 7)  

# sample 매서드 : 주어진 범위 내에서 몇 개를 뽑을지
```




    [12, 21, 29, 19, 32, 45, 39]




```python
# 다양한 사전들을 써봅시다.
from collections import defaultdict  # defaultdict : 처음에 초기화가 되어있는 딕셔너리
from collections import OrderedDict  # OrderedDict : 입력한 순서를 기억하는 딕셔너리  # key값의 순서가 저장된 순서대로

D = defaultdict(int)  # int를 넣으면 다 0으로 초기화
print(D) 

D2 = OrderedDict()
D2['a'] = 1
D2['b'] = 2
D2['c'] = 3   # 나중에 추가함
D2['d'] = 5
D2['z'] = 26
print(D2)      

# c 추가하기 전 :  OrderedDict([('a', 1), ('b', 2), ('d', 5), ('z', 26)])
```

    defaultdict(<class 'int'>, {})
    OrderedDict([('a', 1), ('b', 2), ('c', 3), ('d', 5), ('z', 26)])
    
