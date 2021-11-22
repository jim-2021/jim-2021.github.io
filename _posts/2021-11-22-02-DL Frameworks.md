## 딥러닝 프레임워크

### 프레임워크란?
응용 프로그램을 개발하기 위한 여러 라이브러리나 모듈을 효율적으로 사용할 수 있도록 합쳐 놓은 일종의 패키지이다.

딥러닝은 프레임워크를 적극적으로 사용하자!

why?

딥러닝 프레임워크는 이미 검증된 수많은 라이브러리와 사전 학습(Pre-Trained)까지 완료된 다양한 딥러닝 알고리즘을 제공한다. 
이를 통해 중복적인 기능을 구현해야 하는 소모적인 작업으로부터 개발자를 해방시켜, 문제 해결을 위한 핵심 알고리즘 개발에만 집중할 수 있도록 도와준다.


### 프레임워크 사용의 장단점

#### 장점
- API 사용법만 알면 딥러닝 알고리즘을 쉽고 빠르게 구현할 수 있다.
- 프레임워크가 내부적으로 최적화하여 최적화를 신경쓰지 않아도 된다.
- 구동되는 Device에 종속되지 않은 코드를 작성할 수 있다.(GPU, CPU, MobileAP, TPU, FPGA 등)
- 수치 연산 상의 문제로부터 안전한 구현을 할 수 있다.

#### 단점
- API에서 제공하지 않은 기능은 구현하기 어렵다.
- 프레임워크가 제공하는 수준 이상의 최적화가 어렵다.
- API 버전에 따라 동일한 코드의 동작이 다를 수 있다.
- API에 버그가 있을 경우 대응이 어렵다.

### 대표적인 딥러닝 프레임워크

딥러닝 개발에 사용되는 프레임워크는 특정 딥러닝 분야에 특화되어 있거나 기능상의 차이가 있을 수 있으므로, 자신이 개발하고자 하는 분야와 목적에 따라 알맞은 프레임워크를 선택해야 한다.

#### TensorFlow
- 가장 인기 있는 프레임워크!
- 구글에서 개발했으며 2015년에 오픈소스로 공개했다.
- 저수준 라이브러리. 유연성이 뛰어나다.
- C/C++ 엔진에 파이썬 API로 제작되어 빠른 실행이 가능하다.
- Python, JavaScript, C++, Java, Go, Swift 등의 API를 지원한다.
- 최근 Keras를 정식 API에 포함시켜 사용성을 증대했다.
    - 버전 2점대는 고수준 라이브러리
    - Keras가 TensorFlow의 표준 라이브러리로 자리 잡았다.
    
#### Torch
- Lua라는 스크립트 언어를 기반의 제작된 딥러닝 프레임워크이다.
- 페이스북, 트위터 등에서 주로 연구에 사용한다.
- 유연성과 모델 제작의 단순함을 추구한다.
- Python 구현인 PyTorch가 인기를 끌고 있다.

#### Keras
- 단순하고 직관적인 API의 고수준 딥러닝 라이브러리이다.
- Theano와 TensorFlow를 백엔드로 사용한다.
- 파이썬으로 제작되어 매우 가볍고 배우기도 쉽다.
- Python API만을 지원하고, 비교적 확장성은 부족하다.
- 최근에는 TensorFlow API에 정식으로 포함되었다.

#### 이외에도 Caffe, theano, MatConvNet 등이 있다.

#### TensorFlow
- 구글에서 꾸준히 개발/배포하는 오픈 소스 프레임워크이다.
- 학계, 산업계 모두에서 가장 사랑받는 프레임워크이다.

![JPG](/assets/images/tensorflow 순위.JPG)

### TensorFlow 2.0

- custom에 적절한 TensorFlow의 장점과 쉽게 구현 및 연산이 가능한 Keras의 장점을 결합했다.
- 분산처리에 관한 것을 추가

#### TensorFlow 2.0 장점
- Eager execution으로 곧바로 실행되는 코드를 구현했다.(PyTorch의 직관성)
- AutoGraph를 통한 자동 코드 변환 기능으로 기존 세션 방식의 장점을 유지했다.
- 1.x 버전에서 여러 구현이 혼재하던 API를 Keras를 주축으로 깔끔하게 정리했다.
- **Keras 방식의 간단한 구현과 TensorFlow 방식의 유연한 구현이 모두 사용 가능하다.**
- Dataset 및 Log 관리의 편의성이 향상됐다.
- 사용자 수준에 따라 **직관성, 편리성, 유연성을 조절**하여 자유롭게 사용이 가능하다.

### 코드를 통해 구현해보자


```python
# Import modules
import tensorflow as tf
print(tf.__version__)

# 데이터 불러오기 : mnist 데이터
mnist = tf.keras.datasets.mnist
(x_train, y_train), (x_test, y_test) = mnist.load_data()

## 간단한 전처리
x_train, x_test = x_train / 255.0, x_test / 255.0 
## mnist는 영상이라서 0~255로 표현 -> 0~1로  nomalization

# 네트워크 구조 정의 : 모델 정의
model = tf.keras.models.Sequential([tf.keras.layers.Flatten(input_shape=(28, 28)),
                                   tf.keras.layers.Dense(128, activation='relu'),
                                   tf.keras.layers.Dropout(0.2),
                                   tf.keras.layers.Dense(10, activation='softmax')])

## Sequential : 사용하고자 하는 레이어드를 순서대로 넣어주면 됨
## flatten : 영상이기 때문에 flatten하게 펴주고

# Keras 모델 Compile
model.compile(optimizer='adam', # 최적화 방법 
             loss='sparse_categorical_crossentropy', # 손실함수
             metrics=['accuracy']) # 평가지표

# 학습 수행
model.fit(x_train, y_train, epochs=5)

# 학습 결과 테스트
model.evaluate(x_test, y_test)
```

    2.5.0
    Epoch 1/5
    1875/1875 [==============================] - 2s 973us/step - loss: 0.2982 - accuracy: 0.9145
    Epoch 2/5
    1875/1875 [==============================] - 2s 1ms/step - loss: 0.1407 - accuracy: 0.9585
    Epoch 3/5
    1875/1875 [==============================] - 2s 1ms/step - loss: 0.1050 - accuracy: 0.9677
    Epoch 4/5
    1875/1875 [==============================] - 2s 976us/step - loss: 0.0874 - accuracy: 0.9733
    Epoch 5/5
    1875/1875 [==============================] - 2s 945us/step - loss: 0.0730 - accuracy: 0.9772
    313/313 [==============================] - 0s 643us/step - loss: 0.0698 - accuracy: 0.9771
    




    [0.06984221935272217, 0.9771000146865845]


