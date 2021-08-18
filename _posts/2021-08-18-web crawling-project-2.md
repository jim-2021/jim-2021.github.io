### Web Driver와 Selenium을 이용한 동적 페이지 크롤링

이전 포스팅에서는 **네이버 영화 리뷰 크롤링**을 통해 정적 페이지에 대한 웹 크롤링을 진행하였다.

[네이버 영화 리뷰 크롤링 보러가기] https://jim-2021.github.io/web-crawling-project-1/

이번에는 동적 페이지 크롤링을 해볼 것이다.

시작하기 전에 정적페이지와 동적페이지에 대한 설명을 간단하게 하자면,
말 그대로 
- 정적 : 고정되어 있는
- 동적 : 움직이는

정적 페이지는 클라이언트가 서버에 요청하면 **미리 준비되어 있는 HTML을 클라이언트에게 바로 보내고**

동적 페이지는 클라이언트가 서버에 요청하면 **서버에서 재처리한 결과를 클라이언트에게 보낸다.**

동적 페이지는 페이지의 내용이 사용자와의 상호작용으로 변경되기 때문에 requests 라이브러리로 html 응답을 받아와도 받고자 하는 동적인 데이터는 보이지 않는다.

그런데 내가 보고 있는 웹이 동적 페이지인지는 어떻게 알 수 있을까?

1. 해당 웹 페이지에서 개발자 도구에 들어간다.(ctrl+shift+i)
2. 톱니바퀴 모양의 settings를 클릭한다.
3. Disable javascript를 체크표시한다.
4. 웹 페이지를 새로고침 했을 때 보이지 않는 것들이 동적인 데이터이다.

크롤링하고 싶은 웹페이지가 동적 페이지라면 
어떻게 크롤링해야 할까?

**Selenium**과 **Web Driver**를 이용하는 방법이 있다.
간단하게 설명하면

- **Selenium**은 웹페이지 테스트 자동화용 모듈이다.
    - 개발/테스트용 드라이버(웹브라우저)를 사용하여 실제 사용자가 사용하는 것처럼 동작한다.
- selenium은 **Web Driver**라는 특수한 웹 브라우저를 이용해야 한다.
    - 크롬 드라이버 : 크롬의 개발/테스트용 드라이버(웹브라우저)

동적 페이지는 계속해서 변경되는 데이터를 가지고 있기 때문에(예를 들어, 사용자가 스크롤을 내리면 글이 추가되는) 미리 준비된 HTML을 보내면 원하는 데이터를 얻지 못할 것이다. 

Selenium을 이용하면 실제로 브라우저를 띄워서 element와 상호작용 하면서 변경되는 데이터들을 크롤링 할 수 있다.

주저리 주저리 설명이 많았지만!
그래서 이번 포스팅에서는 무엇을 할 것인가 하면

바로 
## 인스타그램 태그 크롤링

어떤 키워드를 검색했을 때 나오는 여러 태그들을 크롤링하면 해당 키워드에 대한 관심 정도, 트렌드 파악을 할 수 있을 것 같아서 해보고 싶었다.

구체적인 진행은 태그를 검색해서 나오는 게시글 속의 태그를 크롤링하고, 이 태그들의 빈도수를 파악해서 워드 클라우드를 그렸다.

나는 "친환경"에 대한 태그를 크롤링 했다.

여러 블로그를 참고해서 만들었다.
초보인 나에게 구글링은 필수다ㅠㅠ

### 라이브러리 import
- 필요한 것들을 import하고


```python
from selenium import webdriver as wd
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By

from bs4 import BeautifulSoup
import time
import re  # 정규표현식을 사용하기 위한 모듈

import pandas as pd
```

### 코드 연습
- 우선 코드 진행을 여러 섹션으로 나눠서 연습용으로 적어봤다.

### 1. 로그인
- 인스타그램 크롤링을 하기 위해 게시글에 접근하려면 로그인이 필요하다.


