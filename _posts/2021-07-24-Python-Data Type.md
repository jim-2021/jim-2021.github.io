---
layout: single
title:  "Python 기초 문법(1) - Data Type"
---

## Python Data types

<br>

우리가 어떤 프로그래밍 언어를 처음 배울 때, 그 언어의 **데이터 타입**을 먼저 배우는 것은 중요하다. 

프로그래밍 언어를 통해 컴퓨터에게 일을 시킬 때, 일의 재료가 데이터이고, 이 데이터를 컴퓨터가 알아듣도록 하는 약속이 바로 데이터 타입이다. 

즉 모든 프로그래밍 언어가 데이터를 다루기 위해 필요한 약속인 것이다. 

데이터 타입에 대한 이해가 있어야 파이썬을 이용한 데이터 분석을 할 수 있으므로 지금부터 파이썬 데이터 타입에 대해 알아보도록 하자.

<br>

알아보기 전에! **변수**에 대해서 우선 살펴보자. 

프로그래밍 언어에서는 "변수"라는 개념을 통해 데이터를 사용할 수 있다. 

변수는 메모리에 데이터를 저장하기 위한 공간을 가리키는 말이고, 변수 선언은 RAM 저장공간에 값을 할당하는 행위이다. 

변수에 데이터를 저장할 때는 <kbd>=</kbd>를 사용한다. 

예를 들어 a = 5 라고 하면 a라는 공간에 5라는 데이터를 할당한다는 말이며, 이때 사용자는 변수의 이름인 a로 데이터를 인식하고, 컴퓨터는 a라는 변수의 주소로 데이터를 인식한다.

<br>

* 파이썬의 변수 특징<br>
파이썬에서는 변수를 선언 해야 그 변수의 데이터 타입이 결정된다. (즉 컴퓨터가 데이터에 대해 알게 된다.) 

또한 변수 할당에 제한이 없어 다른 언어보가 활용이 자유롭다. 
하지만 너무 많은 변수를 할당하면 메모리 낭비가 크다는 점을 기억해야 한다. 

---

본격적으로 데이터 타입에 대해 살펴보자. 

- Numeric : Integer(정수), Float(실수), Complex Number(복소수)
- Dictionary : 사전 데이터 타입
- Boolean : 참/거짓
- Sequence : 순서가 있는 데이터 타입 - String, List, Tuple
- Set

본 포스팅에서는 **정수(int), 실수(float), 문자열(str), 리스트(list), 튜플(tuple), 집합(set), 사전(dict)** 데이터 타입에 대해서 알아보도록 하겠다.

## 1. 정수형(int) & 실수형(float)

정수와 실수는 간단하게 정리하고 넘어가자.<br>
갈길이 멀다!


```python
a = 8
b = 20
a + b
```




    28




```python
c = 5.3
d = 6.10
c + d
```




    11.399999999999999



a에 8을, b에 20을 할당하고, a와 b를 더한 결과를 출력한 것이고,
<br>
c에 5.3을 d에 6.10을 할당하고 c와 d를 더한 결과를 출력한 것이다.
<br>
<br>
숫자형 데이터 타입은 우리가 알고 있는 대부분의 연산을 지원한다.

#### 사칙연산

위에서 할당한 변수를 그대로 이용해 보자.


```python
print(a + b)
print(a - b)
print(a * b)
print(a / b)
```

    28
    -12
    160
    0.4
    

#### 특수연산


```python
c = 6
d = 2
print(c ** d) # c를 d번 곱한 것
print(c // d) # c를 d로 나눈 몫
print(c % d)  # c를 d로 나눈 나머지
```

    36
    3
    0
    

---

### 연속형 데이터 (Sequential Data Type)

* 연속형 데이터는 하나의 변수에 여러 개의 데이터를 가지고 있는 데이터 타입이다.


* 연속형 데이터에는 문자열(string), 리스트(list), 튜플(tuple)이 있다.


* 아래서 살펴볼 문자열 데이터 타입은 문자들을 나열로 인식하기 때문에 연속형 데이터에 포함한다. 


* 각 연속형 데이터 타입의 특징을 파악해서 용도에 맞게 사용하는 것이 중요하다.

