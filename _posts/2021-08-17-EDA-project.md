## Instacart Market Basket Analysis

Kaggle에 올라와 있는 **Instacart Market Basket Analysis**를 통해 EDA를 해보도록 하겠습니다.

본 포스팅의 EDA는 전처리와 모델링 전 단계로 말 그대로 **데이터를 탐색**하는 과정입니다.

다양한 판다스와 시각화 기법으로 데이터를 샅샅이 탐험해보겠습니다.

### DATA 설명
이 대회의 목표는 사용자의 다음 주문에 들어갈 제품이 무엇인지 예측하는 것입니다. 

데이터들은 익명으로 작성되어 있고, 200,000명 이상의 Instacart 사용자가 300만개 이상의 식료품을 주문한 샘플을 포함하고 있습니다. 

각 사용자별 4~100개의 주문과, 각 주문에 따라 구매한 제품의 순서를 제공하고, 주문이 이루어진 요일과 시간, 주문 간의 상대적인 시간 측정도 제공합니다.


```python
# 라이브러리 
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
color = sns.color_palette("Paired", 10)

%matplotlib inline
```


```python
color
```




<svg  width="550" height="55"><rect x="0" y="0" width="55" height="55" style="fill:#a6cee3;stroke-width:2;stroke:rgb(255,255,255)"/><rect x="55" y="0" width="55" height="55" style="fill:#1f78b4;stroke-width:2;stroke:rgb(255,255,255)"/><rect x="110" y="0" width="55" height="55" style="fill:#b2df8a;stroke-width:2;stroke:rgb(255,255,255)"/><rect x="165" y="0" width="55" height="55" style="fill:#33a02c;stroke-width:2;stroke:rgb(255,255,255)"/><rect x="220" y="0" width="55" height="55" style="fill:#fb9a99;stroke-width:2;stroke:rgb(255,255,255)"/><rect x="275" y="0" width="55" height="55" style="fill:#e31a1c;stroke-width:2;stroke:rgb(255,255,255)"/><rect x="330" y="0" width="55" height="55" style="fill:#fdbf6f;stroke-width:2;stroke:rgb(255,255,255)"/><rect x="385" y="0" width="55" height="55" style="fill:#ff7f00;stroke-width:2;stroke:rgb(255,255,255)"/><rect x="440" y="0" width="55" height="55" style="fill:#cab2d6;stroke-width:2;stroke:rgb(255,255,255)"/><rect x="495" y="0" width="55" height="55" style="fill:#6a3d9a;stroke-width:2;stroke:rgb(255,255,255)"/></svg>




```python
import warnings
warnings.filterwarnings('ignore')
```


```python
from google.colab import drive
drive.mount('/content/drive')
```

    Drive already mounted at /content/drive; to attempt to forcibly remount, call drive.mount("/content/drive", force_remount=True).
    

#### 데이터 불러오기
- 일단 3개의 데이터프레임만 불러와서 EDA 실행
- 메모리 효율화를 위해 데이터의 크기를 줄인채로 가져오기


```python
# 데이터 불러오기
orders_df = pd.read_csv('../EDA project/instacart_data/orders.csv', 
                        dtype={'order_id':np.int32, 'user_id':np.int32, 'order_number':np.int32, 'order_dow':np.int8, 'order_hour_of_day':np.int8, 'days_since_prior_order':np.float16})
order_products_train_df = pd.read_csv('../EDA project/instacart_data/order_products__train.csv', 
                                      dtype={'order_id':np.int32, 'product_id':np.int32, 'add_to_cart_order':np.int16, 'reordered':np.int8})
order_products_prior_df = pd.read_csv('../EDA project/instacart_data/order_products__prior.csv', 
                                      dtype={'order_id':np.int32, 'product_id':np.int32, 'add_to_cart_order':np.int16, 'reordered':np.int8})
```

데이터를 확인해보자.


```python
# 데이터 확인
orders_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>order_id</th>
      <th>user_id</th>
      <th>eval_set</th>
      <th>order_number</th>
      <th>order_dow</th>
      <th>order_hour_of_day</th>
      <th>days_since_prior_order</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2539329</td>
      <td>1</td>
      <td>prior</td>
      <td>1</td>
      <td>2</td>
      <td>8</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2398795</td>
      <td>1</td>
      <td>prior</td>
      <td>2</td>
      <td>3</td>
      <td>7</td>
      <td>15.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>473747</td>
      <td>1</td>
      <td>prior</td>
      <td>3</td>
      <td>3</td>
      <td>12</td>
      <td>21.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2254736</td>
      <td>1</td>
      <td>prior</td>
      <td>4</td>
      <td>4</td>
      <td>7</td>
      <td>29.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>431534</td>
      <td>1</td>
      <td>prior</td>
      <td>5</td>
      <td>4</td>
      <td>15</td>
      <td>28.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
orders_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 3421083 entries, 0 to 3421082
    Data columns (total 7 columns):
     #   Column                  Dtype  
    ---  ------                  -----  
     0   order_id                int32  
     1   user_id                 int32  
     2   eval_set                object 
     3   order_number            int32  
     4   order_dow               int8   
     5   order_hour_of_day       int8   
     6   days_since_prior_order  float16
    dtypes: float16(1), int32(3), int8(2), object(1)
    memory usage: 78.3+ MB
    


```python
order_products_prior_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>order_id</th>
      <th>product_id</th>
      <th>add_to_cart_order</th>
      <th>reordered</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>33120</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>28985</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>9327</td>
      <td>3</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>45918</td>
      <td>4</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>30035</td>
      <td>5</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
order_products_train_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>order_id</th>
      <th>product_id</th>
      <th>add_to_cart_order</th>
      <th>reordered</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>49302</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>11109</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>10246</td>
      <td>3</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>49683</td>
      <td>4</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>43633</td>
      <td>5</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



각 데이터프레임의 컬럼의 의미를 살펴보자

#### orders_df : 주문 정보
- order_id : 주문 고유번호
- user_id : 사용자 고유번호
- eval_set : 해당 주문이 속한 집단(prior, train, test)
- order_number : 사용자의 주문 순서 번호(1=첫번째 주문, n=n번째 주문)
- order_dow : 주문한 요일
- order_hour_of_day : 주문한 시간
- days_since_prior_order : 마지막 주문 이후의 일수(최대 30일, NaN : 1일)