```python
# 로그인 정보
user_id = '아이디'
user_pw = '비밀번호'

# 인스타 로그인 url
login_url = 'https://www.instagram.com/'

# Chrome drvier 실행
# 다운로드한 Chrome drvier의 경로
chrome_driver = '/Users/user/jimin/chromedriver_win32/chromedriver.exe'
driver = wd.Chrome(chrome_driver)
driver.get(login_url)
time.sleep(2)

# 로그인
driver.find_element_by_name('username').send_keys(user_id) # 아이디 입력
driver.find_element_by_name('password').send_keys(user_pw) # 비밀번호 입력
time.sleep(2)
# 로그인 버튼 클릭 : click()
driver.find_element_by_css_selector('button.sqdOP.L3NKy.y3zKF').click()
time.sleep(2)

# 정보 나중에 저장하기 클릭 : click()
driver.find_element_by_css_selector('button.sqdOP.yWX7d.y3zKF').click()
time.sleep(2)

# 알림 설정 나중에 하기 클릭 : click()
driver.find_element_by_css_selector('button.aOOlW.HoLwm').click()
time.sleep(2)

# 로그인 완료
```

### 2. 해당 키워드를 검색한 페이지로 이동


```python
chrome_driver = '/Users/user/jimin/chromedriver_win32/chromedriver.exe'

# 태그 검색 url : 해당 키워드를 검색하면 나오는 페이지
keyword = '친환경'
url = f'https://www.instagram.com/explore/tags/{keyword}/'

# driver 객체 생성
driver = wd.Chrome(chrome_driver)
driver.get(url)
time.sleep(5)

# driver 닫기
driver.close()
```

### 3. 하나의 게시글에 접근 
- 왼쪽 상단의 첫 번째 게시글
- ★☆로그인 필요


```python
chrome_driver = '/Users/user/jimin/chromedriver_win32/chromedriver.exe'

# 해당 키워드를 검색하면 나오는 게시글들이 있는 페이지
keyword = '친환경'
url = f'https://www.instagram.com/explore/tags/{keyword}/'

# driver 객체 생성
driver = wd.Chrome(chrome_driver)
driver.get(url)
time.sleep(3)

# 왼쪽 상단의 첫 번째 게시글에 접근 -> click()  # 게시글 진입하려면 로그인 필요
# 각각의 게시글에 있는 태그를 가져오기 위해
driver.find_element_by_css_selector('div.v1Nh3.kIKUG._bz0w').click() # div 태그의 v1Nh3.kIKUG._bz0w 클래스
time.sleep(5)

# driver 닫기
driver.close()
```

### 4. 게시글에 있는 태그 가져오기


```python
# 태그 가져오기 : print()
insta_tags = []

# 게시글 본문에서 태그만 추출하기
data = driver.find_element_by_css_selector('.C4VMK') # 클래스가 C4VMK
tag_raw = data.text  # text만 추출 => 태그(문자열)
tags = re.findall('#[A-Za-z0-9가-힣]+', tag_raw)
tag = ''.join(tags).replace('#', ' ')  # '#' 제거

tag_list = tag.split()

# for문 돌려서 태그 하나씩 리스트에 저장
for tag_one in tag_list:
    insta_tags.append(tag_one)
print(insta_tags)

# findall : 주어진 문자열 전체에서 패턴과 일치하는 것을 모두 찾아서 list로 반환
# re.findall('패턴 문자열' '문자열') 
# 패턴 문자열에 정규표현식을 넣어서 패턴에 해당하는 모든 태그를 찾는다. 
# 정규표현식 : 문자열로 표현할 수 있는 모든 값은 정규표현식으로 대체 가능
```

### 연습

### 1. 하나의 게시물(왼쪽 상단의 첫 번째 게시물) 속에 있는 태그 가져오기
- 로그인부터 태그 검색, 게시글 클릭, 태그 가져오기까지 