## 2. 문자열(str)


```python
s = "This Summer"
s
```




    'This Summer'




```python
s1 = 'Last Christmas I gave you my heart'
s1
```




    'Last Christmas I gave you my heart'



<kbd>'</kbd> 와 <kbd>"</kbd>  두 가지의 기호를 통해 문자열을 나타낸다.
<br>
<br>
특수문자 표현을 통해 문자열을 다양하게 나타낼 수 있다.


```python
print("This\nSummer")  # \n : new line
```

    This
    Summer
    


```python
print("This\tSummer")  # \t : tab
```

    This	Summer
    

#### 문자열 연산


```python
s = "This"
s1 = "Summer"
s + ' ' + s1  # 띄어쓰기 : ' '
```




    'This Summer'



문자열도 연속형 데이터이기 때문에 길이를 알 수 있다. 

len() 함수를 통해 문자열의 길이를 알아보자. 이때 문자뿐 아니라 공백, 기호도 포함한다.


```python
s2 = "bravo, my life!"
len(s2)
```




    15



#### 문자열 formatting

문자열 포맷팅은 문자열을 더 잘 표현하기 위한 방법이다.
<br>
print함수를 이용해서 문자열을 출력할 때 특정 format을 지정할 수 있다.
3가지 방법이 있다.


```python
# "수학 점수는 100점입니다."
subject = "수학" ; score = 100

# 1. print formating
print("%s 점수는 %d점 입니다." % (subject, score))

# 2. str.format 
print("{} 점수는 {}점 입니다.".format(subject, score))

# 3. f-string
print(f"{subject} 점수는 {score}점 입니다.")
```

    수학 점수는 100점 입니다.
    수학 점수는 100점 입니다.
    수학 점수는 100점 입니다.
    

- 1번 방법의 장점은 출력될 데이터의 형식을 알 수 있다는 점이고 => **데이터의 형식 명시**
    - 주로 %d, %f, %s를 사용하는데 각각 정수, 실수, 문자열이다.
<br>
<br>
- 3번 방법의 장점은 코드가 길어졌을 때, 이 자리에 어떤 데이터가 들어오는지 쉽게 알 수 있다. => **데이터의 내용 명시** 
<br>
<br>
- 2번 방법은 format() 함수를 사용해서 문자열을 생성한 것이다. print 함수와는 상관이 없다. 
<br>
<br>
나는 2번 방법만 알고 있었는데, 다른 2가지 방법도 유용하게 사용할 것 같다!

#### 문자열 관련 함수

각 데이터 타입마다 함수를 가지고 있다.

문자열 데이터 타입이 가진 함수들을 정리하면
- upper(), lower()  -> 영어 대소문자 바꾸기

- strip()  -> 문자 공백 지우기

- join()  -> 문자열 삽입

- split()  -> 문자열 나누기

- replace()  -> 문자열 바꾸기



하나씩 해보면


```python
s = "happy"
s.upper()
```




    'HAPPY'




```python
s = "HAPPY"
s.lower()
```




    'happy'




```python
s = " hap py  "  # 앞뒤 공백만 지워준다!
s.strip()
```




    'hap py'




```python
".".join('happy')
```




    'h.a.p.p.y'




```python
s = "Happy Day"
s.split(' ')     
# () 안의 글자를 기준으로 나눈다. 이것을 token이라고 한다. 여기서는 공백을 기준으로 나눠준 것이다.
```




    ['Happy', 'Day']




```python
s = "Happy Day."
s.replace("Happy", "Monday")
```




    'Monday Day.'



---

## 3.리스트(list)

* 연속형 데이터 타입 중 하나로 순서가 있고 수정이 가능한 데이터 타입이다. 
<br>
* 유연한 구조를 가지고 있어서 데이터를 편하게 다룰 수 있고, 가장 많이 사용된다.


* 그런데 수정이 자유롭기 때문에 수정을 하면 안 되는 경우에는 사용을 유의해야 한다.


```python
L = [1, 2, 3, 4, 5]
L, type(L)   # type() : 데이터 타입을 확인
```




    ([1, 2, 3, 4, 5], list)