#### order_product_df(train, prior)
#### train : 현재 구매자군의 제품주문내역
#### prior : 과거 구매자군의 제품주문내역
- order_id : 주문 고유번호
- product_id : 제품 고유번호
- add_to_cart_order : 각 제품이 카트에 담긴 순서
- reordered : 사용자가 제품을 이전에 주문한 적이 있으면 1, 그렇지 않으면 0 

데이터의 전반적인 특징을 살펴보자


```python
orders_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 3421083 entries, 0 to 3421082
    Data columns (total 7 columns):
     #   Column                  Dtype  
    ---  ------                  -----  
     0   order_id                int32  
     1   user_id                 int32  
     2   eval_set                object 
     3   order_number            int32  
     4   order_dow               int8   
     5   order_hour_of_day       int8   
     6   days_since_prior_order  float16
    dtypes: float16(1), int32(3), int8(2), object(1)
    memory usage: 78.3+ MB
    


```python
pd.options.display.float_format = '{:.2f}'.format # 소수점 둘째자리까지 출력

orders_df.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>order_id</th>
      <th>user_id</th>
      <th>order_number</th>
      <th>order_dow</th>
      <th>order_hour_of_day</th>
      <th>days_since_prior_order</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>3421083.00</td>
      <td>3421083.00</td>
      <td>3421083.00</td>
      <td>3421083.00</td>
      <td>3421083.00</td>
      <td>3214874.00</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>1710542.00</td>
      <td>102978.21</td>
      <td>17.15</td>
      <td>2.78</td>
      <td>13.45</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>std</th>
      <td>987581.74</td>
      <td>59533.72</td>
      <td>17.73</td>
      <td>2.05</td>
      <td>4.23</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.00</td>
      <td>1.00</td>
      <td>1.00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>855271.50</td>
      <td>51394.00</td>
      <td>5.00</td>
      <td>1.00</td>
      <td>10.00</td>
      <td>4.00</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>1710542.00</td>
      <td>102689.00</td>
      <td>11.00</td>
      <td>3.00</td>
      <td>13.00</td>
      <td>7.00</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2565812.50</td>
      <td>154385.00</td>
      <td>23.00</td>
      <td>5.00</td>
      <td>16.00</td>
      <td>15.00</td>
    </tr>
    <tr>
      <th>max</th>
      <td>3421083.00</td>
      <td>206209.00</td>
      <td>100.00</td>
      <td>6.00</td>
      <td>23.00</td>
      <td>30.00</td>
    </tr>
  </tbody>
</table>
</div>




```python
order_products_prior_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 32434489 entries, 0 to 32434488
    Data columns (total 4 columns):
     #   Column             Dtype
    ---  ------             -----
     0   order_id           int32
     1   product_id         int32
     2   add_to_cart_order  int16
     3   reordered          int8 
    dtypes: int16(1), int32(2), int8(1)
    memory usage: 340.3 MB
    


```python
order_products_prior_df.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>order_id</th>
      <th>product_id</th>
      <th>add_to_cart_order</th>
      <th>reordered</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>32434489.00</td>
      <td>32434489.00</td>
      <td>32434489.00</td>
      <td>32434489.00</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>1710748.52</td>
      <td>25576.34</td>
      <td>8.35</td>
      <td>0.59</td>
    </tr>
    <tr>
      <th>std</th>
      <td>987300.70</td>
      <td>14096.69</td>
      <td>7.13</td>
      <td>0.49</td>
    </tr>
    <tr>
      <th>min</th>
      <td>2.00</td>
      <td>1.00</td>
      <td>1.00</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>855943.00</td>
      <td>13530.00</td>
      <td>3.00</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>1711048.00</td>
      <td>25256.00</td>
      <td>6.00</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2565514.00</td>
      <td>37935.00</td>
      <td>11.00</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>max</th>
      <td>3421083.00</td>
      <td>49688.00</td>
      <td>145.00</td>
      <td>1.00</td>
    </tr>
  </tbody>
</table>
</div>




```python
order_products_train_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 1384617 entries, 0 to 1384616
    Data columns (total 4 columns):
     #   Column             Non-Null Count    Dtype
    ---  ------             --------------    -----
     0   order_id           1384617 non-null  int32
     1   product_id         1384617 non-null  int32
     2   add_to_cart_order  1384617 non-null  int16
     3   reordered          1384617 non-null  int8 
    dtypes: int16(1), int32(2), int8(1)
    memory usage: 14.5 MB
    


```python
order_products_train_df.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>order_id</th>
      <th>product_id</th>
      <th>add_to_cart_order</th>
      <th>reordered</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>1384617.00</td>
      <td>1384617.00</td>
      <td>1384617.00</td>
      <td>1384617.00</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>1706297.62</td>
      <td>25556.24</td>
      <td>8.76</td>
      <td>0.60</td>
    </tr>
    <tr>
      <th>std</th>
      <td>989732.65</td>
      <td>14121.27</td>
      <td>7.42</td>
      <td>0.49</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.00</td>
      <td>1.00</td>
      <td>1.00</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>843370.00</td>
      <td>13380.00</td>
      <td>3.00</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>1701880.00</td>
      <td>25298.00</td>
      <td>7.00</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2568023.00</td>
      <td>37940.00</td>
      <td>12.00</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>max</th>
      <td>3421070.00</td>
      <td>49688.00</td>
      <td>80.00</td>
      <td>1.00</td>
    </tr>
  </tbody>
</table>
</div>



### 우선 orders_df의 데이터를 보면서 전체적인 주문정보를 파악해보자. 