```python
# 로그인 정보
user_id = '아이디'
user_pw = '비밀번호'

# 인스타 로그인 url
login_url = 'https://www.instagram.com/'

# Chrome drvier 실행
chrome_driver = '/Users/user/jimin/chromedriver_win32/chromedriver.exe'
driver = wd.Chrome(chrome_driver)
driver.get(login_url)
time.sleep(2)

# 로그인
driver.find_element_by_name('username').send_keys(user_id) # 아이디 입력
driver.find_element_by_name('password').send_keys(user_pw) # 비밀번호 입력
time.sleep(2)
# 로그인 버튼 클릭 : click()
driver.find_element_by_css_selector('button.sqdOP.L3NKy.y3zKF').click()
time.sleep(2)

# 정보 나중에 저장하기 클릭 : click()
driver.find_element_by_css_selector('button.sqdOP.yWX7d.y3zKF').click()
time.sleep(2)

# 알림 설정 나증에 하기 클릭 : click()
driver.find_element_by_css_selector('button.aOOlW.HoLwm').click()
time.sleep(2)

# -----------------------------------------

# 태그 검색 url : 해당 키워드를 검색하면 나오는 페이지
keyword = '친환경'
url = f'https://www.instagram.com/explore/tags/{keyword}/'

# 검색창에 키워드 검색
driver.get(url)
time.sleep(5)

# 로딩이 될 때까지 기다리기
WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.CSS_SELECTOR, 'div.v1Nh3.kIKUG._bz0w')))
# 찾는 element가 로딩이 될 때까지 driver가 최대 10초간 기다림 / css를 이용해서 element를 찾는다.
# 10초가 지나면 에러
# 이유 : 로딩이 안 되고 다음 코드가 진행되면 에러나기 때문에

# 상단 왼쪽 첫 게시물 클릭
driver.find_element_by_css_selector('div.v1Nh3.kIKUG._bz0w').click()  # v1Nh3 kIKUG  _bz0w
time.sleep(3)

# -----------------------------------------

insta_tags = []
data = driver.find_element_by_css_selector('.C4VMK') # 클래스가 C4VMK 
tag_raw = data.text  # text만 추출 => 태그(문자열)
tags = re.findall('#[A-Za-z0-9가-힣]+', tag_raw)
tag = ''.join(tags).replace('#', ' ') # '#' 제거

tag_list = tag.split()

for tag_one in tag_list:
    insta_tags.append(tag_one)
print(insta_tags)

time.sleep(3)

driver.close()
```

    ['Repost', '환경보호', '환경보호실천', '일회용컵재활용', '재활용', '폐기물관리강화', '베트남재활용', '친환경', '재활용습관', '친환경생활습관', 'korchinatnc', '코차이나티엔씨', '페트병재활용']
    

### 2. 여러 게시물 속에 있는 태그 가져오기
- 순차적으로 여러 게시물에 접근
- 각 게시물들의 태그 가져오기


```python
# 로그인 정보, keyword, count
user_id = '아이디'
user_pw = '비밀번호'
keyword = '친환경'
p_count = 3  # 게시글 개수(앞에서부터)

# 인스타 로그인 url
login_url = 'https://www.instagram.com/'

# Chrome drvier 실행
chrome_driver = '/Users/user/jimin/chromedriver_win32/chromedriver.exe'
driver = wd.Chrome(chrome_driver)
driver.get(login_url)
time.sleep(2)

# 로그인
driver.find_element_by_name('username').send_keys(user_id) # 아이디 입력
driver.find_element_by_name('password').send_keys(user_pw) # 비밀번호 입력
time.sleep(2)
# 로그인 버튼 클릭 : click()
driver.find_element_by_css_selector('button.sqdOP.L3NKy.y3zKF').click()
time.sleep(2)

# 정보 나중에 저장하기 클릭 : click()
driver.find_element_by_css_selector('button.sqdOP.yWX7d.y3zKF').click()
time.sleep(2)

# 알림 설정 나증에 하기 클릭 : click()
driver.find_element_by_css_selector('button.aOOlW.HoLwm').click()
time.sleep(2)

# ----------------------------------------------------

# 태그 검색 url : 해당 키워드를 검색하면 나오는 페이지
url = f'https://www.instagram.com/explore/tags/{keyword}/'

# 검색창에 키워드 검색
driver.get(url)
time.sleep(5)

# 로딩이 될 때까지 기다리기
WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.CSS_SELECTOR, 'div.v1Nh3.kIKUG._bz0w'))) 

# 상단 왼쪽 첫 게시물 클릭
driver.find_element_by_css_selector('div.v1Nh3.kIKUG._bz0w').click()  # v1Nh3 kIKUG  _bz0w
time.sleep(3)

insta_tags = []

# for문으로 순차적으로 게시물에 접근
for i in range(p_count):  # p_count : 게시물 개수(앞에서부터)
    time.sleep(3)
    
    # 태그 가져오기 : print()
    data = driver.find_element_by_css_selector('.C4VMK') 
    tag_raw = data.text  # text만 추출 => 태그(문자열)
    tags = re.findall('#[A-Za-z0-9가-힣]+', tag_raw)
    tag = ''.join(tags).replace('#', ' ') # '#' 제거

    tag_list = tag.split()

    for tag_one in tag_list:
        insta_tags.append(tag_one)
    
    # 다음 게시물로 이동 
    driver.find_element_by_css_selector('a._65Bje.coreSpriteRightPaginationArrow').click() 
    #  _65Bje  coreSpriteRightPaginationArrow
    
    time.sleep(2)
    
print(insta_tags)
time.sleep(2)
    
driver.close()
```

    ['Repost', '환경보호', '환경보호실천', '일회용컵재활용', '재활용', '폐기물관리강화', '베트남재활용', '친환경', '재활용습관', '친환경생활습관', 'korchinatnc', '코차이나티엔씨', '페트병재활용', '미미달', '텀블벅', '펀딩', 'mimidar', '고려청자도자기빨대', '도자기빨대', '고려청자', '고려청자시리즈', '친횐경빨대', '친환경', 'straw']
    