리스트는 <kbd>[</kbd> 와 <kbd>]</kbd>를 이용하여 표현하며, 
리스트의 원소들은 쉼포로 구분된다. 
<br>
리스트는 다양한 데이터 타입의 원소를 포함한다. 심지어 리스트도!


```python
L1 = [7, "Gee", 3.14, [10, 5, 1]]
L1
```




    [7, 'Gee', 3.14, [10, 5, 1]]



빈 리스트를 만드는 방법은


```python
L3 = []      # 1
L4 = list()  # 2 

L3, L4, type(L3), type(L4)
```




    ([], [], list, list)



#### Indexing

연속형 데이터들은 하나의 변수에 여러 개의 데이터를 가지기 때문에 각 데이터들에 접근하는 방법이 필요하다. 


바로 indexing 기법이다.


```python
L = ["hi", 5, 7, 9, ["A", 3.14, 12]]

# L의 첫번째 원소
print("첫번째 원소 :", L[0])

# L의 5번째 원소
print("5번째 원소 :", L[4])
```

    첫번째 원소 : hi
    5번째 원소 : ['A', 3.14, 12]
    

인덱싱 기법은 0부터 시작해서 1씩 증가하는 정수 index를 사용한다.

또한 음수 index도 있다. 이는 뒤에서부터 접근하는 방법이다.


```python
# L의 마지막 원소 # L[4]
L[-1]  
```




    ['A', 3.14, 12]



여기서 리스트 안의 리스트에 접근하는 방법은 무엇일까?

"A"에 접근해보자.


```python
L[-1][0]   # 마지막 원소[-1]의 첫번째 원소[0]
```




    'A'



#### Slicing

슬라이싱은 이후 배울 numpy array와 pandas series, dataframe에서도 많이 사용된다고 하니 꼭 알아두어야 한다.

슬라이싱은 말 그대로 리스트의 일부분을 잘라낸다는 의미이다. 


```python
L = [1, 2, 3, 4, 50]

L[0:3]  
```




    [1, 2, 3]



위 예시는 L의 첫번째 (L[0]) 부터 3번째 (L[2]) 까지 자른 것이다.

슬라이싱은 인덱싱을 범위로 하여, 끝 인덱스는 (L[3]) 는 포함하지 않고 추출한다.


```python
L[:2]
```




    [1, 2]




```python
L[2:]
```




    [3, 4, 50]



시작 인덱스를 생략하면 자동으로 맨 처음부터 선택되고, 
<br>
끝 인덱스를 생략하면 맨 마지막까지 선택된다.

#### 리스트 연산


- 리스트 더하기 & 곱하기


```python
L = [1, 2]
L2 = ["hi", 5, [3, 4]]

print("L + L2 :", L + L2)
print()  # 한 칸 띄우기
print("L * 3 :", L * 3)
```

    L + L2 : [1, 2, 'hi', 5, [3, 4]]
    
    L * 3 : [1, 2, 1, 2, 1, 2]
    

- 리스트 수정하기


```python
L2[2] = 7
L2
```




    ['hi', 5, 7]



#### 리스트 관련 함수

- append() -> 리스트에 원소 추가하기

- sort() -> 리스트 원소 정렬하기

- reverse() -> 리스트 뒤집기(정렬 X)

- pop() -> 리스트에서 원소 제거하기


하나씩 해보면


```python
L = []       # 빈 리스트 만들기
L.append("i")
L.append("my")
L.append("me")
L.append("mine")
L
```




    ['i', 'my', 'me', 'mine']




```python
L = [4, 8, 12, 7]
L.sort()   # ascending order
# L.sort(revers=True) # descending order
L
```




    [4, 7, 8, 12]




```python
L.reverse()
L
```




    [12, 8, 7, 4]




```python
L.pop()
L
# 한 번 실행할 때마다 뒤에서부터 하나씩 지운다.
```




    [12, 8, 7]



- 리스트의 복사


```python
# 1
ls1 = [3, 4, 5]
ls2 = ls1  # 주소값 복사
ls1, ls2
```




    ([3, 4, 5], [3, 4, 5])