```python
orders_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>order_id</th>
      <th>user_id</th>
      <th>eval_set</th>
      <th>order_number</th>
      <th>order_dow</th>
      <th>order_hour_of_day</th>
      <th>days_since_prior_order</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2539329</td>
      <td>1</td>
      <td>prior</td>
      <td>1</td>
      <td>2</td>
      <td>8</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2398795</td>
      <td>1</td>
      <td>prior</td>
      <td>2</td>
      <td>3</td>
      <td>7</td>
      <td>15.00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>473747</td>
      <td>1</td>
      <td>prior</td>
      <td>3</td>
      <td>3</td>
      <td>12</td>
      <td>21.00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2254736</td>
      <td>1</td>
      <td>prior</td>
      <td>4</td>
      <td>4</td>
      <td>7</td>
      <td>29.00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>431534</td>
      <td>1</td>
      <td>prior</td>
      <td>5</td>
      <td>4</td>
      <td>15</td>
      <td>28.00</td>
    </tr>
  </tbody>
</table>
</div>




```python
print('order_id 개수 : {}'.format(len(orders_df.order_id.unique())))
print('user_id 개수 : {}'.format(len(orders_df.user_id.unique())))
print('eval_set : {}'.format(orders_df.eval_set.unique()))
print('days_since_prior_order 최대값 : {}'.format(orders_df.days_since_prior_order.max()))
```

    order_id 개수 : 3421083
    user_id 개수 : 206209
    eval_set : ['prior' 'train' 'test']
    days_since_prior_order 최대값 : 30.0
    

eval_set의 비율


```python
# eval_set

set_cnt = orders_df.eval_set.value_counts()

plt.figure(figsize=(14, 10))
sns.barplot(set_cnt.index, set_cnt.values, alpha=0.8, color=color[0])
plt.xlabel('Eval set type', fontsize=15)
plt.ylabel('Count', fontsize=15)
plt.title('Count rows of set type', fontsize=20)
plt.show()
```


    
![png](/assets/EDA-data/output_25_0.png)    



```python
# order_number

grouped = orders_df.groupby('user_id')['order_number'].max().reset_index()
grouped = grouped.order_number.value_counts()

plt.figure(figsize=(20, 8))
sns.barplot(grouped.index, grouped.values, alpha=0.8, color=color[2])
plt.xlabel('Maximum order number', fontsize=15)
plt.ylabel('Count', fontsize=15)
plt.xticks(rotation=45)
plt.title('Maximum order number of UserID', fontsize=20)
plt.show()
```


    
![png](/assets/EDA-data/output_26_0.png)
    


위에서 살펴본 주문 정보 데이터(orders_df)에 대해 설명하자면,

총 3,421,083개의 주문 데이터가 있고(order_id), 사용자는 206209명이며(user_id), 데이터 세트는 prior, train, test 3개이고 이 중 prior가 가장 많은 데이터를 가지고 있다.(eval_set) 이에 prior 데이터인 order_products_prior_df를 사용하도록 하겠다. 

또한 사용자 한 명당 최소 4건, 최대 100건의 주문이 이뤄졌고(order_number), 마지막 주문 이후 일수는 30일을 넘지 않도록 해두었다.(days_since_prior_order)

주문 정보 데이터(orders df)에는 **요일별/시간별 주문 특징**이 포함되어 있다.

이것을 분석해보자.

요일별 주문을 살펴보면


```python
grouped = orders_df.groupby('order_dow').count()['order_id']
week = np.array(['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'])
grouped.index = week[grouped.index]

plt.figure(figsize=(12, 8))
sns.barplot(grouped.index, grouped.values, alpha=0.8, color=color[4])
plt.ylabel('Count', fontsize=12)
plt.xlabel('Day of week', fontsize=12)
plt.title('Order by week day', fontsize=15)
plt.show()
```


    
![png](/assets/EDA-data/output_29_0.png)
    


일요일과 월요일이 가장 많아 보인다.

시간대별 주문을 살펴보면


```python
plt.figure(figsize=(12, 8))
sns.countplot(data=orders_df, x='order_hour_of_day', color=color[6])
plt.ylabel('Count', fontsize=12)
plt.xlabel('Hour of day', fontsize=12)
plt.title('Order by hour of day', fontsize=15)
plt.show()
```


    
![png](/assets/EDA-data/output_31_0.png)
    


10~16시 낮시간에 많은 주문이 이루어진 것을 확인할 수 있다.

그렇다면 요일과 시간을 합쳐서 살펴보면 어떨까?


```python
grouped = orders_df.groupby(['order_dow', 'order_hour_of_day'])['order_id'].agg(lambda x: len(x)/1000).reset_index()
grouped = grouped.pivot(index='order_dow', columns='order_hour_of_day', values='order_id')

week = np.array(['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'])
grouped.index = week[grouped.index]

plt.figure(figsize=(20, 6))
sns.heatmap(grouped, annot=True, fmt='1.1f', linewidths=.5, cmap='BuPu')
plt.title('Frequency : Weekly-Hour')
plt.show()
```


    
![png](/assets/EDA-data/output_33_0.png)
    


일요일 오후``(13~15시)``와 월요일 오전``(9~11시)``가 가장 많고, 

일요일과 월요일을 통틀어서는 9시~16시에 주문이 많은 특징이 있다.

### 주문 상세 정보가 담긴 order_producrs_prior_df


```python
order_products_prior_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>order_id</th>
      <th>product_id</th>
      <th>add_to_cart_order</th>
      <th>reordered</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>33120</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>28985</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>9327</td>
      <td>3</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>45918</td>
      <td>4</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>30035</td>
      <td>5</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



#### 각 주문마다 몇 개의 제품을 구매하는지



```python
grouped = order_products_prior_df.groupby('order_id')['product_id'].count().reset_index()  # 주문 별 제품의 개수
grouped = grouped.product_id.value_counts()  # 각각의 제품 개수가 몇 번 있는지

plt.figure(figsize=(20, 8))
sns.barplot(grouped.index, grouped.values, alpha=0.8)
plt.ylabel('Count', fontsize=12)
plt.xlabel('Number of products in order', fontsize=12)
plt.xticks(rotation=45)
plt.show()
```


    
![png](/assets/EDA-data/output_38_0.png)
    


한 번 주문할 때 5개의 제품을 주문하는 경우가 가장 많다.

#### 재주문률


```python
plt.figure(figsize=(12, 10))
plt.pie(order_products_prior_df.reordered.value_counts(),
        labels=order_products_prior_df.reordered.value_counts().index,
        autopct='%d%%',
        textprops={'fontsize':20})
plt.axis('equal')
plt.title("Reorder Ratio", fontsize=15)
```




    Text(0.5, 1.0, 'Reorder Ratio')




    