### 3. 워드 클라우드 만들기 


```python
# 불용어 처리
stop_words = ['친환경', '맞팔']
result = [word for word in insta_tags if word not in stop_words]
```


```python
# 태그(단어)들을 빈도 수로 정렬
from collections import Counter
count = Counter(result)

# 많은 빈도수를 가진 태그 선정
top_tags = count.most_common(10)  # 빈도수 상위 단어 10개
```


```python
# 워드 클라우그 그리기
from PIL import Image

import matplotlib.pyplot as plt
from wordcloud import WordCloud
import numpy as np 
from PIL import Image 
import matplotlib

font_path = 'C:/Windows/Fonts/malgun.ttf'
mask = np.array(Image.open('cloud.png'))

wc = WordCloud(width=1000, height=600, background_color="white", font_path=font_path, mask=mask)
cloud = wc.generate_from_frequencies(dict(top_tags))
plt.figure(figsize = (12, 8))
plt.axis('off')
plt.imshow(cloud)
```




    <matplotlib.image.AxesImage at 0x27413d08a90>




    
![png](assets/webcrawling/output_22_1.png)
    


## 최종! 하나의 코드로 만들기

- 함수
- 파라미터 : user_id, user_pw, keyword, p_count(게시물 개수), top_tag(빈도수 상위 단어 개수)