```python
ls1[2] = 10
ls1, ls2
```




    ([3, 4, 10], [3, 4, 10])



주소값 복사 : 같은 공간의 값을 참조하고 있기 때문에 둘 중 하나만 바뀌어도 같이 바뀐다.
<br>
리스트가 같아도 주소값 복사를 하지 않으면 바뀌지 않는다.


```python
# 2
ls3 = ls1.copy()
ls1, ls3
```




    ([3, 4, 10], [3, 4, 10])




```python
ls1[0] = 7
ls1, ls3
```




    ([7, 4, 10], [3, 4, 10])



copy() : 서로 다른 공간(주소값)에 할당되어 있는 메모리를 참조하고 있기 때문에 바뀌지 않는다.

---

## 4. 튜플(tuple)

- 리스트와 거의 같지만 다른 점이 2가지 있다.

    1) ()를 사용한다.

    2) 수정이 불가능하다.                 
<br>   
여기서 수정이 가능한 데이터 타입과 불가능한 데이터 타입을 정리하자면

> Mutable : 수정 가능
&nbsp;&nbsp;&nbsp; 예) List, dict, set

> immutable : 수정 불가능
&nbsp;&nbsp;&nbsp; 예) int, float, string, tuple, frozenset


```python
t = (1, 5, 10)
t, type(t)
```




    ((1, 5, 10), tuple)



튜플은 여러 개의 변수로 여러 개의 값을 받을 수 있다.

위 결과처럼, 앞에서 봤던 것처럼 이렇게 2항에 대해서 출력한 모든 결과는 튜플이다.


```python
t2 = ("hi", "apple", ("hi", "apple"))
t2
```




    ('hi', 'apple', ('hi', 'apple'))




```python
t[0] = 10
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-32-88963aa635fa> in <module>
    ----> 1 t[0] = 10
    

    TypeError: 'tuple' object does not support item assignment


튜플은 수정이 불가능한 immutable 데이터 타입이기 때문에 에러가 난다.

---

## 5. 집합(set)


```python
s = {1, 2, 3}
s, type(s)
```




    ({1, 2, 3}, set)



집합의 특징

1) 원소의 중복을 허용하지 않는다. 즉 원소의 종류를 나타내기 좋다.

2) 원소의 순서가 존재하지 않는다. 즉 원소의 인덱스가 없다.


```python
s[1]  # 인덱스를 쓰면 에러가 난다.
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-35-f8bb2b116405> in <module>
    ----> 1 s[1]
    

    TypeError: 'set' object is not subscriptable


#### 집합의 연산

집합의 연산자인 교집합, 합집합, 차집합을 모두 지원한다.


```python
s1 = {1, 3, 5, 7, 9}
s2 = {2, 3, 4, 5, 6}

# 교집합
print("교집합")
print("s1 & s2 :", s1 & s2)
print("s1.intersection(s2) :", s1.intersection(s2)) # s1과 s2의 자리를 바꿔도 같다. # 교환법칙 성립
print()

# 합집합
print("합집합")
print("s1 | s2 :", s1 | s2)
print("s1.union(s2) :", s1.union(s2))
print()

# 차집합
print("차집합")
print("s1 - s2 :", s1 - s2)
print("s2 - s1 :", s2 - s1)
```

    교집합
    s1 & s2 : {3, 5}
    s1.intersection(s2) : {3, 5}
    
    합집합
    s1 | s2 : {1, 2, 3, 4, 5, 6, 7, 9}
    s1.union(s2) : {1, 2, 3, 4, 5, 6, 7, 9}
    
    차집합
    s1 - s2 : {1, 9, 7}
    s2 - s1 : {2, 4, 6}
    

중복을 허용하지 않는 집합 원소의 **uniqueness**를 활용해보자.


```python
L = [1, 2, 2, 17, 4, 23, 9, 10, 11, 11]
set(L)   # 중복된 것은 표시하지 않는다.
```




    {1, 2, 4, 9, 10, 11, 17, 23}



#### 집합 관련 함수

- add() -> 집합에 원소 하나 추가하기 

- update() -> 집합에 여러 원소 추가하기 