![png](/assets/EDA-data/output_41_1.png)
    


평균적으로 제품의 약 58%가 재주문된 제품이다.

#### 장바구니에 담은 순서가 제품의 재구매에 어떤 영향을 미칠까?
- 각 순서별 재주문률


```python
grouped = order_products_prior_df.groupby('add_to_cart_order').mean()[['reordered']]
grouped = grouped.head(60)  # 60번째로 담은 것 까지만 출력

plt.figure(figsize=(20, 8))
sns.pointplot(grouped.index, grouped.reordered, alpha=0.8)
plt.ylabel('reorder ratio', fontsize=12)
plt.xlabel('add_to_cart', fontsize=12)
plt.title('Reorder by add to cart order', fontsize=15)
plt.show()
```


    
![png](/assets/EDA-data/output_44_0.png)
    


장바구니에 먼저 담을수록 재주문될 가능성이 더 높아 보인다.

### aisles_df, departments_df, products_df 


#### products : 제품 정보
- product_id : 제품 고유번호
- product_name : 제품 이름
- aisle_id : 제품 상세카테고리 고유번호
- department_id : 부서 고유번호

#### aisles : 제품 상세 카테고리
- aisle_id : 제품 상세카테고리 고유번호
- aisle : 제품 상세카테고리 이름

#### departments : 제품 카테고리
- department_id : 제품 카테고리 고유번호
- department : 제품 카테고리 이름


```python
products_df = pd.read_csv('../EDA project/instacart_data/products.csv', dtype={'product_id':np.int32, 'aisle_id':np.int16, 'department_id':np.int8})
departments_df = pd.read_csv('../EDA project/instacart_data/departments.csv', dtype={'department_id':np.int8})
aisles_df = pd.read_csv('../EDA project/instacart_data/aisles.csv', dtype={'aisle_id':np.int16})
```


```python
departments_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 21 entries, 0 to 20
    Data columns (total 2 columns):
     #   Column         Non-Null Count  Dtype 
    ---  ------         --------------  ----- 
     0   department_id  21 non-null     int8  
     1   department     21 non-null     object
    dtypes: int8(1), object(1)
    memory usage: 317.0+ bytes
    


```python
departments_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>department_id</th>
      <th>department</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>frozen</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>other</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>bakery</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>produce</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>alcohol</td>
    </tr>
  </tbody>
</table>
</div>




```python
aisles_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>aisle_id</th>
      <th>aisle</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>prepared soups salads</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>specialty cheeses</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>energy granola bars</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>instant foods</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>marinades meat preparation</td>
    </tr>
  </tbody>
</table>
</div>




```python
products_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product_id</th>
      <th>product_name</th>
      <th>aisle_id</th>
      <th>department_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Chocolate Sandwich Cookies</td>
      <td>61</td>
      <td>19</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>All-Seasons Salt</td>
      <td>104</td>
      <td>13</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Robust Golden Unsweetened Oolong Tea</td>
      <td>94</td>
      <td>7</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Smart Ones Classic Favorites Mini Rigatoni Wit...</td>
      <td>38</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Green Chile Anytime Sauce</td>
      <td>5</td>
      <td>13</td>
    </tr>
  </tbody>
</table>
</div>




```python
products_df.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product_id</th>
      <th>aisle_id</th>
      <th>department_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>49688.00</td>
      <td>49688.00</td>
      <td>49688.00</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>24844.50</td>
      <td>67.77</td>
      <td>11.73</td>
    </tr>
    <tr>
      <th>std</th>
      <td>14343.83</td>
      <td>38.32</td>
      <td>5.85</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.00</td>
      <td>1.00</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>12422.75</td>
      <td>35.00</td>
      <td>7.00</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>24844.50</td>
      <td>69.00</td>
      <td>13.00</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>37266.25</td>
      <td>100.00</td>
      <td>17.00</td>
    </tr>
    <tr>
      <th>max</th>
      <td>49688.00</td>
      <td>134.00</td>
      <td>21.00</td>
    </tr>
  </tbody>
</table>
</div>




```python
products_df[products_df.product_id == 1]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product_id</th>
      <th>product_name</th>
      <th>aisle_id</th>
      <th>department_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Chocolate Sandwich Cookies</td>
      <td>61</td>
      <td>19</td>
    </tr>
  </tbody>
</table>
</div>




```python
aisles_df[aisles_df.aisle_id == 61]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>aisle_id</th>
      <th>aisle</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>60</th>
      <td>61</td>
      <td>cookies cakes</td>
    </tr>
  </tbody>
</table>
</div>




```python
departments_df[departments_df.department_id == 19]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>department_id</th>
      <th>department</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>18</th>
      <td>19</td>
      <td>snacks</td>
    </tr>
  </tbody>
</table>
</div>



### product, aisle, department 3개의 관계

products_df의 기술통계량 테이블에서 ``max``가 ``49688``, ``134``,	``21``개로 점점 줄어들고 

각 id에 번호를 매겨서 이름을 보니까

**카테고리 > 상세 카테고리 > 제품**으로 구성되어 있는 것을 확인할 수 있다. 

``department > aisle > product``  

### 이제 데이터 프레임들을 합쳐서 살펴보겠습니다.

재주문에 대해 더 살펴보기 위해 데이터 프레임들을 합쳐서 살펴보도록 하겠다.