```python
def get_tags_and_make_cloud(user_id, user_pw, keyword, p_count, top_tag):

    # 파라미터
    # user_id : '아이디'
    # user_pw : '비밀번호'
    # keyword : '검색할 태그'
    # p_count : 게시물 개수
    # top_tag : 빈도수 상위 단어 개수

    # 인스타 로그인 url
    login_url = 'https://www.instagram.com/'
    
    try:
        # Chrome drvier 실행
        chrome_driver = '/Users/user/jimin/chromedriver_win32/chromedriver.exe'
        driver = wd.Chrome(chrome_driver)
        driver.get(login_url)
        time.sleep(3)

        # 로그인
        driver.find_element_by_name('username').send_keys(user_id) # 아이디 입력
        driver.find_element_by_name('password').send_keys(user_pw) # 비밀번호 입력
        time.sleep(3)
        # 로그인 버튼 클릭 : click()
        driver.find_element_by_css_selector('button.sqdOP.L3NKy.y3zKF').click()
        time.sleep(3)

        # 정보 나중에 저장하기 클릭 : click()
        driver.find_element_by_css_selector('button.sqdOP.yWX7d.y3zKF').click()
        time.sleep(3)

        # 로딩이 될 때까지 기다리기
        WebDriverWait(driver, 20).until(EC.presence_of_element_located((By.CSS_SELECTOR, 'button.aOOlW.HoLwm')))
    
        # 알림 설정 나증에 하기 클릭 : click()
        driver.find_element_by_css_selector('button.aOOlW.HoLwm').click()
        time.sleep(3)
    except:
        print('로그인 실패')
        driver.close()
        
    # ----------------------------------------------------

    # 태그 검색 url : 해당 키워드를 검색하면 나오는 페이지
    url = f'https://www.instagram.com/explore/tags/{keyword}/'

    # 검색창에 키워드 검색
    try:
        driver.get(url)
        time.sleep(5)

        # 로딩이 될 때까지 기다리기
        WebDriverWait(driver, 20).until(EC.presence_of_element_located((By.CSS_SELECTOR, 'div.v1Nh3.kIKUG._bz0w'))) 

        # 상단 왼쪽 첫 게시물 클릭
        driver.find_element_by_css_selector('div.v1Nh3.kIKUG._bz0w').click()
        time.sleep(3)
    except:
        print("검색 url 접근 or 첫 번째 게시물 접근 실패")
        driver.close()
            
    # 태그 담을 빈 리스트
    instagram_tags = []

    # 게시물 이동하면서 태그 추출
    for i in range(p_count):
        try:
            time.sleep(3)
    
            # 태그 가져오기 : print()
            data = driver.find_element_by_css_selector('.C4VMK') 
            tag_raw = data.text  # text만 추출 => 태그(문자열)
            tags = re.findall('#[A-Za-z0-9가-힣]+', tag_raw)
            tag = ''.join(tags).replace('#', ' ') # '#' 제거
    
            tag_list = tag.split()

            for tag_one in tag_list:
                instagram_tags.append(tag_one)
        except:
            print("태그 가져오기 실패")
        
        # 다음 게시물로 이동
        # 다음 게시물이 없을 경우 for문을 종료하기 위해 try, except
        try:
            driver.find_element_by_css_selector('a._65Bje.coreSpriteRightPaginationArrow').click()
            #  _65Bje  coreSpriteRightPaginationArrow
        except:
            break
    
    result = instagram_tags
    time.sleep(3)
    
    driver.close()
    
    # ----------------------------------------------------
    
    # 불용어 처리
    stop_words = ['친환경', '환경보호', '맞팔']
    result = [word for word in result if word not in stop_words]
    
    # 태그(단어)들을 빈도 수로 정렬
    from collections import Counter
    count = Counter(result)

    # 많은 빈도수를 가진 태그 선정
    top_tags = count.most_common(top_tag)  # 빈도수 상위 단어 개수
    
    # 워드 클라우드 그리기
    from PIL import Image

    import matplotlib.pyplot as plt
    from wordcloud import WordCloud
    import numpy as np 
    from PIL import Image 
    import matplotlib

    font_path = 'C:/Windows/Fonts/malgun.ttf'

    wc = WordCloud(width=1000, height=600, background_color="white", font_path=font_path)
    cloud = wc.generate_from_frequencies(dict(top_tags))
    plt.figure(figsize = (12, 8))
    plt.axis('off')
    plt.imshow(cloud)
    # print(result)
```


```python
get_tags_and_make_cloud('아이디', '비밀번호', '친환경', 200, 30)
```


    
![png](assets/webcrawling/output_25_0.png)
    


**<try, except>**

크롤러는 다양한 상황(네트워크 환경 등)에 의해서 죽을 수 있기 때문에 예외 처리가 중요하다.

에러가 났을 때 확인할 수 있어야 하기 때문! 
에러가 날 수 있을 것 같은 부분에 try, except를 적어줬다.

그리고 이전 포스팅에서도 말했듯이 이 코드는 일반화하여 사용할 수 없다.

내부 최적화 등 여러 이슈에 의해서 웹페이지가 리뉴얼될 수 있기 때문에 위에서 사용한 url, 태그 등을 계속해서 사용할 수 없다. 

특히 인스타, 페이스북, 네이버 등은 굉장히 많이 바뀌는 사이트라고 한다.