- remove() -> 집합에서 원소 제거하기 

하나씩 해보면


```python
s = set()  # 빈 집합 만들기
s.add(3)
s

# append랑 다르다 : append는 순서대로, add는 막 넣음(집합에 순서 없음)
```




    {3}




```python
s = {5, 7, 0}
s.update({2, 3})  # s 자체가 바뀐다.
s

# s.uion({2, 3})  # 출력 결과는 동일하지만 s 자체가 바뀌는 것은 아니다.
```




    {0, 2, 3, 5, 7}




```python
s.remove(0)
s
```




    {2, 3, 5, 7}



---

## 6. 사전(dictionary)

- 파이썬에 리스트와 함께 많이 사용되는 구조이다.
<br>
- 딕셔너리는 활용도가 높은 데이터 타입이다. 실제 데이터 분석할 때 다양한 용도로 많이 사용한다. 데이터를 저장하고 다루는 것에서 굉장히 중요하니 잘 알아두어야 한다.
<br>

- **핵심 개념 : key - value** 
    - 데이터 저장 방식


```python
D = {"John" : '0011', "Maria" : '1234'}
D, type(D)
```




    ({'John': '0011', 'Maria': '1234'}, dict)




```python
# 빈 사전
D1 = {}  # dict()
D1, type(D1)
```




    ({}, dict)



딕셔너리의 인덱싱 방법은 리스트나 튜플 같은 연속형 데이터 타입과는 다르다.
<br>

정수 인덱스가 아니라 **key값을 통해서 value에 접근한다.**


```python
D['John']

# key값에는 순서가 없다.
```




    '0011'



리스트에서는 원소를 추가하고 수정하는데 다른 코드를 사용한 반면,
<br>
딕셔너리에서는 같은 방법을 사용한다.
<br>
사전 D에 key가 'Julia'이고, value가 '5678'인 원소를 추가해보자.


```python
D['Julia'] = '5678'
D
```




    {'John': '0011', 'Maria': '1234', 'Julia': '5678'}



딕셔너리는 원소가 있으면 수정하고(update), 없으면 추가(insert)한다.
<br>
이때 value를 수정하고, key-value pair 형태로 추가한다.

또한 딕셔너리 데이터 타입에서 중요한 점은

&nbsp;&nbsp;&nbsp;&nbsp;**사전을 만들 때 key가 중복이 있으면 안 된다.**

사전에서 key가 될 수 있는 데이터 타입은 **immutable**이어야 하고, value는 다 가능하다.

#### 사전 관련 함수

- keys() -> 사전의 모든 key값들 보기 

- values() -> 사전의 모든 value들 보기 

- items() -> 사전의 모든 key, value 쌍 보기 

- get() -> 사전의 원소 가져오기 

- in -> 사전에 해당 key값이 존재하는지 확인하기 
    - in은 모든 연속형 데이터 타입에 사용할 수 있다.
    
하나씩 해보면


```python
D = {'name': 'Peter', 'phone': '01012345679', 'birth': '0909'}
D.keys()
```




    dict_keys(['name', 'phone', 'birth'])




```python
D.values()
```




    dict_values(['Peter', '01012345679', '0909'])




```python
D.items()
```




    dict_items([('name', 'Peter'), ('phone', '01012345679'), ('birth', '0909')])




```python
D.get('name')
```




    'Peter'



get() 함수는 key값이 있으면 가져오고, 없으면 새로 key-value를 세팅한다.


```python
D.get('address', 'Busan')
# address가 없으면 만들어서 가져오고, 있으면 원래 것을 가져온다.
```




    'Busan'




```python
"name" in D  # D.keys() # 자동으로 key값에 접근한다.
```




    True




```python
'0909' in D.values()  # value로 확인하는 방법
```




    True



 

--- 

**데이터를 모을 때 리스트의 형태로 모을거냐, 집합의 형태로 모을거냐, 딕셔너리의 형태로 모을거냐 등을 결정하기 위해서 각 데이터 타입의 특징을 잘 알고 있어야 한다!** 
<br>
<br>

그럼 파이썬 데이터 타입에 대한 공부는 여기까지!