```python
order_products = pd.merge(order_products_prior_df, orders_df, on='order_id', how='left')
order_products = pd.merge(order_products, products_df, on='product_id', how='left')
order_products = pd.merge(order_products, aisles_df, on='aisle_id', how='left')
order_products = pd.merge(order_products, departments_df, on='department_id', how='left')
order_products.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>order_id</th>
      <th>product_id</th>
      <th>add_to_cart_order</th>
      <th>reordered</th>
      <th>user_id</th>
      <th>eval_set</th>
      <th>order_number</th>
      <th>order_dow</th>
      <th>order_hour_of_day</th>
      <th>days_since_prior_order</th>
      <th>product_name</th>
      <th>aisle_id</th>
      <th>department_id</th>
      <th>aisle</th>
      <th>department</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>33120</td>
      <td>1</td>
      <td>1</td>
      <td>202279</td>
      <td>prior</td>
      <td>3</td>
      <td>5</td>
      <td>9</td>
      <td>8.00</td>
      <td>Organic Egg Whites</td>
      <td>86</td>
      <td>16</td>
      <td>eggs</td>
      <td>dairy eggs</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>28985</td>
      <td>2</td>
      <td>1</td>
      <td>202279</td>
      <td>prior</td>
      <td>3</td>
      <td>5</td>
      <td>9</td>
      <td>8.00</td>
      <td>Michigan Organic Kale</td>
      <td>83</td>
      <td>4</td>
      <td>fresh vegetables</td>
      <td>produce</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>9327</td>
      <td>3</td>
      <td>0</td>
      <td>202279</td>
      <td>prior</td>
      <td>3</td>
      <td>5</td>
      <td>9</td>
      <td>8.00</td>
      <td>Garlic Powder</td>
      <td>104</td>
      <td>13</td>
      <td>spices seasonings</td>
      <td>pantry</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>45918</td>
      <td>4</td>
      <td>1</td>
      <td>202279</td>
      <td>prior</td>
      <td>3</td>
      <td>5</td>
      <td>9</td>
      <td>8.00</td>
      <td>Coconut Butter</td>
      <td>19</td>
      <td>13</td>
      <td>oils vinegars</td>
      <td>pantry</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>30035</td>
      <td>5</td>
      <td>0</td>
      <td>202279</td>
      <td>prior</td>
      <td>3</td>
      <td>5</td>
      <td>9</td>
      <td>8.00</td>
      <td>Natural Sweetener</td>
      <td>17</td>
      <td>13</td>
      <td>baking ingredients</td>
      <td>pantry</td>
    </tr>
  </tbody>
</table>
</div>




```python
order_products.shape
```




    (32434489, 15)



용량 낭비를 막기 위해 필요없는 데이터프레임은 제거한다.


```python
del orders_df
del order_products_prior_df
del order_products_train_df
del products_df
del aisles_df
del departments_df
```


```python
# 필요없는 열 제거
order_products.drop('eval_set', axis=1, inplace=True)
```

- 요일별 재주문
- 시간별 재주문
- 재주문 기간의 경향성
- 잘 팔리는 제품 top 30
- 상세 카테고리 top 30 : aisle
- 카테고리 top : department
- 주문횟수와 재주문 기간의 관계
- 주문횟수와 재주문 비율의 관계
- 재주문 비율이 높은 제품 : top 30
- 재주문 비율이 높은 상세 카테고리 : top 30
- 재주문 비율이 높은 카테고리



요일별 재주문 : order_dow


```python
grouped = order_products.groupby('order_dow').mean()['reordered']
week = np.array(['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'])
grouped.index = week[grouped.index]

plt.figure(figsize=(12, 8))
sns.barplot(grouped.index, grouped.values, alpha=0.8, color=color[4])
plt.ylabel('Reorder ratio', fontsize=15)
plt.xlabel('week', fontsize=15)
plt.title('Reorder ratio by day of week', fontsize=17)
plt.ylim(0.5, 0.65)
plt.show()
```


    
![png](/assets/EDA-data/output_67_0.png)
    


시간별 재주문 : order_hour_of_day


```python
grouped = order_products.groupby('order_hour_of_day').mean()['reordered']

plt.figure(figsize=(15, 8))
sns.barplot(grouped.index, grouped.values, alpha=0.8, color=color[6])
plt.ylabel('Reorder ratio', fontsize=15)
plt.xlabel('Hour', fontsize=15)
plt.title('Reorder ratio by hour of day', fontsize=17)
plt.ylim(0.5, 0.7)
plt.show()
```


    
![png](/assets/EDA-data/output_69_0.png)
    


요일+시간별 재주문


```python
grouped = order_products.groupby(['order_dow', 'order_hour_of_day']).mean()['reordered'].reset_index() 
grouped = grouped.pivot(index='order_dow', columns='order_hour_of_day', values='reordered')

week = np.array(['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'])
grouped.index = week[grouped.index]

plt.figure(figsize=(20, 6))
sns.heatmap(grouped, annot=True, fmt='1.2f', linewidths=.5, cmap='BuPu')
plt.title('Reorder Frequency : Weekly-Hour')
plt.show()
```


    
![png](/assets/EDA-data/output_71_0.png)
    


주문과 주문 사이의 간격(day)이 얼마나 될까?


```python
plt.figure(figsize=(15, 8))
sns.countplot(data=order_products, x='days_since_prior_order', color=color[0])
plt.ylabel('Count', fontsize=15)
plt.xlabel('Days since prior order', fontsize=15)
plt.title('Frequency distribution by days since prior order', fontsize=17)
plt.show()
```


    
![png](/assets/EDA-data/output_73_0.png)
    


일주일에 한 번, 한 달에 한 번 주문하는 경우가 많다. 

그리고 14일, 21일, 28일에 그래프가 전후보다 더 올라와 있는 것으로 보아 일주일 단위로 주문하는 경우가 많음을 확인할 수 있다.

#### 잘 팔리는 제품 top 30


```python
product_cnt = order_products['product_name'].value_counts().reset_index().head(30)
product_cnt.columns = ['product', 'count']
product_cnt
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product</th>
      <th>count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Banana</td>
      <td>472565</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bag of Organic Bananas</td>
      <td>379450</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Organic Strawberries</td>
      <td>264683</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Organic Baby Spinach</td>
      <td>241921</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Organic Hass Avocado</td>
      <td>213584</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Organic Avocado</td>
      <td>176815</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Large Lemon</td>
      <td>152657</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Strawberries</td>
      <td>142951</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Limes</td>
      <td>140627</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Organic Whole Milk</td>
      <td>137905</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Organic Raspberries</td>
      <td>137057</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Organic Yellow Onion</td>
      <td>113426</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Organic Garlic</td>
      <td>109778</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Organic Zucchini</td>
      <td>104823</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Organic Blueberries</td>
      <td>100060</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Cucumber Kirby</td>
      <td>97315</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Organic Fuji Apple</td>
      <td>89632</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Organic Lemon</td>
      <td>87746</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Apple Honeycrisp Organic</td>
      <td>85020</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Organic Grape Tomatoes</td>
      <td>84255</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Seedless Red Grapes</td>
      <td>82689</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Organic Cucumber</td>
      <td>80392</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Honeycrisp Apple</td>
      <td>79769</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Organic Baby Carrots</td>
      <td>76896</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Organic Half &amp; Half</td>
      <td>76360</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Sparkling Water Grapefruit</td>
      <td>75886</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Organic Large Extra Fancy Fuji Apple</td>
      <td>75165</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Yellow Onions</td>
      <td>73167</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Organic Gala Apples</td>
      <td>72846</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Organic Baby Arugula</td>
      <td>72829</td>
    </tr>
  </tbody>
</table>
</div>



Banana, Organic Strawberries, Organic Baby Spinach 등의 과일, 채소 제품이 가장 많이 구매되고, 

Organic 제품이 Top30의 대부분을 차지하고 있다.

#### 상세 카테고리 top 30 : aisle


```python
aisle_cnt = order_products['aisle'].value_counts().reset_index().head(30)
aisle_cnt.columns = ['aisle', 'count']

plt.figure(figsize=(15, 8))
sns.barplot(aisle_cnt.values[:, 0], aisle_cnt.values[:, 1], alpha=0.8, color=color[8])
plt.ylabel('Count', fontsize=15)
plt.xlabel('Aisle', fontsize=15)
plt.xticks(rotation='vertical')
plt.title('Top 30 : aisle', fontsize=20)
plt.show()
```


    
![png](/assets/EDA-data/output_79_0.png)
    


상세 카테고리 역시 fresh fruits, fresh vegetables가 1, 2위에 있고,

yogurt, cheese, milk 등 유제품도 상위에 있다.

#### 카테고리 Top : department


```python
department_cnt = order_products['department'].value_counts().reset_index()
department_cnt.columns = ['aisle', 'count']

plt.figure(figsize=(15, 8))
sns.barplot(department_cnt.values[:, 0], department_cnt.values[:, 1], alpha=0.8, color=color[8])
plt.ylabel('Count', fontsize=15)
plt.xlabel('Department', fontsize=15)
plt.xticks(rotation='vertical')
plt.title('Top : department', fontsize=20)
plt.show()
```


    
![png](/assets/EDA-data/output_82_0.png)
    


produce라는 카테고리가 압도적인 1위이고, 이어서 dairy eggs, snacks, beverages, frozen 등이 있다.

produce 카테고리 안의 product에는 앞서 제품별 Top30의 상위권에 있던 product들이 높은 주문수로 속해있다.


```python
produce_grouped = ((order_products[order_products.department == 'produce']).groupby(['department', 'product_name']).count()['order_id'].reset_index()).sort_values(by='order_id', ascending=False).head(20)
produce_grouped.columns = ['department', 'product', 'count']
produce_grouped
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>department</th>
      <th>product</th>
      <th>count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>119</th>
      <td>produce</td>
      <td>Banana</td>
      <td>472565</td>
    </tr>
    <tr>
      <th>113</th>
      <td>produce</td>
      <td>Bag of Organic Bananas</td>
      <td>379450</td>
    </tr>
    <tr>
      <th>1181</th>
      <td>produce</td>
      <td>Organic Strawberries</td>
      <td>264683</td>
    </tr>
    <tr>
      <th>845</th>
      <td>produce</td>
      <td>Organic Baby Spinach</td>
      <td>241921</td>
    </tr>
    <tr>
      <th>992</th>
      <td>produce</td>
      <td>Organic Hass Avocado</td>
      <td>213584</td>
    </tr>
    <tr>
      <th>831</th>
      <td>produce</td>
      <td>Organic Avocado</td>
      <td>176815</td>
    </tr>
    <tr>
      <th>671</th>
      <td>produce</td>
      <td>Large Lemon</td>
      <td>152657</td>
    </tr>
    <tr>
      <th>1510</th>
      <td>produce</td>
      <td>Strawberries</td>
      <td>142951</td>
    </tr>
    <tr>
      <th>696</th>
      <td>produce</td>
      <td>Limes</td>
      <td>140627</td>
    </tr>
    <tr>
      <th>1116</th>
      <td>produce</td>
      <td>Organic Raspberries</td>
      <td>137057</td>
    </tr>
    <tr>
      <th>1230</th>
      <td>produce</td>
      <td>Organic Yellow Onion</td>
      <td>113426</td>
    </tr>
    <tr>
      <th>957</th>
      <td>produce</td>
      <td>Organic Garlic</td>
      <td>109778</td>
    </tr>
    <tr>
      <th>1243</th>
      <td>produce</td>
      <td>Organic Zucchini</td>
      <td>104823</td>
    </tr>
    <tr>
      <th>866</th>
      <td>produce</td>
      <td>Organic Blueberries</td>
      <td>100060</td>
    </tr>
    <tr>
      <th>344</th>
      <td>produce</td>
      <td>Cucumber Kirby</td>
      <td>97315</td>
    </tr>
    <tr>
      <th>951</th>
      <td>produce</td>
      <td>Organic Fuji Apple</td>
      <td>89632</td>
    </tr>
    <tr>
      <th>1029</th>
      <td>produce</td>
      <td>Organic Lemon</td>
      <td>87746</td>
    </tr>
    <tr>
      <th>30</th>
      <td>produce</td>
      <td>Apple Honeycrisp Organic</td>
      <td>85020</td>
    </tr>
    <tr>
      <th>972</th>
      <td>produce</td>
      <td>Organic Grape Tomatoes</td>
      <td>84255</td>
    </tr>
    <tr>
      <th>1438</th>
      <td>produce</td>
      <td>Seedless Red Grapes</td>
      <td>82689</td>
    </tr>
  </tbody>
</table>
</div>



#### 주문수가 많을수록 재주문을 하기까지 걸리는 기간이 짧아질까? 
- order_number가 클수록 days_since_prior_order가 작을까?


```python
grouped = order_products.groupby('order_number').mean()['days_since_prior_order']

sns.set(style='whitegrid')
plt.figure(figsize=(16, 6))
plt.plot(grouped, marker='o', color=color[5])
plt.ylabel('Days', fontsize=15)
plt.xlabel('order_number', fontsize=15)
plt.title('Days since prior order', fontsize=20)
plt.show()
```


    
![png](/assets/EDA-data/output_87_0.png)
    


주문 횟수가 많을수록 재주문 기간이 짧아진다.

주문횟수가 많다는 것은 우수고객이라는 뜻으로 추측되고, 이에 우수고객은 재주문까지 걸리는 시간도 짧아서 더욱 많은 구매를 한다는 것으로 이해할 수 있다.

### 주문을 많이 할수록 재구매를 많이 할까?
- order_number가 큰 order_id에 재구매한 product가 많이 있을까?


```python
grouped = order_products.groupby(['order_number', 'reordered']).agg({'product_id':len}).reset_index()
grouped = grouped.pivot(index='order_number', columns='reordered', values='product_id')
grouped['reorder_ratio'] = grouped[1] / grouped.sum(axis=1)
grouped.fillna(0, inplace=True)
grouped
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>reordered</th>
      <th>0</th>
      <th>1</th>
      <th>reorder_ratio</th>
    </tr>
    <tr>
      <th>order_number</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>2078068.00</td>
      <td>0.00</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1490447.00</td>
      <td>557885.00</td>
      <td>0.27</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1258570.00</td>
      <td>792161.00</td>
      <td>0.39</td>
    </tr>
    <tr>
      <th>4</th>
      <td>985974.00</td>
      <td>834324.00</td>
      <td>0.46</td>
    </tr>
    <tr>
      <th>5</th>
      <td>802165.00</td>
      <td>826246.00</td>
      <td>0.51</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>2066.00</td>
      <td>12462.00</td>
      <td>0.86</td>
    </tr>
    <tr>
      <th>96</th>
      <td>2153.00</td>
      <td>11593.00</td>
      <td>0.84</td>
    </tr>
    <tr>
      <th>97</th>
      <td>1953.00</td>
      <td>11414.00</td>
      <td>0.85</td>
    </tr>
    <tr>
      <th>98</th>
      <td>1883.00</td>
      <td>10975.00</td>
      <td>0.85</td>
    </tr>
    <tr>
      <th>99</th>
      <td>1957.00</td>
      <td>10479.00</td>
      <td>0.84</td>
    </tr>
  </tbody>
</table>
<p>99 rows × 3 columns</p>
</div>




```python
# 평균 재구매 비율
avg_reorder_rate = round(grouped[1].sum() / (grouped[[0, 1]].sum()).sum(), 2)
avg_reorder_rate
```




    0.59




```python
sns.set(style='whitegrid')
plt.figure(figsize=(16, 6))
plt.plot(grouped['reorder_ratio'], marker='o', color=color[3])
#plt.ylabel('Days', fontsize=15)
plt.xlabel('order_number', fontsize=15)
plt.title(f'Reordered product ratio (Avg:{avg_reorder_rate})', fontsize=20)
plt.show()
```


    
![png](/assets/EDA-data/output_92_0.png)
    


주문수가 많을수록 재구매가 많다. 

즉 기존에 구매했던 제품을 또 사는 경우가 많다.

주문수가 40번이 넘어가면 80% 이상이 재구매 제품임을 확인할 수 있다.



#### 재구매 비율이 높은 상품 Top 30


```python
grouped = order_products.groupby(['product_id', 'product_name', 'aisle_id']).agg({'order_id':len, 'reordered':lambda x: len(x[x>0]), 'user_id':lambda x: len(x.unique())})
grouped.columns = ['total_order', 'reorder_cnt', 'unique_user_cnt']

grouped['reorder_ratio'] = round(grouped.reorder_cnt / grouped.total_order, 2)
grouped['orders_per_user'] = round(grouped.total_order / grouped.unique_user_cnt, 2)

grouped[grouped.total_order > 1000].sort_values(by='reorder_ratio', ascending=False)[['total_order', 'reorder_ratio', 'orders_per_user']].head(30)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th></th>
      <th>total_order</th>
      <th>reorder_ratio</th>
      <th>orders_per_user</th>
    </tr>
    <tr>
      <th>product_id</th>
      <th>product_name</th>
      <th>aisle_id</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>45504</th>
      <th>Whole Organic Omega 3 Milk</th>
      <th>84</th>
      <td>9108</td>
      <td>0.86</td>
      <td>7.15</td>
    </tr>
    <tr>
      <th>9292</th>
      <th>Half And Half Ultra Pasteurized</th>
      <th>84</th>
      <td>2921</td>
      <td>0.86</td>
      <td>7.23</td>
    </tr>
    <tr>
      <th>47231</th>
      <th>Ultra-Purified Water</th>
      <th>115</th>
      <td>1489</td>
      <td>0.86</td>
      <td>7.02</td>
    </tr>
    <tr>
      <th>5514</th>
      <th>Organic Homogenized Whole Milk</th>
      <th>84</th>
      <td>3970</td>
      <td>0.86</td>
      <td>7.03</td>
    </tr>
    <tr>
      <th>43394</th>
      <th>Organic Lactose Free Whole Milk</th>
      <th>91</th>
      <td>8477</td>
      <td>0.86</td>
      <td>7.09</td>
    </tr>
    <tr>
      <th>34197</th>
      <th>Goat Milk</th>
      <th>84</th>
      <td>5185</td>
      <td>0.85</td>
      <td>6.66</td>
    </tr>
    <tr>
      <th>29447</th>
      <th>Milk, Organic, Vitamin D</th>
      <th>84</th>
      <td>20198</td>
      <td>0.85</td>
      <td>6.87</td>
    </tr>
    <tr>
      <th>38689</th>
      <th>Organic Reduced Fat Milk</th>
      <th>84</th>
      <td>35663</td>
      <td>0.85</td>
      <td>6.70</td>
    </tr>
    <tr>
      <th>31720</th>
      <th>Organic  Whole Milk</th>
      <th>84</th>
      <td>9842</td>
      <td>0.84</td>
      <td>6.30</td>
    </tr>
    <tr>
      <th>24521</th>
      <th>Lowfat Goat Milk</th>
      <th>84</th>
      <td>1159</td>
      <td>0.84</td>
      <td>6.20</td>
    </tr>
    <tr>
      <th>2748</th>
      <th>Organic Reduced Fat Omega-3 Milk</th>
      <th>84</th>
      <td>5159</td>
      <td>0.84</td>
      <td>6.19</td>
    </tr>
    <tr>
      <th>39180</th>
      <th>Organic Lowfat 1% Milk</th>
      <th>84</th>
      <td>14869</td>
      <td>0.84</td>
      <td>6.24</td>
    </tr>
    <tr>
      <th>13166</th>
      <th>Organic Milk Reduced Fat, 2% Milkfat</th>
      <th>84</th>
      <td>12737</td>
      <td>0.84</td>
      <td>6.15</td>
    </tr>
    <tr>
      <th>24852</th>
      <th>Banana</th>
      <th>24</th>
      <td>472565</td>
      <td>0.84</td>
      <td>6.39</td>
    </tr>
    <tr>
      <th>35470</th>
      <th>Water Mineral</th>
      <th>115</th>
      <td>2244</td>
      <td>0.83</td>
      <td>5.77</td>
    </tr>
    <tr>
      <th>38928</th>
      <th>0% Greek Strained Yogurt</th>
      <th>120</th>
      <td>13238</td>
      <td>0.83</td>
      <td>5.81</td>
    </tr>
    <tr>
      <th>20940</th>
      <th>Organic Low Fat Milk</th>
      <th>84</th>
      <td>8438</td>
      <td>0.83</td>
      <td>6.04</td>
    </tr>
    <tr>
      <th>36011</th>
      <th>Organic Fat Free Milk</th>
      <th>84</th>
      <td>26591</td>
      <td>0.83</td>
      <td>5.95</td>
    </tr>
    <tr>
      <th>13176</th>
      <th>Bag of Organic Bananas</th>
      <th>24</th>
      <td>379450</td>
      <td>0.83</td>
      <td>5.97</td>
    </tr>
    <tr>
      <th>1157</th>
      <th>Wheat Sandwich Bread</th>
      <th>112</th>
      <td>1031</td>
      <td>0.83</td>
      <td>5.93</td>
    </tr>
    <tr>
      <th>13806</th>
      <th>1% Milkfat Low Fat Vitamin A &amp; D Milk</th>
      <th>84</th>
      <td>1486</td>
      <td>0.83</td>
      <td>5.74</td>
    </tr>
    <tr>
      <th>27845</th>
      <th>Organic Whole Milk</th>
      <th>84</th>
      <td>137905</td>
      <td>0.83</td>
      <td>5.89</td>
    </tr>
    <tr>
      <th>117</th>
      <th>Petit Suisse Fruit</th>
      <th>2</th>
      <td>3636</td>
      <td>0.83</td>
      <td>5.78</td>
    </tr>
    <tr>
      <th>27511</th>
      <th>Milk, Organic, Fat Free</th>
      <th>84</th>
      <td>2615</td>
      <td>0.83</td>
      <td>5.75</td>
    </tr>
    <tr>
      <th>49609</th>
      <th>Very Berry Flavor Sparking Mineral Water</th>
      <th>115</th>
      <td>1250</td>
      <td>0.82</td>
      <td>5.61</td>
    </tr>
    <tr>
      <th>31651</th>
      <th>Extra Fancy Unsalted Mixed Nuts</th>
      <th>117</th>
      <td>9709</td>
      <td>0.82</td>
      <td>5.49</td>
    </tr>
    <tr>
      <th>14371</th>
      <th>Organic Nonfat Milk</th>
      <th>84</th>
      <td>3063</td>
      <td>0.82</td>
      <td>5.58</td>
    </tr>
    <tr>
      <th>1402</th>
      <th>Plain Soymilk Creamer</th>
      <th>91</th>
      <td>2228</td>
      <td>0.82</td>
      <td>5.65</td>
    </tr>
    <tr>
      <th>20082</th>
      <th>Organic Whole Milk with DHA Omega-3</th>
      <th>84</th>
      <td>13528</td>
      <td>0.82</td>
      <td>5.57</td>
    </tr>
    <tr>
      <th>29118</th>
      <th>French Vanilla Soy Creamer</th>
      <th>91</th>
      <td>3650</td>
      <td>0.82</td>
      <td>5.69</td>
    </tr>
  </tbody>
</table>
</div>



상세 카테고리 84번인 'milk'에 속하는 제품들이 재구매가 많이 된다는 것을 알 수 있다.(재구매율 : 83~86%)

또한 물, 바나나, 요거트, 빵 등도 높은 재구매율을 보인다.

#### 상세 카테고리의 재구매 비율 Top 30


```python
grouped = order_products.groupby('aisle').mean()['reordered'].reset_index()
grouped = grouped.sort_values(by='reordered', ascending=False).head(30)

plt.figure(figsize=(12, 8))
sns.pointplot(grouped['aisle'].values, grouped['reordered'].values, alpha=0.8, color=color[9])
plt.ylabel('Reorder ratio', fontsize=12)
plt.xlabel('Aisle', fontsize=12)
plt.title('aisle : reorder ratio', fontsize=15)
plt.xticks(rotation='vertical')
plt.show()
```


    
![png](/assets/EDA-data/output_98_0.png)
    


구매 상위권이었던 fresh fruits이 재구매 비율에서는 3위로 떨어졌다.

1위인 milk와 2위와의 차이가 꽤 있고, 그 아래로는 비율의 차이가 점점 완만해지는 모습을 나타내고 있다.

#### 카테고리 재구매 비율


```python
grouped = order_products.groupby('department').mean()['reordered'].reset_index()
grouped = grouped.sort_values(by='reordered', ascending=False).head(30)

plt.figure(figsize=(12, 8))
sns.pointplot(grouped['department'].values, grouped['reordered'].values, alpha=0.8, color=color[7])
plt.ylabel('Reorder ratio', fontsize=12)
plt.xlabel('Department', fontsize=12)
plt.title('department : reorder ratio', fontsize=15)
plt.xticks(rotation='vertical')
plt.show()
```


    
![png](/assets/EDA-data/output_101_0.png)
    


카테고리(department)의 구매와 재구매의 상위권이 크게 다르지 않음을 알 수 있다.

다만 구매에서 압도적 1위이던 produce는 재구매 비율에서는 3위로 떨어졌고, 구매에서 2, 4위였던 dairy eggs, beverages가 각각 1, 2위에 자리한다.

