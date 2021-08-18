### requests와 BeautifulSoup을 이용한 정적 페이지 크롤링


## 네이버 영화 : 리뷰 크롤링

- 크롤링 대상 : 영화 제목, 리뷰, 리뷰작성일, 평점

- 영화 제목을 입력 받으면 리뷰작성일, 리뷰, 평점을 리턴하는 함수

크롤링을 하기에 앞서 어떤 사이트에서 어떤 데이터를 가져올지 정해야 한다. 

이미 네이버 영화 사이트에서 하기로 했고, 영화 제목, 리뷰, 리뷰작성일, 평점 데이터를 가져오기로 정했으면 

해당 사이트에 가서 사이트의 구조, 어떻게 하면 데이터를 가져올 수 있는지 잘 살펴봐야 한다.

내가 필요한 리뷰 url을 보니까 **영화 코드**, **페이지 수**가 url에 포함되어 있어서, 코드와 페이지 수를 바꿔가면서 원하는 영화의 리뷰와 원하는 만큼의 페이지의 리뷰를 가져올 수 있을 것 같다.

#### 코드 연습


```python
# 필요한 라이브러리 import
import requests
from bs4 import BeautifulSoup
import pandas as pd
```


```python
page_n = 1 # 첫 번째 페이지의 리뷰들
movie_n = 191597 # 영화 코드 # 보스 베이비 2

# 리뷰 프레임 소스 url # 페이지 주소 규칙화
# 페이지와 영화 코드가 포함되어 있다.
review_index = f'https://movie.naver.com/movie/bi/mi/pointWriteFormList.naver?code={movie_n}&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false&page={page_n}'

# 리뷰 url을 가져오기
resp = requests.get(review_index)
resp.text  # html source가 전부 로컬로 들어왔음을 확인
```




    '\r\n\r\n\r\n\r\n<!DOCTYPE html>\r\n<html lang="ko">\r\n<head>\r\n\t<meta charset="utf-8">\r\n\t<meta http-equiv="X-UA-Compatible" content="IE=edge">\r\n\t<title>네이버 영화</title>\r\n\t\n\n\n\t\n\t\n\t\n\t\n\t\t<link rel="shortcut icon" href="https://ssl.pstatic.net/static/m/movie/icons/naver_movie_favicon.ico" type="image/x-icon">\n\t\t\t\n\n\r\n\t\n<link rel="stylesheet" type="text/css" href="/css/common.css?20210728101151" />\r\n\t\n<link rel="stylesheet" type="text/css" href="/css/movie_tablet.css?20210728101151" />\n<link rel="stylesheet" type="text/css" href="/css/movie_end.css?20210728101151" />\r\n\t\r\n\t\n\n\n<script type="text/javascript" src="/js/deploy/movie.all.js?20210728101151"></script>\n\n\n\r\n</head>\r\n<body>\r\n\t<!-- content -->\r\n<input type="hidden" name="movieCode" id="movieCode" value="191597"/>\r\n<input type="hidden" name="onlyActualPointYn" id="onlyActualPointYn" value="N"/>\r\n<input type="hidden" name="includeSpoilerYn" id="includeSpoilerYn" value="N"/>\r\n<input type="hidden" name="order" id="order" value="sympathyScore"/>\r\n<input type="hidden" name="page" id="page" value="1"/>\r\n<div class="ifr_area basic_ifr">\r\n\t<div class="input_netizen ">\r\n\t\t\r\n\t\t\t\r\n\t\t\t\r\n\t\t\t\r\n\t\t\r\n\t\t\t\t<!-- [D] 관람객 평점 작성 완료 -->\r\n                <div id="actualPointWriteExecuteLayer" class="ly_viewer" style="display:none">\r\n                        <h4>관람객 평점 작성 완료 안내</h4>\r\n                        <p>관람객 평점이 등록되었습니다.<br><em>네이버페이 포인트 500원</em>이 적립되었습니다.<br><em>7일 이후</em> 확인 가능합니다.</p>\r\n                        <p>(평점 삭제시, 적립된 포인트는 회수됩니다.)</p>\r\n                        <div class="btn">\r\n                                <a href="#" id="actualPointWriteExecuteLayerOkButton" class="ok">확인</a>\r\n                                <a href="#" id="actualPointWriteExecuteLayerCloseButton" class="close" title="닫기">관람객 평점 작성 완료 안내 레이어 닫기</a>\r\n                        </div>\r\n                </div>\r\n                <!-- //관람객 평점 작성 완료 -->\r\n                \r\n                <!-- [D] 관람객 평점 작성 완료2 -->\r\n                <div id="pointWriteExecuteLayer" class="ly_viewer" style="display:none">\r\n                        <h4>관람객 평점 작성 완료 안내</h4>\r\n                        <p class="msg1">관람객 평점이 등록되었습니다.</p>\r\n                        <div class="btn">\r\n                                <a href="#" id="pointWriteExecuteLayerOkButton" class="ok">확인</a>\r\n                                <a href="#" id="pointWriteExecuteLayerCloseButton" class="close" title="닫기">관람객 평점 작성 완료 안내 레이어 닫기</a>\r\n                        </div>\r\n                </div>\r\n                <!-- //관람객 평점 작성 완료2 -->\r\n\t\t\t\t\r\n\t\t\t\t<div class="score_total">\r\n\t\t\t\t\t<strong class="total">관람객 평점 <em>1,260</em>건<button id="open-form-btn" class="btn_review">내 평점 등록</button></strong>\r\n\t\t\t\t</div>\r\n\t\t\t\t\r\n\t\t\t\t<div id="orderCheckbox" class="top_behavior">\r\n\t\t\t\t\t<ul class="sorting_list">\r\n\t\t\t\t\t\t<li class=\'on\'><a href="#" onclick="parent.clickcr(this, \'ara.bysym\', \'\', \'\', event); dislplayOrder(\'sympathyScore\');">공감순</a></li>\r\n\t\t\t\t\t\t<li><a href="#" onclick="parent.clickcr(this, \'ara.byrct\', \'\', \'\', event); dislplayOrder(\'newest\');">최신순</a></li>\r\n\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t<li><a href="#" onclick="parent.clickcr(this, \'ara.high\', \'\', \'\', event); dislplayOrder(\'highest\');">평점 높은 순</a></li>\r\n\t\t\t\t\t\t\t<li><a href="#" onclick="parent.clickcr(this, \'ara.low\', \'\', \'\', event); dislplayOrder(\'lowest\');">평점 낮은 순</a></li>\r\n\t\t\t\t\t\t\r\n\t\t\t\t\t</ul>\r\n\t\t\t\t\t\r\n\t\t\t\t\t\t<ul class="quarter_mode">\r\n\t\t\t\t\t\t\t<li>\r\n\t\t\t\t\t\t\t\t<input type="checkbox" id="spoilerYnCheckBox" name="spilerViewer" class="blind " title="스포일러 보기" onclick="parent.clickcr(this,\'\',\'\',\'\',event); return false;"  />\r\n\t\t\t\t\t\t\t\t<label for="spoilerYnCheckBox" id="spoilerYnLable" class="label_viewer">스포일러 보기</label>\r\n\t\t\t\t\t\t\t</li>\r\n\t\t\t\t\t\t\t<li>\r\n\t\t\t\t\t\t\t\t<input type="checkbox" id="actualYnCheckBox" name="viewer" title="관람객 평점만 보기" class="blind "onclick="parent.clickcr(this,\'ura.mgs\',\'\',\'\',event); return false;"  />\r\n\t\t\t\t\t\t\t\t<label for="actualYnCheckBox" id="actualYnLable" class="label_viewer">관람객 평점만 보기</label>\r\n\t\t\t\t\t\t\t\t<a href="#" id="actualPointHelpButton" class="help _actualPointHelp" title="도움말">관람객 평점만 보기 도움말</a>\r\n\t\t\t\t\t\t\t\t<div id="actualPointHelp" class="ly_help _actualPointHelp" style="display:none">\r\n\t\t\t\t\t\t\t\t\t<h5>관람객평점만 보기 안내 레이어</h5>\r\n\t\t\t\t\t\t\t\t\t<div class="ly_cont _actualPointHelp">\r\n\t\t\t\t\t\t\t\t\t\t<p>네이버 영화에서 예매하신 고객님들이<br> 영화 관람 후 등록해주신 평점입니다.</p>\r\n\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t<button type="button" id="actualPointHelpCloseButton" class="btn_close _actualPointHelp" title="닫기"><span class="blind">관람객 평점만 보기 안내 레이어 닫기</span></button>\r\n\t\t\t\t\t\t\t\t\t<span class="arr _actualPointHelp"></span>\r\n                                </div>\r\n\t\t\t\t\t\t\t</li>\r\n\t\t\t\t\t\t</ul>\r\n\t\t\t\t\t\r\n\t\t\t\t</div>\r\n\t\t\r\n\t\t\t\t\r\n\t\t\t\t\r\n\t\t\t\t\t\r\n\t\t\t\t\t\r\n\t\t\t\t\t\t<div class="score_result">\r\n\t\t\t\t\t\t\t<ul>\r\n\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t<li>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="star_score">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="score_reple">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_viewer">관람객</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<!-- 스포일러 컨텐츠로 처리되는지 여부 -->\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span id="_filtered_ment_0">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t가볍게 보러갔다가 감동까지 느끼고 옵니다올해 봤던 애니메이션 중에 제일 재밌게 봤네요 \r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<dl>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="javascript:showPointListByNid(17609059, \'after\');parent.clickcr(this, \'ara.uid\', \'\', \'\', event); return false;" target="_top">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<span>교님(sair****)</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>2021.07.21 13:23</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="parent.clickcr(this, \'ara.report\', \'\', \'\', event); common.report(\'false\',\'sair****\', \'EH5RD/0UjVTJFwFPo+/6t3aHLqqTuy4JcllxXjuA7Hs=\', \'가볍게 보러갔다가 감동까지 느끼고 옵니다올해 봤던 애니메이션 중에 제일 재밌게 봤네요 \', \'17609059\', \'point_after\', false);return false;" class="go_report2"><em>신고</em></a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</dl>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="btn_area">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_sympathyButton" href="#" onclick="parent.clickcr(this, \'ara.sym\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_up"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="sympathy_17609059">154</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, \'ara.opp\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_down"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">비공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="notSympathy_17609059">13</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t</li>\r\n\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t<li>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="star_score">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="score_reple">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_viewer">관람객</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<!-- 스포일러 컨텐츠로 처리되는지 여부 -->\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span id="_filtered_ment_1">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t귀여워서 심장 아픔ㅠㅠ 세상에 이런 보스라면 매일 출근하궤쒀!! \r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<dl>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="javascript:showPointListByNid(17608937, \'after\');parent.clickcr(this, \'ara.uid\', \'\', \'\', event); return false;" target="_top">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<span>끼야오루(hell****)</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>2021.07.21 12:09</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="parent.clickcr(this, \'ara.report\', \'\', \'\', event); common.report(\'false\',\'hell****\', \'RJVAVhjVU7BtAcRp4P8quyu3IfxHoaqdPGIqo9hePb4=\', \'귀여워서 심장 아픔ㅠㅠ 세상에 이런 보스라면 매일 출근하궤쒀!! \', \'17608937\', \'point_after\', false);return false;" class="go_report2"><em>신고</em></a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</dl>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="btn_area">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_sympathyButton" href="#" onclick="parent.clickcr(this, \'ara.sym\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_up"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="sympathy_17608937">128</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, \'ara.opp\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_down"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">비공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="notSympathy_17608937">9</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t</li>\r\n\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t<li>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="star_score">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="score_reple">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_viewer">관람객</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<!-- 스포일러 컨텐츠로 처리되는지 여부 -->\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span id="_filtered_ment_2">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t귀여운 아가보스가 두명이나 나오는데 비주얼적으로 안볼수가 없지요!!^^ 1편도 재미있게 봤었는데, 2편은 더더 경쾌하고 액션 넘쳐서 즐겁게 볼수 있었어요. 이 사랑스러운 영화 추천합니다!! \r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<dl>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="javascript:showPointListByNid(17609015, \'after\');parent.clickcr(this, \'ara.uid\', \'\', \'\', event); return false;" target="_top">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<span>pian****</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>2021.07.21 13:02</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="parent.clickcr(this, \'ara.report\', \'\', \'\', event); common.report(\'false\',\'pian****\', \'A5c6H+fI0x/lUBdyp3qESiPDdcKM9wN2nqUj0IHyUwY=\', \'귀여운 아가보스가 두명이나 나오는데 비주얼적으로 안볼수가 없지요!!^^ 1편도 재미있게 봤었는데, 2편은 더더 경쾌하고 액션 넘쳐서 즐겁게 볼수 있었어요. 이 사랑스러운 영화 추천합니다!! \', \'17609015\', \'point_after\', false);return false;" class="go_report2"><em>신고</em></a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</dl>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="btn_area">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_sympathyButton" href="#" onclick="parent.clickcr(this, \'ara.sym\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_up"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="sympathy_17609015">96</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, \'ara.opp\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_down"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">비공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="notSympathy_17609015">3</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t</li>\r\n\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t<li>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="star_score">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="score_reple">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_viewer">관람객</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<!-- 스포일러 컨텐츠로 처리되는지 여부 -->\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span id="_filtered_ment_3">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t너무 사랑스럽잖아!!ㅠㅠㅠ 진짜 1편도 다섯번정도 봤는데 앞으로 계속 나와줘라 \r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<dl>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="javascript:showPointListByNid(17608958, \'after\');parent.clickcr(this, \'ara.uid\', \'\', \'\', event); return false;" target="_top">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<span>두여름(gaeu****)</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>2021.07.21 12:23</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="parent.clickcr(this, \'ara.report\', \'\', \'\', event); common.report(\'false\',\'gaeu****\', \'9z/I8YCOkhg4rW8A/dm9CPhUkSI2sk+URX4E+SfeRa0=\', \'너무 사랑스럽잖아!!ㅠㅠㅠ 진짜 1편도 다섯번정도 봤는데 앞으로 계속 나와줘라 \', \'17608958\', \'point_after\', false);return false;" class="go_report2"><em>신고</em></a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</dl>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="btn_area">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_sympathyButton" href="#" onclick="parent.clickcr(this, \'ara.sym\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_up"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="sympathy_17608958">87</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, \'ara.opp\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_down"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">비공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="notSympathy_17608958">3</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t</li>\r\n\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t<li>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="star_score">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="score_reple">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_viewer">관람객</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<!-- 스포일러 컨텐츠로 처리되는지 여부 -->\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span id="_filtered_ment_4">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t귀여운 캐릭터로 기분까지 좋아지는 영화 \r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<dl>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="javascript:showPointListByNid(17608908, \'after\');parent.clickcr(this, \'ara.uid\', \'\', \'\', event); return false;" target="_top">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<span>sed1834(xxx7****)</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>2021.07.21 11:53</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="parent.clickcr(this, \'ara.report\', \'\', \'\', event); common.report(\'false\',\'xxx7****\', \'Lwx11rZo7j/HiQDsuldQdYOjYUs3d6u6jy9+E8NirtI=\', \'귀여운 캐릭터로 기분까지 좋아지는 영화 \', \'17608908\', \'point_after\', false);return false;" class="go_report2"><em>신고</em></a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</dl>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="btn_area">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_sympathyButton" href="#" onclick="parent.clickcr(this, \'ara.sym\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_up"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="sympathy_17608908">81</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, \'ara.opp\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_down"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">비공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="notSympathy_17608908">3</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t</li>\r\n\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t<li>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="star_score">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="score_reple">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_viewer">관람객</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<!-- 스포일러 컨텐츠로 처리되는지 여부 -->\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span id="_filtered_ment_5">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t겁나 귀엽고 웃김ㅋㅋㅋㅋ 혼자 기분전환할겸 봤는데 친구 데리고 한번 더 보고싶음 꿀잼... 베이비들 넘 귀엽~~~!! \r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<dl>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="javascript:showPointListByNid(17608761, \'after\');parent.clickcr(this, \'ara.uid\', \'\', \'\', event); return false;" target="_top">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<span>eclipse(doct****)</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>2021.07.21 10:07</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="parent.clickcr(this, \'ara.report\', \'\', \'\', event); common.report(\'false\',\'doct****\', \'zzXA7/IWLojqP4xpush0pF86D4Ll73zVk/x4+92t2Ak=\', \'겁나 귀엽고 웃김ㅋㅋㅋㅋ 혼자 기분전환할겸 봤는데 친구 데리고 한번 더 보고싶음 꿀잼... 베이비들 넘 귀엽~~~!! \', \'17608761\', \'point_after\', false);return false;" class="go_report2"><em>신고</em></a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</dl>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="btn_area">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_sympathyButton" href="#" onclick="parent.clickcr(this, \'ara.sym\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_up"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="sympathy_17608761">75</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, \'ara.opp\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_down"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">비공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="notSympathy_17608761">8</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t</li>\r\n\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t<li>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="star_score">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="score_reple">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_viewer">관람객</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<!-- 스포일러 컨텐츠로 처리되는지 여부 -->\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span id="_filtered_ment_6">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t베이비 넘모 귀엽고,, 사랑스러움 ㅠㅠ 심장에 무리 올뻔 &gt;_&lt; \r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<dl>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="javascript:showPointListByNid(17608923, \'after\');parent.clickcr(this, \'ara.uid\', \'\', \'\', event); return false;" target="_top">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<span>Quin(suen****)</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>2021.07.21 12:01</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="parent.clickcr(this, \'ara.report\', \'\', \'\', event); common.report(\'false\',\'suen****\', \'j8Ll6mAOIRpa0xarR1OBP1NfiCiHmUAekbTI+ZlAw/Y=\', \'베이비 넘모 귀엽고,, 사랑스러움 ㅠㅠ 심장에 무리 올뻔 &gt;_&lt; \', \'17608923\', \'point_after\', false);return false;" class="go_report2"><em>신고</em></a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</dl>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="btn_area">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_sympathyButton" href="#" onclick="parent.clickcr(this, \'ara.sym\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_up"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="sympathy_17608923">64</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, \'ara.opp\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_down"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">비공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="notSympathy_17608923">2</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t</li>\r\n\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t<li>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="star_score">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="score_reple">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_viewer">관람객</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<!-- 스포일러 컨텐츠로 처리되는지 여부 -->\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span id="_filtered_ment_7">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t이쁜캐릭터들 재미와 감동입니다. \r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<dl>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="javascript:showPointListByNid(17608959, \'after\');parent.clickcr(this, \'ara.uid\', \'\', \'\', event); return false;" target="_top">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<span>123erjjerf(jimi****)</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>2021.07.21 12:24</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="parent.clickcr(this, \'ara.report\', \'\', \'\', event); common.report(\'false\',\'jimi****\', \'QrmNRu7YiQTTOVMpcc9NQavCaX57YKLQHQPwaE7HZaQ=\', \'이쁜캐릭터들 재미와 감동입니다. \', \'17608959\', \'point_after\', false);return false;" class="go_report2"><em>신고</em></a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</dl>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="btn_area">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_sympathyButton" href="#" onclick="parent.clickcr(this, \'ara.sym\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_up"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="sympathy_17608959">57</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, \'ara.opp\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_down"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">비공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="notSympathy_17608959">4</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t</li>\r\n\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t<li>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="star_score">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="score_reple">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_viewer">관람객</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<!-- 스포일러 컨텐츠로 처리되는지 여부 -->\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span id="_filtered_ment_8">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t조카랑 같이 봤는데 조카 보여주러 갔다가 제가 더 재밌게 보고 오네요~가족 모두 즐기기 좋은 영화입니다!! \r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<dl>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="javascript:showPointListByNid(17608750, \'after\');parent.clickcr(this, \'ara.uid\', \'\', \'\', event); return false;" target="_top">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<span>살쾡이(wild****)</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>2021.07.21 09:56</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="parent.clickcr(this, \'ara.report\', \'\', \'\', event); common.report(\'false\',\'wild****\', \'KyulPdi7YXz3ro8yewlLm2Fu0XYw5/hBGwpUQSvMPM0=\', \'조카랑 같이 봤는데 조카 보여주러 갔다가 제가 더 재밌게 보고 오네요~가족 모두 즐기기 좋은 영화입니다!! \', \'17608750\', \'point_after\', false);return false;" class="go_report2"><em>신고</em></a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</dl>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="btn_area">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_sympathyButton" href="#" onclick="parent.clickcr(this, \'ara.sym\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_up"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="sympathy_17608750">57</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, \'ara.opp\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_down"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">비공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="notSympathy_17608750">8</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t</li>\r\n\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t<li class=\'last\'>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="star_score">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="score_reple">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_viewer">관람객</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<!-- 스포일러 컨텐츠로 처리되는지 여부 -->\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span id="_filtered_ment_9">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t1편 재밌게 봤으면 2푠도 꼭 보셈 ㅇㅇ 존꿀잼ㅋㅋㅋㅋ 보스베이비 볼수록 빠져드는 매력이 있음 \r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</p>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<dl>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="javascript:showPointListByNid(17608957, \'after\');parent.clickcr(this, \'ara.uid\', \'\', \'\', event); return false;" target="_top">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<span>피노코(pino****)</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t<em>2021.07.21 12:22</em>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dt>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t<a href="#" onclick="parent.clickcr(this, \'ara.report\', \'\', \'\', event); common.report(\'false\',\'pino****\', \'WOjt7+8tYP/zJdIvjzu2O0MTAabgXoAGPdh8jZ2320g=\', \'1편 재밌게 봤으면 2푠도 꼭 보셈 ㅇㅇ 존꿀잼ㅋㅋㅋㅋ 보스베이비 볼수록 빠져드는 매력이 있음 \', \'17608957\', \'point_after\', false);return false;" class="go_report2"><em>신고</em></a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t</dd>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</dl>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t\t<div class="btn_area">\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_sympathyButton" href="#" onclick="parent.clickcr(this, \'ara.sym\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_up"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="sympathy_17608957">48</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t\t<a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, \'ara.opp\', \'\', \'\', event);">\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="ico_down"></span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<span class="blind">비공감</span>\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t<strong class="notSympathy_17608957">6</strong>\r\n\t\t\t\t\t\t\t\t\t\t\t\t</a>\r\n\t\t\t\t\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t\t\t\t\t</li>\r\n\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t</ul>\r\n\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t<div class="paging">\r\n\t\t\t\t\t\t\t<div>\r\n\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t\t<a id="pagerTagAnchor1" href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=1" onclick="parent.clickcr(this, \'ara.page\', \'\', \'\', event);"><span class="on">1</span></a>\n<a id="pagerTagAnchor2" href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=2" onclick="parent.clickcr(this, \'ara.page\', \'\', \'\', event);"><span>2</span></a>\n<a id="pagerTagAnchor3" href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=3" onclick="parent.clickcr(this, \'ara.page\', \'\', \'\', event);"><span>3</span></a>\n<a id="pagerTagAnchor4" href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=4" onclick="parent.clickcr(this, \'ara.page\', \'\', \'\', event);"><span>4</span></a>\n<a id="pagerTagAnchor5" href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=5" onclick="parent.clickcr(this, \'ara.page\', \'\', \'\', event);"><span>5</span></a>\n<a id="pagerTagAnchor6" href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=6" onclick="parent.clickcr(this, \'ara.page\', \'\', \'\', event);"><span>6</span></a>\n<a id="pagerTagAnchor7" href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=7" onclick="parent.clickcr(this, \'ara.page\', \'\', \'\', event);"><span>7</span></a>\n<a id="pagerTagAnchor8" href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=8" onclick="parent.clickcr(this, \'ara.page\', \'\', \'\', event);"><span>8</span></a>\n<a id="pagerTagAnchor9" href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=9" onclick="parent.clickcr(this, \'ara.page\', \'\', \'\', event);"><span>9</span></a>\n<a id="pagerTagAnchor10" href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=10" onclick="parent.clickcr(this, \'ara.page\', \'\', \'\', event);"><span>10</span></a>\n\r\n\t\t\t\t\t\t\t\t<a id="pagerTagAnchor2" href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=2" title="다음" class="pg_next" onclick="parent.clickcr(this, \'ara.page\', \'\', \'\', event);"><em>다음</em></a>\n\r\n\t\t\t\t\t\t\t\t\t\t\t\t\t\r\n\t\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\t</div>\r\n\t\t\t\t\t\r\n\t\t\t\t\r\n\t\t\t\r\n\t\t\r\n\t</div>\r\n</div>\r\n<!-- //content -->\r\n<form id="reportForm" name="reportForm1" method="POST" accept-charset="utf-8"></form>\r\n<script type="text/javascript">\r\n\r\n\r\nif (false == false && "after" == "after" && false) {\r\n\tif (true && false) {\r\n\t\tjindo.$Element("pointWriteExecuteLayer").show();\r\n\t} else if (false && true) {\r\n\t\tjindo.$Element("actualPointWriteExecuteLayer").show();\r\n\t}\r\n}\r\n\r\nvar oElActualPointWriteExecuteLayer = jindo.$Element("actualPointWriteExecuteLayer");\r\n\r\nif (oElActualPointWriteExecuteLayer != null && oElActualPointWriteExecuteLayer != "undefined") {\r\n\r\n\t\r\n\tjindo.$Element("actualPointWriteExecuteLayerOkButton").attach("click", function(e){\r\n\t\te.stop(jindo.$Event.CANCEL_DEFAULT);\r\n\t\tjindo.$Element("actualPointWriteExecuteLayer").hide();\r\n\t\trefreshPage();\r\n\t});\r\n\t\r\n\t\r\n\t\r\n\tjindo.$Element("actualPointWriteExecuteLayerCloseButton").attach("click", function(e){\r\n\t\te.stop(jindo.$Event.CANCEL_DEFAULT);\r\n\t\tjindo.$Element("actualPointWriteExecuteLayer").hide();\r\n\t\trefreshPage();\r\n\t});\r\n\t\r\n}\r\n\r\nvar oElPointWriteExecuteLayer = jindo.$Element("pointWriteExecuteLayer");\r\n\r\nif (oElPointWriteExecuteLayer != null && oElPointWriteExecuteLayer != "undefined") {\r\n\t\r\n\tjindo.$Element("pointWriteExecuteLayerOkButton").attach("click", function(e){\r\n\t\te.stop(jindo.$Event.CANCEL_DEFAULT);\r\n\t\tjindo.$Element("pointWriteExecuteLayer").hide();\r\n\t\trefreshPage();\r\n\t});\r\n\t\r\n\t\r\n\t\r\n\tjindo.$Element("pointWriteExecuteLayerCloseButton").attach("click", function(e){\r\n\t\te.stop(jindo.$Event.CANCEL_DEFAULT);\r\n\t\tjindo.$Element("pointWriteExecuteLayer").hide();\r\n\t\trefreshPage();\r\n\t});\r\n\t\r\n}\r\n\r\njindo.$Fn(function () {\r\n\r\n  function checkboxHandlerFactory(isActualYn, isLabel) {\r\n    return function() {\r\n      var actualYnChecked = jindo.$("actualYnCheckBox").checked;\r\n      var spoilerYnChecked = jindo.$("spoilerYnCheckBox").checked;\r\n\r\n      // convert\r\n      actualYnChecked = (isActualYn && isLabel)? !actualYnChecked : actualYnChecked;\r\n      spoilerYnChecked = (!isActualYn && isLabel)? !spoilerYnChecked : spoilerYnChecked;\r\n\r\n      location.href = "/movie/bi/mi/pointWriteFormList.naver?code=191597&type=after&onlyActualPointYn=" + (actualYnChecked? "Y" : "N")  + "&onlySpoilerPointYn=" + (spoilerYnChecked? "Y" : "N") + "&order=sympathyScore";\r\n    };\r\n  }\r\n\r\n\t\r\n\tif (jindo.$("actualYnCheckBox") != null) {\r\n\t\tjindo.$Fn(checkboxHandlerFactory(true, false), this).attach(jindo.$("actualYnCheckBox"), \'click\');\r\n\t}\r\n\r\n\tif (jindo.$("actualYnLable") != null) {\r\n\t    jindo.$Fn(checkboxHandlerFactory(true, true), this).attach(jindo.$("actualYnLable"), \'click\');\r\n\t}\r\n\r\n\t\r\n    if (jindo.$("spoilerYnCheckBox") != null) {\r\n    jindo.$Fn(checkboxHandlerFactory(false, false), this).attach(jindo.$("spoilerYnCheckBox"), \'click\');\r\n  }\r\n\r\n  if (jindo.$("spoilerYnLable") != null) {\r\n    jindo.$Fn(checkboxHandlerFactory(false, true), this).attach(jindo.$("spoilerYnLable"), \'click\');\r\n  }\r\n\t\r\n\t\r\n\tif (jindo.$Element("actualPointHelp") != null && jindo.$Element("actualPointHelp") != "undefined") {\r\n\t\tactualPointHelpLayerToggle = function() {\r\n\t\t\tsetTimeout( function() {\r\n\t\t\t\tif (document.activeElement != null) {\r\n\t\t\t\t\tvar focusedEl = jindo.$Element(document.activeElement);\r\n\t\t\t\t\tif (focusedEl != null) {\r\n\t\t\t\t\t\tif ( !focusedEl.hasClass("_actualPointHelp") ) {\r\n\t\t\t\t\t\t\t jindo.$Element("actualPointHelp").hide();\r\n\t\t\t\t\t\t}\r\n\t\t\t\t\t}\r\n\t\t\t\t}\r\n\t\t\t}, 100);\r\n\t\t};\r\n\r\n\t\tnew jindo.LayerManager("actualPointHelp", {\r\n\t\t\tsCheckEvent : "click",\r\n\t\t\tnHideDelay : 0\r\n\t\t}).link(jindo.$("actualPointHelp"), jindo.$("actualPointHelpButton"));\r\n\r\n\t\tjindo.$Element("actualPointHelpButton").attach("click", function(e){\r\n\t\t\te.stop(jindo.$Event.CANCEL_DEFAULT);\r\n\t\t\tjindo.$Element("actualPointHelp").toggle();\r\n\t\t});\r\n\r\n\t\tjindo.$Element("actualPointHelpCloseButton").attach("click", function(e){\r\n\t\t\te.stop(jindo.$Event.CANCEL_DEFAULT);\r\n\t\t\tjindo.$Element("actualPointHelp").toggle();\r\n\t\t});\r\n\r\n\t\t\r\n\t\tvar waelActualPointHelp = jindo.$ElementList(\'._actualPointHelp\');\r\n\r\n\t\tjindo.$A(waelActualPointHelp.$value()).forEach(function(value, index, array) {\r\n\t\t\tjindo.$Fn(actualPointHelpLayerToggle, this).attach(value, "blur");\r\n\t\t});\r\n\t}\r\n\r\n\tparent.setParamForPointAfterList(\'N\', \'sympathyScore\', \'1\');\r\n\tparent.resizePointAfterListIframe(0);\r\n\r\n\t// 최소 높이 270px 지정\r\n\tvar frameHeight = eval(jindo.$Document().scrollSize().height);\r\n\r\n\tparent.resizePointAfterListIframe(frameHeight);\r\n\tparent.isPointAfterListLoad = true;\r\n\t\r\n\t\r\n\t\r\n\r\n  var isCheckPointExist = false;\r\n  // TODO: fix below condition\r\n  if (false) {\r\n    if(jindo.$Element("open-form-btn")) {\r\n\t\tjindo.$Element("open-form-btn").attach("click", function(e) {\r\n\t\t  \t\r\n\t\t  \t\r\n\t\t\t\r\n\t\t  if (true && false && isCheckPointExist == false) {\r\n\t\t\tparent.point.checkPointAfterExistAndMileageSubscriptionType();\r\n\t\t  }\r\n\t\t  isCheckPointExist = true;\r\n\t\t  parent.openPointWriteForm();\r\n\t\t});\r\n    }\r\n  } else {\r\n    if (jindo.$Element("open-form-btn")) {\r\n\t\tjindo.$Element("open-form-btn").attach("click", function (e) {\r\n\t\t  common.checkLogin(false);\r\n\t\t});\r\n    }\r\n  }\r\n\r\n  var isHide = true;\r\n\r\n  if(jindo.$Element("eval-edit")) {\r\n    jindo.$Element("eval-edit").attach("click", function() {\r\n\t\t  var edit = jindo.$Element("ly-edit");\r\n\t  \tisHide ? edit.show() : edit.hide();\r\n      \tisHide = !isHide;\r\n    })\r\n  }\r\n  parent.resizePointAfterListIframeOnLoad();\r\n}, this).attach(this, \'load\');\r\n\r\nvar point = {\r\n\t\tcheckLoginWithMessage : function(login, loginMessage, notLoginMessage) {\r\n\t\t\tif(login == false){\r\n\t\t\t\tif(confirm(message)){\r\n\t\t\t\t\ttop.location.href="https://nid.naver.com/nidlogin.login?mode=form&url="+encodeURIComponent(top.location.href);\r\n\t\t\t\t}\r\n\t\t\t\treturn false;\r\n\t\t\t}\r\n\t\t\treturn true;\r\n\t\t},\r\n\r\n\t\t\r\n\t\tcheckAlreadyPointAfterExist : function (nid) {\r\n\t\t\tvar existPointType = "pointBefore";\r\n\t\t\t\r\n\t\t\tif (false == false) {\r\n\t\t\t\tvar oAjax = new jindo.$Ajax("/api/internal/point/pointAfterExistJson.naver", {\r\n\t\t\t    \tonload : function (oRes) {\r\n\t\t\t    \t\tvar resultCode = oRes.json().resultCode;\r\n\t\t\t    \t\t\r\n\t\t\t    \t\tif (resultCode == "error") {\t\t\t\t\t\t\t// 서버 오류\r\n\t\t\t    \t\t\talert("오류가 발생했습니다. 잠시 후 다시 시도해주세요.");\r\n\t\t\t    \t\t\treturn false;\r\n\t\t\t    \t\t} else {\r\n\t\t\t    \t\t\texistPointType = oRes.json().existPointType;\r\n\t\t\t    \t\t\tpoint.del(existPointType, nid);\r\n\t\t\t    \t\t}\r\n\t\t\t    \t},\r\n\t\t\t\t\ttimeout : 5,\r\n\t\t\t    \tonerror : function (oRes) {\r\n\t\t\t    \t\talert("오류가 발생했습니다. 잠시 후 다시 시도해주세요.");\r\n\t\t\t    \t\treturn false;\r\n\t\t\t    \t},\r\n\t\t\t    \tontimeout : function (oRes) {\r\n\t\t\t    \t\talert("처리가 지연되고 있습니다. 다시 시도해주세요.");\r\n\t\t\t    \t\treturn false;\r\n\t\t\t    \t}\r\n\t\t\t    });\r\n\t\t\t    \r\n\t\t\t    oAjax.request({\r\n\t\t\t    \t"movieCode" : "191597",\r\n\t\t\t    \t"isActualPoint" : "false"\r\n\t\t\t    });\r\n\t\t\t} else {\r\n\t\t\t\tpoint.del(existPointType, nid);\r\n\t\t\t}\r\n\t\t},\r\n\r\n\t\tdel : function (existPointType, nid) {\r\n\t\t\tif (existPointType == "actualPoint") {\r\n\t\t\t\tif (confirm("관람객 평점 삭제시, 평점 작성으로 적립된 포인트는 회수됩니다. 평점을 삭제할까요?") == false) {\r\n\t\t\t\t\treturn false;\r\n\t\t\t\t}\r\n\t\t\t} else {\r\n\t\t\t\tif (confirm("본인 삭제 시 복구할 수 없습니다.\\n평점을 삭제하시겠습니까?") == false) {\r\n\t\t\t\t\treturn false;\r\n\t\t\t\t}\r\n\t\t\t}\r\n\t\t\t\r\n\t\t\tvar ajaxDeleteUrl = "/api/internal/point/pointBeforeDelete.naver";\r\n\t\t\tif ("after" == "after") {\r\n\t\t\t\tajaxDeleteUrl = "/api/internal/point/pointAfterDelete.naver";\r\n\t\t\t}\r\n\t\t\t\r\n\t\t\tvar ajax = new jindo.$Ajax(ajaxDeleteUrl, { \r\n\t\t\t\tmethod : "POST",\r\n\t\t\t\tasync : false,\r\n\t\t\t\tonload : this.delCallback\r\n\t\t\t});\r\n\t\t\tajax.header("ajax", "true");\r\n\t\t\tajax.request({\r\n\t\t\t\t"nid":nid\r\n\t\t\t});\r\n\t\t},\r\n\t\t\r\n\t\tdelCallback : function(req) {\r\n\t\t\tvar returnValue = req.text();\r\n\t\t\t\r\n\t\t\tif(returnValue != "success"){\r\n\t\t\t\talert(returnValue);\r\n\t\t\t\treturn false;\r\n\t\t\t}\r\n\t\t\t\r\n\t\t\ttop.location.href = \'/movie/bi/mi/point.naver?code=191597#pointAfterTab\';\r\n\t\t\ttop.location.reload(true);\r\n\t\t}\r\n};\r\n\r\nfunction dislplayOrder(order) {\r\n\tvar url = "/movie/bi/mi/pointWriteFormList.naver?code=191597&type=after";\r\n\t\r\n\tvar onlyActualPointYnValue = jindo.$("onlyActualPointYn").value;\r\n  \tvar includeSpoilerYnValue = jindo.$("includeSpoilerYn").value;\r\n\r\n\tif (onlyActualPointYnValue != "") {\r\n\t\turl = url + "&onlyActualPointYn=" + onlyActualPointYnValue;\r\n\t}\r\n\r\n\tif (includeSpoilerYnValue != "") {\r\n      url = url + "&onlySpoilerPointYn=" + includeSpoilerYnValue;\r\n    }\r\n\t\r\n\turl = url + "&order=" + order;\r\n\t\r\n\tlocation.href = document.location.protocol + "//" + document.domain + url;\r\n}\r\n\r\nfunction showPointListByNid(nid, target){\r\n\tif (target == \'after\') {\r\n\t\ttop.location.href = top.location.protocol + "//" + top.location.hostname + "/movie/point/af/list.naver?st=nickname&target=after&sword="+nid;\r\n\t} else {\r\n\t\ttop.location.href = top.location.protocol + "//" + top.location.hostname + "/movie/point/af/list.naver?st=nickname_before&target=before&sword="+nid;\r\n\t}\r\n}\r\n\r\nfunction refreshPage() {\r\n\t \r\n\ttop.location.href = \'/movie/bi/mi/point.naver?code=191597#pointAfterTab\';\r\n}\r\n\r\n// 스포일러 감상평 내용 보기\r\nfunction showMovieReview(rvwIdx) {\r\n    jindo.$Element(\'_text_spo_\' + rvwIdx).css(\'display\', \'none\');\r\n    jindo.$Element(\'_filtered_ment_\' + rvwIdx).css(\'display\', \'block\');\r\n    parent.resizePointAfterListIframeOnLoad();\r\n}\r\n\r\n// 감상평 펼쳐보기\r\nfunction unfoldPointMent(obj) {\r\n    var fullMent = jindo.$Element(obj).attr("data-src");\r\n    jindo.$Element(obj).parent().html(fullMent);\r\n    parent.resizePointAfterListIframeOnLoad();\r\n}\r\n\r\n\r\n</script>\r\n\r\n\r\n<script type="text/javascript">\r\n\tvar sympathy = new Sympathy("191597", "after");\r\n</script>\r\n\t\r\n\t\r\n\t\t<script type="text/javascript">\r\n            jindo.$Fn(function() {\r\n                try{ lcs_do(); } catch(e){}\r\n            }).attach(window, "pageshow");\r\n\t\t</script>\r\n\t\r\n</body>\r\n</html>'




```python
# BeautifulSoup을 이용해서 파싱
# 불러온 html source를 "lxml"(기준) parser로 parsing
soup = BeautifulSoup(resp.text, 'lxml')
soup
```




    <!DOCTYPE html>
    <html lang="ko">
    <head>
    <meta charset="utf-8"/>
    <meta content="IE=edge" http-equiv="X-UA-Compatible"/>
    <title>네이버 영화</title>
    <link href="https://ssl.pstatic.net/static/m/movie/icons/naver_movie_favicon.ico" rel="shortcut icon" type="image/x-icon"/>
    <link href="/css/common.css?20210728101151" rel="stylesheet" type="text/css"/>
    <link href="/css/movie_tablet.css?20210728101151" rel="stylesheet" type="text/css"/>
    <link href="/css/movie_end.css?20210728101151" rel="stylesheet" type="text/css"/>
    <script src="/js/deploy/movie.all.js?20210728101151" type="text/javascript"></script>
    </head>
    <body>
    <!-- content -->
    <input id="movieCode" name="movieCode" type="hidden" value="191597"/>
    <input id="onlyActualPointYn" name="onlyActualPointYn" type="hidden" value="N"/>
    <input id="includeSpoilerYn" name="includeSpoilerYn" type="hidden" value="N"/>
    <input id="order" name="order" type="hidden" value="sympathyScore"/>
    <input id="page" name="page" type="hidden" value="1"/>
    <div class="ifr_area basic_ifr">
    <div class="input_netizen">
    <!-- [D] 관람객 평점 작성 완료 -->
    <div class="ly_viewer" id="actualPointWriteExecuteLayer" style="display:none">
    <h4>관람객 평점 작성 완료 안내</h4>
    <p>관람객 평점이 등록되었습니다.<br/><em>네이버페이 포인트 500원</em>이 적립되었습니다.<br/><em>7일 이후</em> 확인 가능합니다.</p>
    <p>(평점 삭제시, 적립된 포인트는 회수됩니다.)</p>
    <div class="btn">
    <a class="ok" href="#" id="actualPointWriteExecuteLayerOkButton">확인</a>
    <a class="close" href="#" id="actualPointWriteExecuteLayerCloseButton" title="닫기">관람객 평점 작성 완료 안내 레이어 닫기</a>
    </div>
    </div>
    <!-- //관람객 평점 작성 완료 -->
    <!-- [D] 관람객 평점 작성 완료2 -->
    <div class="ly_viewer" id="pointWriteExecuteLayer" style="display:none">
    <h4>관람객 평점 작성 완료 안내</h4>
    <p class="msg1">관람객 평점이 등록되었습니다.</p>
    <div class="btn">
    <a class="ok" href="#" id="pointWriteExecuteLayerOkButton">확인</a>
    <a class="close" href="#" id="pointWriteExecuteLayerCloseButton" title="닫기">관람객 평점 작성 완료 안내 레이어 닫기</a>
    </div>
    </div>
    <!-- //관람객 평점 작성 완료2 -->
    <div class="score_total">
    <strong class="total">관람객 평점 <em>1,260</em>건<button class="btn_review" id="open-form-btn">내 평점 등록</button></strong>
    </div>
    <div class="top_behavior" id="orderCheckbox">
    <ul class="sorting_list">
    <li class="on"><a href="#" onclick="parent.clickcr(this, 'ara.bysym', '', '', event); dislplayOrder('sympathyScore');">공감순</a></li>
    <li><a href="#" onclick="parent.clickcr(this, 'ara.byrct', '', '', event); dislplayOrder('newest');">최신순</a></li>
    <li><a href="#" onclick="parent.clickcr(this, 'ara.high', '', '', event); dislplayOrder('highest');">평점 높은 순</a></li>
    <li><a href="#" onclick="parent.clickcr(this, 'ara.low', '', '', event); dislplayOrder('lowest');">평점 낮은 순</a></li>
    </ul>
    <ul class="quarter_mode">
    <li>
    <input class="blind" id="spoilerYnCheckBox" name="spilerViewer" onclick="parent.clickcr(this,'','','',event); return false;" title="스포일러 보기" type="checkbox"/>
    <label class="label_viewer" for="spoilerYnCheckBox" id="spoilerYnLable">스포일러 보기</label>
    </li>
    <li>
    <input class="blind" id="actualYnCheckBox" name="viewer" onclick="parent.clickcr(this,'ura.mgs','','',event); return false;" title="관람객 평점만 보기" type="checkbox"/>
    <label class="label_viewer" for="actualYnCheckBox" id="actualYnLable">관람객 평점만 보기</label>
    <a class="help _actualPointHelp" href="#" id="actualPointHelpButton" title="도움말">관람객 평점만 보기 도움말</a>
    <div class="ly_help _actualPointHelp" id="actualPointHelp" style="display:none">
    <h5>관람객평점만 보기 안내 레이어</h5>
    <div class="ly_cont _actualPointHelp">
    <p>네이버 영화에서 예매하신 고객님들이<br/> 영화 관람 후 등록해주신 평점입니다.</p>
    </div>
    <button class="btn_close _actualPointHelp" id="actualPointHelpCloseButton" title="닫기" type="button"><span class="blind">관람객 평점만 보기 안내 레이어 닫기</span></button>
    <span class="arr _actualPointHelp"></span>
    </div>
    </li>
    </ul>
    </div>
    <div class="score_result">
    <ul>
    <li>
    <div class="star_score">
    <span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>
    </div>
    <div class="score_reple">
    <p>
    <span class="ico_viewer">관람객</span>
    <!-- 스포일러 컨텐츠로 처리되는지 여부 -->
    <span id="_filtered_ment_0">
    														
    															
    															
    																가볍게 보러갔다가 감동까지 느끼고 옵니다올해 봤던 애니메이션 중에 제일 재밌게 봤네요 
    															
    														
    														
    													</span>
    </p>
    <dl>
    <dt>
    <em>
    <a href="#" onclick="javascript:showPointListByNid(17609059, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
    <span>교님(sair****)</span>
    </a>
    </em>
    <em>2021.07.21 13:23</em>
    </dt>
    <dd>
    <a class="go_report2" href="#" onclick="parent.clickcr(this, 'ara.report', '', '', event); common.report('false','sair****', 'EH5RD/0UjVTJFwFPo+/6t3aHLqqTuy4JcllxXjuA7Hs=', '가볍게 보러갔다가 감동까지 느끼고 옵니다올해 봤던 애니메이션 중에 제일 재밌게 봤네요 ', '17609059', 'point_after', false);return false;"><em>신고</em></a>
    </dd>
    </dl>
    </div>
    <div class="btn_area">
    <a class="_sympathyButton" href="#" onclick="parent.clickcr(this, 'ara.sym', '', '', event);">
    <span class="ico_up"></span>
    <span class="blind">공감</span>
    <strong class="sympathy_17609059">154</strong>
    </a>
    <a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, 'ara.opp', '', '', event);">
    <span class="ico_down"></span>
    <span class="blind">비공감</span>
    <strong class="notSympathy_17609059">13</strong>
    </a>
    </div>
    </li>
    <li>
    <div class="star_score">
    <span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>
    </div>
    <div class="score_reple">
    <p>
    <span class="ico_viewer">관람객</span>
    <!-- 스포일러 컨텐츠로 처리되는지 여부 -->
    <span id="_filtered_ment_1">
    														
    															
    															
    																귀여워서 심장 아픔ㅠㅠ 세상에 이런 보스라면 매일 출근하궤쒀!! 
    															
    														
    														
    													</span>
    </p>
    <dl>
    <dt>
    <em>
    <a href="#" onclick="javascript:showPointListByNid(17608937, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
    <span>끼야오루(hell****)</span>
    </a>
    </em>
    <em>2021.07.21 12:09</em>
    </dt>
    <dd>
    <a class="go_report2" href="#" onclick="parent.clickcr(this, 'ara.report', '', '', event); common.report('false','hell****', 'RJVAVhjVU7BtAcRp4P8quyu3IfxHoaqdPGIqo9hePb4=', '귀여워서 심장 아픔ㅠㅠ 세상에 이런 보스라면 매일 출근하궤쒀!! ', '17608937', 'point_after', false);return false;"><em>신고</em></a>
    </dd>
    </dl>
    </div>
    <div class="btn_area">
    <a class="_sympathyButton" href="#" onclick="parent.clickcr(this, 'ara.sym', '', '', event);">
    <span class="ico_up"></span>
    <span class="blind">공감</span>
    <strong class="sympathy_17608937">128</strong>
    </a>
    <a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, 'ara.opp', '', '', event);">
    <span class="ico_down"></span>
    <span class="blind">비공감</span>
    <strong class="notSympathy_17608937">9</strong>
    </a>
    </div>
    </li>
    <li>
    <div class="star_score">
    <span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>
    </div>
    <div class="score_reple">
    <p>
    <span class="ico_viewer">관람객</span>
    <!-- 스포일러 컨텐츠로 처리되는지 여부 -->
    <span id="_filtered_ment_2">
    														
    															
    															
    																귀여운 아가보스가 두명이나 나오는데 비주얼적으로 안볼수가 없지요!!^^ 1편도 재미있게 봤었는데, 2편은 더더 경쾌하고 액션 넘쳐서 즐겁게 볼수 있었어요. 이 사랑스러운 영화 추천합니다!! 
    															
    														
    														
    													</span>
    </p>
    <dl>
    <dt>
    <em>
    <a href="#" onclick="javascript:showPointListByNid(17609015, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
    <span>pian****</span>
    </a>
    </em>
    <em>2021.07.21 13:02</em>
    </dt>
    <dd>
    <a class="go_report2" href="#" onclick="parent.clickcr(this, 'ara.report', '', '', event); common.report('false','pian****', 'A5c6H+fI0x/lUBdyp3qESiPDdcKM9wN2nqUj0IHyUwY=', '귀여운 아가보스가 두명이나 나오는데 비주얼적으로 안볼수가 없지요!!^^ 1편도 재미있게 봤었는데, 2편은 더더 경쾌하고 액션 넘쳐서 즐겁게 볼수 있었어요. 이 사랑스러운 영화 추천합니다!! ', '17609015', 'point_after', false);return false;"><em>신고</em></a>
    </dd>
    </dl>
    </div>
    <div class="btn_area">
    <a class="_sympathyButton" href="#" onclick="parent.clickcr(this, 'ara.sym', '', '', event);">
    <span class="ico_up"></span>
    <span class="blind">공감</span>
    <strong class="sympathy_17609015">96</strong>
    </a>
    <a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, 'ara.opp', '', '', event);">
    <span class="ico_down"></span>
    <span class="blind">비공감</span>
    <strong class="notSympathy_17609015">3</strong>
    </a>
    </div>
    </li>
    <li>
    <div class="star_score">
    <span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>
    </div>
    <div class="score_reple">
    <p>
    <span class="ico_viewer">관람객</span>
    <!-- 스포일러 컨텐츠로 처리되는지 여부 -->
    <span id="_filtered_ment_3">
    														
    															
    															
    																너무 사랑스럽잖아!!ㅠㅠㅠ 진짜 1편도 다섯번정도 봤는데 앞으로 계속 나와줘라 
    															
    														
    														
    													</span>
    </p>
    <dl>
    <dt>
    <em>
    <a href="#" onclick="javascript:showPointListByNid(17608958, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
    <span>두여름(gaeu****)</span>
    </a>
    </em>
    <em>2021.07.21 12:23</em>
    </dt>
    <dd>
    <a class="go_report2" href="#" onclick="parent.clickcr(this, 'ara.report', '', '', event); common.report('false','gaeu****', '9z/I8YCOkhg4rW8A/dm9CPhUkSI2sk+URX4E+SfeRa0=', '너무 사랑스럽잖아!!ㅠㅠㅠ 진짜 1편도 다섯번정도 봤는데 앞으로 계속 나와줘라 ', '17608958', 'point_after', false);return false;"><em>신고</em></a>
    </dd>
    </dl>
    </div>
    <div class="btn_area">
    <a class="_sympathyButton" href="#" onclick="parent.clickcr(this, 'ara.sym', '', '', event);">
    <span class="ico_up"></span>
    <span class="blind">공감</span>
    <strong class="sympathy_17608958">87</strong>
    </a>
    <a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, 'ara.opp', '', '', event);">
    <span class="ico_down"></span>
    <span class="blind">비공감</span>
    <strong class="notSympathy_17608958">3</strong>
    </a>
    </div>
    </li>
    <li>
    <div class="star_score">
    <span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>
    </div>
    <div class="score_reple">
    <p>
    <span class="ico_viewer">관람객</span>
    <!-- 스포일러 컨텐츠로 처리되는지 여부 -->
    <span id="_filtered_ment_4">
    														
    															
    															
    																귀여운 캐릭터로 기분까지 좋아지는 영화 
    															
    														
    														
    													</span>
    </p>
    <dl>
    <dt>
    <em>
    <a href="#" onclick="javascript:showPointListByNid(17608908, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
    <span>sed1834(xxx7****)</span>
    </a>
    </em>
    <em>2021.07.21 11:53</em>
    </dt>
    <dd>
    <a class="go_report2" href="#" onclick="parent.clickcr(this, 'ara.report', '', '', event); common.report('false','xxx7****', 'Lwx11rZo7j/HiQDsuldQdYOjYUs3d6u6jy9+E8NirtI=', '귀여운 캐릭터로 기분까지 좋아지는 영화 ', '17608908', 'point_after', false);return false;"><em>신고</em></a>
    </dd>
    </dl>
    </div>
    <div class="btn_area">
    <a class="_sympathyButton" href="#" onclick="parent.clickcr(this, 'ara.sym', '', '', event);">
    <span class="ico_up"></span>
    <span class="blind">공감</span>
    <strong class="sympathy_17608908">81</strong>
    </a>
    <a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, 'ara.opp', '', '', event);">
    <span class="ico_down"></span>
    <span class="blind">비공감</span>
    <strong class="notSympathy_17608908">3</strong>
    </a>
    </div>
    </li>
    <li>
    <div class="star_score">
    <span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>
    </div>
    <div class="score_reple">
    <p>
    <span class="ico_viewer">관람객</span>
    <!-- 스포일러 컨텐츠로 처리되는지 여부 -->
    <span id="_filtered_ment_5">
    														
    															
    															
    																겁나 귀엽고 웃김ㅋㅋㅋㅋ 혼자 기분전환할겸 봤는데 친구 데리고 한번 더 보고싶음 꿀잼... 베이비들 넘 귀엽~~~!! 
    															
    														
    														
    													</span>
    </p>
    <dl>
    <dt>
    <em>
    <a href="#" onclick="javascript:showPointListByNid(17608761, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
    <span>eclipse(doct****)</span>
    </a>
    </em>
    <em>2021.07.21 10:07</em>
    </dt>
    <dd>
    <a class="go_report2" href="#" onclick="parent.clickcr(this, 'ara.report', '', '', event); common.report('false','doct****', 'zzXA7/IWLojqP4xpush0pF86D4Ll73zVk/x4+92t2Ak=', '겁나 귀엽고 웃김ㅋㅋㅋㅋ 혼자 기분전환할겸 봤는데 친구 데리고 한번 더 보고싶음 꿀잼... 베이비들 넘 귀엽~~~!! ', '17608761', 'point_after', false);return false;"><em>신고</em></a>
    </dd>
    </dl>
    </div>
    <div class="btn_area">
    <a class="_sympathyButton" href="#" onclick="parent.clickcr(this, 'ara.sym', '', '', event);">
    <span class="ico_up"></span>
    <span class="blind">공감</span>
    <strong class="sympathy_17608761">75</strong>
    </a>
    <a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, 'ara.opp', '', '', event);">
    <span class="ico_down"></span>
    <span class="blind">비공감</span>
    <strong class="notSympathy_17608761">8</strong>
    </a>
    </div>
    </li>
    <li>
    <div class="star_score">
    <span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>
    </div>
    <div class="score_reple">
    <p>
    <span class="ico_viewer">관람객</span>
    <!-- 스포일러 컨텐츠로 처리되는지 여부 -->
    <span id="_filtered_ment_6">
    														
    															
    															
    																베이비 넘모 귀엽고,, 사랑스러움 ㅠㅠ 심장에 무리 올뻔 &gt;_&lt; 
    															
    														
    														
    													</span>
    </p>
    <dl>
    <dt>
    <em>
    <a href="#" onclick="javascript:showPointListByNid(17608923, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
    <span>Quin(suen****)</span>
    </a>
    </em>
    <em>2021.07.21 12:01</em>
    </dt>
    <dd>
    <a class="go_report2" href="#" onclick="parent.clickcr(this, 'ara.report', '', '', event); common.report('false','suen****', 'j8Ll6mAOIRpa0xarR1OBP1NfiCiHmUAekbTI+ZlAw/Y=', '베이비 넘모 귀엽고,, 사랑스러움 ㅠㅠ 심장에 무리 올뻔 &gt;_&lt; ', '17608923', 'point_after', false);return false;"><em>신고</em></a>
    </dd>
    </dl>
    </div>
    <div class="btn_area">
    <a class="_sympathyButton" href="#" onclick="parent.clickcr(this, 'ara.sym', '', '', event);">
    <span class="ico_up"></span>
    <span class="blind">공감</span>
    <strong class="sympathy_17608923">64</strong>
    </a>
    <a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, 'ara.opp', '', '', event);">
    <span class="ico_down"></span>
    <span class="blind">비공감</span>
    <strong class="notSympathy_17608923">2</strong>
    </a>
    </div>
    </li>
    <li>
    <div class="star_score">
    <span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>
    </div>
    <div class="score_reple">
    <p>
    <span class="ico_viewer">관람객</span>
    <!-- 스포일러 컨텐츠로 처리되는지 여부 -->
    <span id="_filtered_ment_7">
    														
    															
    															
    																이쁜캐릭터들 재미와 감동입니다. 
    															
    														
    														
    													</span>
    </p>
    <dl>
    <dt>
    <em>
    <a href="#" onclick="javascript:showPointListByNid(17608959, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
    <span>123erjjerf(jimi****)</span>
    </a>
    </em>
    <em>2021.07.21 12:24</em>
    </dt>
    <dd>
    <a class="go_report2" href="#" onclick="parent.clickcr(this, 'ara.report', '', '', event); common.report('false','jimi****', 'QrmNRu7YiQTTOVMpcc9NQavCaX57YKLQHQPwaE7HZaQ=', '이쁜캐릭터들 재미와 감동입니다. ', '17608959', 'point_after', false);return false;"><em>신고</em></a>
    </dd>
    </dl>
    </div>
    <div class="btn_area">
    <a class="_sympathyButton" href="#" onclick="parent.clickcr(this, 'ara.sym', '', '', event);">
    <span class="ico_up"></span>
    <span class="blind">공감</span>
    <strong class="sympathy_17608959">57</strong>
    </a>
    <a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, 'ara.opp', '', '', event);">
    <span class="ico_down"></span>
    <span class="blind">비공감</span>
    <strong class="notSympathy_17608959">4</strong>
    </a>
    </div>
    </li>
    <li>
    <div class="star_score">
    <span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>
    </div>
    <div class="score_reple">
    <p>
    <span class="ico_viewer">관람객</span>
    <!-- 스포일러 컨텐츠로 처리되는지 여부 -->
    <span id="_filtered_ment_8">
    														
    															
    															
    																조카랑 같이 봤는데 조카 보여주러 갔다가 제가 더 재밌게 보고 오네요~가족 모두 즐기기 좋은 영화입니다!! 
    															
    														
    														
    													</span>
    </p>
    <dl>
    <dt>
    <em>
    <a href="#" onclick="javascript:showPointListByNid(17608750, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
    <span>살쾡이(wild****)</span>
    </a>
    </em>
    <em>2021.07.21 09:56</em>
    </dt>
    <dd>
    <a class="go_report2" href="#" onclick="parent.clickcr(this, 'ara.report', '', '', event); common.report('false','wild****', 'KyulPdi7YXz3ro8yewlLm2Fu0XYw5/hBGwpUQSvMPM0=', '조카랑 같이 봤는데 조카 보여주러 갔다가 제가 더 재밌게 보고 오네요~가족 모두 즐기기 좋은 영화입니다!! ', '17608750', 'point_after', false);return false;"><em>신고</em></a>
    </dd>
    </dl>
    </div>
    <div class="btn_area">
    <a class="_sympathyButton" href="#" onclick="parent.clickcr(this, 'ara.sym', '', '', event);">
    <span class="ico_up"></span>
    <span class="blind">공감</span>
    <strong class="sympathy_17608750">57</strong>
    </a>
    <a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, 'ara.opp', '', '', event);">
    <span class="ico_down"></span>
    <span class="blind">비공감</span>
    <strong class="notSympathy_17608750">8</strong>
    </a>
    </div>
    </li>
    <li class="last">
    <div class="star_score">
    <span class="st_off"><span class="st_on" style="width:100.0%"></span></span><em>10</em>
    </div>
    <div class="score_reple">
    <p>
    <span class="ico_viewer">관람객</span>
    <!-- 스포일러 컨텐츠로 처리되는지 여부 -->
    <span id="_filtered_ment_9">
    														
    															
    															
    																1편 재밌게 봤으면 2푠도 꼭 보셈 ㅇㅇ 존꿀잼ㅋㅋㅋㅋ 보스베이비 볼수록 빠져드는 매력이 있음 
    															
    														
    														
    													</span>
    </p>
    <dl>
    <dt>
    <em>
    <a href="#" onclick="javascript:showPointListByNid(17608957, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
    <span>피노코(pino****)</span>
    </a>
    </em>
    <em>2021.07.21 12:22</em>
    </dt>
    <dd>
    <a class="go_report2" href="#" onclick="parent.clickcr(this, 'ara.report', '', '', event); common.report('false','pino****', 'WOjt7+8tYP/zJdIvjzu2O0MTAabgXoAGPdh8jZ2320g=', '1편 재밌게 봤으면 2푠도 꼭 보셈 ㅇㅇ 존꿀잼ㅋㅋㅋㅋ 보스베이비 볼수록 빠져드는 매력이 있음 ', '17608957', 'point_after', false);return false;"><em>신고</em></a>
    </dd>
    </dl>
    </div>
    <div class="btn_area">
    <a class="_sympathyButton" href="#" onclick="parent.clickcr(this, 'ara.sym', '', '', event);">
    <span class="ico_up"></span>
    <span class="blind">공감</span>
    <strong class="sympathy_17608957">48</strong>
    </a>
    <a class="_notSympathyButton" href="#" onclick="parent.clickcr(this, 'ara.opp', '', '', event);">
    <span class="ico_down"></span>
    <span class="blind">비공감</span>
    <strong class="notSympathy_17608957">6</strong>
    </a>
    </div>
    </li>
    </ul>
    </div>
    <div class="paging">
    <div>
    <a href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=1" id="pagerTagAnchor1" onclick="parent.clickcr(this, 'ara.page', '', '', event);"><span class="on">1</span></a>
    <a href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=2" id="pagerTagAnchor2" onclick="parent.clickcr(this, 'ara.page', '', '', event);"><span>2</span></a>
    <a href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=3" id="pagerTagAnchor3" onclick="parent.clickcr(this, 'ara.page', '', '', event);"><span>3</span></a>
    <a href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=4" id="pagerTagAnchor4" onclick="parent.clickcr(this, 'ara.page', '', '', event);"><span>4</span></a>
    <a href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=5" id="pagerTagAnchor5" onclick="parent.clickcr(this, 'ara.page', '', '', event);"><span>5</span></a>
    <a href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=6" id="pagerTagAnchor6" onclick="parent.clickcr(this, 'ara.page', '', '', event);"><span>6</span></a>
    <a href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=7" id="pagerTagAnchor7" onclick="parent.clickcr(this, 'ara.page', '', '', event);"><span>7</span></a>
    <a href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=8" id="pagerTagAnchor8" onclick="parent.clickcr(this, 'ara.page', '', '', event);"><span>8</span></a>
    <a href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=9" id="pagerTagAnchor9" onclick="parent.clickcr(this, 'ara.page', '', '', event);"><span>9</span></a>
    <a href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=10" id="pagerTagAnchor10" onclick="parent.clickcr(this, 'ara.page', '', '', event);"><span>10</span></a>
    <a class="pg_next" href="/movie/bi/mi/pointWriteFormList.naver?code=191597&amp;type=after&amp;isActualPointWriteExecute=false&amp;isMileageSubscriptionAlready=false&amp;isMileageSubscriptionReject=false&amp;page=2" id="pagerTagAnchor2" onclick="parent.clickcr(this, 'ara.page', '', '', event);" title="다음"><em>다음</em></a>
    </div>
    </div>
    </div>
    </div>
    <!-- //content -->
    <form accept-charset="utf-8" id="reportForm" method="POST" name="reportForm1"></form>
    <script type="text/javascript">
    
    
    if (false == false && "after" == "after" && false) {
    	if (true && false) {
    		jindo.$Element("pointWriteExecuteLayer").show();
    	} else if (false && true) {
    		jindo.$Element("actualPointWriteExecuteLayer").show();
    	}
    }
    
    var oElActualPointWriteExecuteLayer = jindo.$Element("actualPointWriteExecuteLayer");
    
    if (oElActualPointWriteExecuteLayer != null && oElActualPointWriteExecuteLayer != "undefined") {
    
    	
    	jindo.$Element("actualPointWriteExecuteLayerOkButton").attach("click", function(e){
    		e.stop(jindo.$Event.CANCEL_DEFAULT);
    		jindo.$Element("actualPointWriteExecuteLayer").hide();
    		refreshPage();
    	});
    	
    	
    	
    	jindo.$Element("actualPointWriteExecuteLayerCloseButton").attach("click", function(e){
    		e.stop(jindo.$Event.CANCEL_DEFAULT);
    		jindo.$Element("actualPointWriteExecuteLayer").hide();
    		refreshPage();
    	});
    	
    }
    
    var oElPointWriteExecuteLayer = jindo.$Element("pointWriteExecuteLayer");
    
    if (oElPointWriteExecuteLayer != null && oElPointWriteExecuteLayer != "undefined") {
    	
    	jindo.$Element("pointWriteExecuteLayerOkButton").attach("click", function(e){
    		e.stop(jindo.$Event.CANCEL_DEFAULT);
    		jindo.$Element("pointWriteExecuteLayer").hide();
    		refreshPage();
    	});
    	
    	
    	
    	jindo.$Element("pointWriteExecuteLayerCloseButton").attach("click", function(e){
    		e.stop(jindo.$Event.CANCEL_DEFAULT);
    		jindo.$Element("pointWriteExecuteLayer").hide();
    		refreshPage();
    	});
    	
    }
    
    jindo.$Fn(function () {
    
      function checkboxHandlerFactory(isActualYn, isLabel) {
        return function() {
          var actualYnChecked = jindo.$("actualYnCheckBox").checked;
          var spoilerYnChecked = jindo.$("spoilerYnCheckBox").checked;
    
          // convert
          actualYnChecked = (isActualYn && isLabel)? !actualYnChecked : actualYnChecked;
          spoilerYnChecked = (!isActualYn && isLabel)? !spoilerYnChecked : spoilerYnChecked;
    
          location.href = "/movie/bi/mi/pointWriteFormList.naver?code=191597&type=after&onlyActualPointYn=" + (actualYnChecked? "Y" : "N")  + "&onlySpoilerPointYn=" + (spoilerYnChecked? "Y" : "N") + "&order=sympathyScore";
        };
      }
    
    	
    	if (jindo.$("actualYnCheckBox") != null) {
    		jindo.$Fn(checkboxHandlerFactory(true, false), this).attach(jindo.$("actualYnCheckBox"), 'click');
    	}
    
    	if (jindo.$("actualYnLable") != null) {
    	    jindo.$Fn(checkboxHandlerFactory(true, true), this).attach(jindo.$("actualYnLable"), 'click');
    	}
    
    	
        if (jindo.$("spoilerYnCheckBox") != null) {
        jindo.$Fn(checkboxHandlerFactory(false, false), this).attach(jindo.$("spoilerYnCheckBox"), 'click');
      }
    
      if (jindo.$("spoilerYnLable") != null) {
        jindo.$Fn(checkboxHandlerFactory(false, true), this).attach(jindo.$("spoilerYnLable"), 'click');
      }
    	
    	
    	if (jindo.$Element("actualPointHelp") != null && jindo.$Element("actualPointHelp") != "undefined") {
    		actualPointHelpLayerToggle = function() {
    			setTimeout( function() {
    				if (document.activeElement != null) {
    					var focusedEl = jindo.$Element(document.activeElement);
    					if (focusedEl != null) {
    						if ( !focusedEl.hasClass("_actualPointHelp") ) {
    							 jindo.$Element("actualPointHelp").hide();
    						}
    					}
    				}
    			}, 100);
    		};
    
    		new jindo.LayerManager("actualPointHelp", {
    			sCheckEvent : "click",
    			nHideDelay : 0
    		}).link(jindo.$("actualPointHelp"), jindo.$("actualPointHelpButton"));
    
    		jindo.$Element("actualPointHelpButton").attach("click", function(e){
    			e.stop(jindo.$Event.CANCEL_DEFAULT);
    			jindo.$Element("actualPointHelp").toggle();
    		});
    
    		jindo.$Element("actualPointHelpCloseButton").attach("click", function(e){
    			e.stop(jindo.$Event.CANCEL_DEFAULT);
    			jindo.$Element("actualPointHelp").toggle();
    		});
    
    		
    		var waelActualPointHelp = jindo.$ElementList('._actualPointHelp');
    
    		jindo.$A(waelActualPointHelp.$value()).forEach(function(value, index, array) {
    			jindo.$Fn(actualPointHelpLayerToggle, this).attach(value, "blur");
    		});
    	}
    
    	parent.setParamForPointAfterList('N', 'sympathyScore', '1');
    	parent.resizePointAfterListIframe(0);
    
    	// 최소 높이 270px 지정
    	var frameHeight = eval(jindo.$Document().scrollSize().height);
    
    	parent.resizePointAfterListIframe(frameHeight);
    	parent.isPointAfterListLoad = true;
    	
    	
    	
    
      var isCheckPointExist = false;
      // TODO: fix below condition
      if (false) {
        if(jindo.$Element("open-form-btn")) {
    		jindo.$Element("open-form-btn").attach("click", function(e) {
    		  	
    		  	
    			
    		  if (true && false && isCheckPointExist == false) {
    			parent.point.checkPointAfterExistAndMileageSubscriptionType();
    		  }
    		  isCheckPointExist = true;
    		  parent.openPointWriteForm();
    		});
        }
      } else {
        if (jindo.$Element("open-form-btn")) {
    		jindo.$Element("open-form-btn").attach("click", function (e) {
    		  common.checkLogin(false);
    		});
        }
      }
    
      var isHide = true;
    
      if(jindo.$Element("eval-edit")) {
        jindo.$Element("eval-edit").attach("click", function() {
    		  var edit = jindo.$Element("ly-edit");
    	  	isHide ? edit.show() : edit.hide();
          	isHide = !isHide;
        })
      }
      parent.resizePointAfterListIframeOnLoad();
    }, this).attach(this, 'load');
    
    var point = {
    		checkLoginWithMessage : function(login, loginMessage, notLoginMessage) {
    			if(login == false){
    				if(confirm(message)){
    					top.location.href="https://nid.naver.com/nidlogin.login?mode=form&url="+encodeURIComponent(top.location.href);
    				}
    				return false;
    			}
    			return true;
    		},
    
    		
    		checkAlreadyPointAfterExist : function (nid) {
    			var existPointType = "pointBefore";
    			
    			if (false == false) {
    				var oAjax = new jindo.$Ajax("/api/internal/point/pointAfterExistJson.naver", {
    			    	onload : function (oRes) {
    			    		var resultCode = oRes.json().resultCode;
    			    		
    			    		if (resultCode == "error") {							// 서버 오류
    			    			alert("오류가 발생했습니다. 잠시 후 다시 시도해주세요.");
    			    			return false;
    			    		} else {
    			    			existPointType = oRes.json().existPointType;
    			    			point.del(existPointType, nid);
    			    		}
    			    	},
    					timeout : 5,
    			    	onerror : function (oRes) {
    			    		alert("오류가 발생했습니다. 잠시 후 다시 시도해주세요.");
    			    		return false;
    			    	},
    			    	ontimeout : function (oRes) {
    			    		alert("처리가 지연되고 있습니다. 다시 시도해주세요.");
    			    		return false;
    			    	}
    			    });
    			    
    			    oAjax.request({
    			    	"movieCode" : "191597",
    			    	"isActualPoint" : "false"
    			    });
    			} else {
    				point.del(existPointType, nid);
    			}
    		},
    
    		del : function (existPointType, nid) {
    			if (existPointType == "actualPoint") {
    				if (confirm("관람객 평점 삭제시, 평점 작성으로 적립된 포인트는 회수됩니다. 평점을 삭제할까요?") == false) {
    					return false;
    				}
    			} else {
    				if (confirm("본인 삭제 시 복구할 수 없습니다.\n평점을 삭제하시겠습니까?") == false) {
    					return false;
    				}
    			}
    			
    			var ajaxDeleteUrl = "/api/internal/point/pointBeforeDelete.naver";
    			if ("after" == "after") {
    				ajaxDeleteUrl = "/api/internal/point/pointAfterDelete.naver";
    			}
    			
    			var ajax = new jindo.$Ajax(ajaxDeleteUrl, { 
    				method : "POST",
    				async : false,
    				onload : this.delCallback
    			});
    			ajax.header("ajax", "true");
    			ajax.request({
    				"nid":nid
    			});
    		},
    		
    		delCallback : function(req) {
    			var returnValue = req.text();
    			
    			if(returnValue != "success"){
    				alert(returnValue);
    				return false;
    			}
    			
    			top.location.href = '/movie/bi/mi/point.naver?code=191597#pointAfterTab';
    			top.location.reload(true);
    		}
    };
    
    function dislplayOrder(order) {
    	var url = "/movie/bi/mi/pointWriteFormList.naver?code=191597&type=after";
    	
    	var onlyActualPointYnValue = jindo.$("onlyActualPointYn").value;
      	var includeSpoilerYnValue = jindo.$("includeSpoilerYn").value;
    
    	if (onlyActualPointYnValue != "") {
    		url = url + "&onlyActualPointYn=" + onlyActualPointYnValue;
    	}
    
    	if (includeSpoilerYnValue != "") {
          url = url + "&onlySpoilerPointYn=" + includeSpoilerYnValue;
        }
    	
    	url = url + "&order=" + order;
    	
    	location.href = document.location.protocol + "//" + document.domain + url;
    }
    
    function showPointListByNid(nid, target){
    	if (target == 'after') {
    		top.location.href = top.location.protocol + "//" + top.location.hostname + "/movie/point/af/list.naver?st=nickname&target=after&sword="+nid;
    	} else {
    		top.location.href = top.location.protocol + "//" + top.location.hostname + "/movie/point/af/list.naver?st=nickname_before&target=before&sword="+nid;
    	}
    }
    
    function refreshPage() {
    	 
    	top.location.href = '/movie/bi/mi/point.naver?code=191597#pointAfterTab';
    }
    
    // 스포일러 감상평 내용 보기
    function showMovieReview(rvwIdx) {
        jindo.$Element('_text_spo_' + rvwIdx).css('display', 'none');
        jindo.$Element('_filtered_ment_' + rvwIdx).css('display', 'block');
        parent.resizePointAfterListIframeOnLoad();
    }
    
    // 감상평 펼쳐보기
    function unfoldPointMent(obj) {
        var fullMent = jindo.$Element(obj).attr("data-src");
        jindo.$Element(obj).parent().html(fullMent);
        parent.resizePointAfterListIframeOnLoad();
    }
    
    
    </script>
    <script type="text/javascript">
    	var sympathy = new Sympathy("191597", "after");
    </script>
    <script type="text/javascript">
                jindo.$Fn(function() {
                    try{ lcs_do(); } catch(e){}
                }).attach(window, "pageshow");
    		</script>
    </body>
    </html>



이제 원하는 리뷰 데이터를 어떻게 찾을 수 있을지를 알아야 한다. 

우선 리뷰를 가져오자


```python
# 리뷰 추출
ment_n = 0 # 맨 위의 리뷰
review = soup.find('span', id=f'_filtered_ment_{ment_n}')
review.get_text().strip()
```




    '가볍게 보러갔다가 감동까지 느끼고 옵니다올해 봤던 애니메이션 중에 제일 재밌게 봤네요'




```python
# 한 페이지 내의 리뷰 추출(총 10개)
for i in range(10):
    review = soup.find('span', id=f'_filtered_ment_{i}')
    review = review.get_text().strip()
    print(f'{i+1}번째 리뷰')
    print(review)
    print()
```

    1번째 리뷰
    가볍게 보러갔다가 감동까지 느끼고 옵니다올해 봤던 애니메이션 중에 제일 재밌게 봤네요
    
    2번째 리뷰
    귀여워서 심장 아픔ㅠㅠ 세상에 이런 보스라면 매일 출근하궤쒀!!
    
    3번째 리뷰
    귀여운 아가보스가 두명이나 나오는데 비주얼적으로 안볼수가 없지요!!^^ 1편도 재미있게 봤었는데, 2편은 더더 경쾌하고 액션 넘쳐서 즐겁게 볼수 있었어요. 이 사랑스러운 영화 추천합니다!!
    
    4번째 리뷰
    너무 사랑스럽잖아!!ㅠㅠㅠ 진짜 1편도 다섯번정도 봤는데 앞으로 계속 나와줘라
    
    5번째 리뷰
    귀여운 캐릭터로 기분까지 좋아지는 영화
    
    6번째 리뷰
    겁나 귀엽고 웃김ㅋㅋㅋㅋ 혼자 기분전환할겸 봤는데 친구 데리고 한번 더 보고싶음 꿀잼... 베이비들 넘 귀엽~~~!!
    
    7번째 리뷰
    베이비 넘모 귀엽고,, 사랑스러움 ㅠㅠ 심장에 무리 올뻔 >_<
    
    8번째 리뷰
    이쁜캐릭터들 재미와 감동입니다.
    
    9번째 리뷰
    조카랑 같이 봤는데 조카 보여주러 갔다가 제가 더 재밌게 보고 오네요~가족 모두 즐기기 좋은 영화입니다!!
    
    10번째 리뷰
    1편 재밌게 봤으면 2푠도 꼭 보셈 ㅇㅇ 존꿀잼ㅋㅋㅋㅋ 보스베이비 볼수록 빠져드는 매력이 있음
    
    

다음으로 리뷰 작성일을 가져오려면


```python
# 리뷰 작성일
# 리뷰 작성일이 포함되어 있는 태그를 가져오기
soup.select('.score_reple em')
```




    [<em>
     <a href="#" onclick="javascript:showPointListByNid(17609059, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
     <span>교님(sair****)</span>
     </a>
     </em>,
     <em>2021.07.21 13:23</em>,
     <em>신고</em>,
     <em>
     <a href="#" onclick="javascript:showPointListByNid(17608937, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
     <span>끼야오루(hell****)</span>
     </a>
     </em>,
     <em>2021.07.21 12:09</em>,
     <em>신고</em>,
     <em>
     <a href="#" onclick="javascript:showPointListByNid(17609015, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
     <span>pian****</span>
     </a>
     </em>,
     <em>2021.07.21 13:02</em>,
     <em>신고</em>,
     <em>
     <a href="#" onclick="javascript:showPointListByNid(17608958, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
     <span>두여름(gaeu****)</span>
     </a>
     </em>,
     <em>2021.07.21 12:23</em>,
     <em>신고</em>,
     <em>
     <a href="#" onclick="javascript:showPointListByNid(17608908, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
     <span>sed1834(xxx7****)</span>
     </a>
     </em>,
     <em>2021.07.21 11:53</em>,
     <em>신고</em>,
     <em>
     <a href="#" onclick="javascript:showPointListByNid(17608761, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
     <span>eclipse(doct****)</span>
     </a>
     </em>,
     <em>2021.07.21 10:07</em>,
     <em>신고</em>,
     <em>
     <a href="#" onclick="javascript:showPointListByNid(17608923, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
     <span>Quin(suen****)</span>
     </a>
     </em>,
     <em>2021.07.21 12:01</em>,
     <em>신고</em>,
     <em>
     <a href="#" onclick="javascript:showPointListByNid(17608959, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
     <span>123erjjerf(jimi****)</span>
     </a>
     </em>,
     <em>2021.07.21 12:24</em>,
     <em>신고</em>,
     <em>
     <a href="#" onclick="javascript:showPointListByNid(17608750, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
     <span>살쾡이(wild****)</span>
     </a>
     </em>,
     <em>2021.07.21 09:56</em>,
     <em>신고</em>,
     <em>
     <a href="#" onclick="javascript:showPointListByNid(17608957, 'after');parent.clickcr(this, 'ara.uid', '', '', event); return false;" target="_top">
     <span>피노코(pino****)</span>
     </a>
     </em>,
     <em>2021.07.21 12:22</em>,
     <em>신고</em>]




```python
date = soup.select('.score_reple em')
date = date[1].get_text().strip().split(' ')[0] # 시간 제외 : [0] 
date, type(date)
```




    ('2021.07.21', str)



select() 함수를 사용해서 em 태그에 있는 날짜 가져오기

날짜가 2번째, 5번째, 8번째... 로 반복되니까 
for문을 사용해서 인덱스를 1, 4, 7, 10...으로 주면 된다.


```python
for i in range(1, 30, 3):
    date = soup.select('.score_reple em')
    date = date[i].get_text().strip().split(' ')[0]
    print(date)
```

    2021.07.21
    2021.07.21
    2021.07.21
    2021.07.21
    2021.07.21
    2021.07.21
    2021.07.21
    2021.07.21
    2021.07.21
    2021.07.21
    

이렇게 하면 한 페이지에 있는 날짜 정보를 가져올 수 있다.

그리고 날짜 정보를 date 타입으로 변경하기 위해 함수를 정의해보자


```python
import datetime as dt

def date_format(date):
    yyyy, mm, dd = [int(x) for x in date.split(".")]
    return dt.date(yyyy, mm, dd)

date_format(date)
```




    datetime.date(2021, 7, 21)



이제 평점을 가져오는 코드를 작성해보자


```python
# 평점 추출
score = soup.select('.star_score em')
float(score[0].get_text())
```




    10.0



### 영화 제목, 코드 딕셔너리 만들기

- 현재 상영작 영화 코드 가져와서 key를 영화 제목으로, value를 영화 코드로 하는 딕셔너리 만들기

- 이 딕셔너리를 적용시켜 영화 제목 입력하면 해당 영화의 리뷰, 리뷰작성일, 평점 가져오기 위해서!


```python
# 현재 상영작 페이지 url
movie_url = 'https://movie.naver.com/movie/running/current.naver'

resp = requests.get(movie_url)
soup = BeautifulSoup(resp.text, 'lxml')

movie_dict = {}

for tag in soup.find_all('dt', class_='tit'):
    title = tag.a.text
    code = tag.a.get('href').split('=')[1]  # 링크에서 코드만 추출
    
    movie_dict[title] = code
```


```python
movie_dict
```




    {'모가디슈': '192150',
     '방법: 재차의': '200785',
     '정글 크루즈': '179406',
     '보스 베이비 2': '191597',
     '블랙 위도우': '184318',
     '이스케이프 룸 2: 노 웨이 아웃': '191548',
     '이도공간': '35360',
     '아이스 로드': '205966',
     '랑종': '204496',
     '우리, 둘': '188466',
     '꽃다발 같은 사랑을 했다': '200896',
     '피닉스': '129687',
     '오필리아': '168074',
     '나는 나대로 혼자서 간다': '198327',
     '와인 패밀리': '205285',
     '은혼 더 파이널': '201272',
     '노매드랜드': '196215',
     '갈매기': '194097',
     '크루엘라': '187322',
     '좀비크러쉬: 헤이리': '194886',
     '우리는 매일매일': '188195',
     '액션히어로': '201783',
     '트립 투 그리스': '193525',
     '빛나는 순간': '193327',
     '하타리': '10777',
     '호스트: 접속금지': '198259',
     '크레센도': '186410',
     '워스': '191927',
     '포이즌 로즈': '197655',
     '스페이스 잼: 새로운 시대': '203678',
     '달맞이꽃': '12077',
     '식물카페, 온정': '204174',
     '숏버스 이별행': '201800',
     '콰이어트 플레이스 2': '189368',
     '옥스포드 살인사건': '66274',
     '메이드 인 루프탑': '199068',
     '하하하': '54970',
     '자메이카의 소울: 이나 데 야드': '186118',
     '화양연화': '28876',
     '날씨의 아이': '181114',
     '중경삼림': '17059',
     '이번엔 잘 되겠지': '203465',
     '너의 이름은.': '150198',
     '아이 엠 러브': '71151',
     '인트로덕션': '202541',
     '오직 사랑하는 이들만이 살아남는다': '96031',
     '발신제한': '194205',
     '극장판 귀멸의 칼날: 무한열차편': '196051',
     '루카': '202903',
     '죽어도 좋은 경험': '23138',
     '피어 오브 레인': '205982',
     '흩어진 밤': '185148',
     '북샵': '119126',
     '열아홉': '203721',
     '그 여름, 가장 차가웠던': '196221',
     '에로스': '40027',
     '화이트 온 화이트': '188264',
     '까치발': '151961',
     '남매의 여름밤': '189623',
     '뱅드림! 필름 라이브': '189086',
     '부활: 그 증거': '194334',
     '샤먼 로드': '178267',
     '청춘 선거': '197061',
     '포겟 미 낫-엄마에게 쓰는 편지': '183600',
     '400번의 구타': '19374',
     '걸어도 걸어도': '50749',
     '교실 안의 야크': '189393',
     '그녀들을 도와줘': '172773',
     '낫아웃': '203716',
     '너의 췌장을 먹고 싶어': '159830',
     '더 나이트': '191443',
     '더 파더': '191920',
     '도라에몽: 스탠바이미 2': '200052',
     '동사서독 리덕스': '49085',
     '디어스킨': '187874',
     '디에고': '185286',
     '뜨거운 것이 좋아': '10286',
     '러브 액츄얼리': '36843',
     '레 미제라블': '185276',
     '로스트 하이웨이': '18708',
     '린 온 피트': '152648',
     '마틴 에덴': '159286',
     '맹크': '191585',
     '모리타니안': '193800',
     '몽상가들': '37544',
     '미나리': '187310',
     '미스터 노바디': '70615',
     '밤의 문이 열린다': '176625',
     '배신자': '185268',
     '뱅드림! 로젤리아 에피소드Ⅰ: 약속': '204197',
     '벌새': '179307',
     '보스 베이비': '129094',
     '서칭 포 슈가맨': '91101',
     '소유와 무소유': '10786',
     '스파이의 아내': '196052',
     '시 읽는 시간': '154947',
     '아비정전': '10546',
     '아사코': '172145',
     '아이들은 즐겁다': '203468',
     '애비규환': '198430',
     '언더 더 실버레이크': '158650',
     '언어의 정원': '106335',
     '열혈남아': '11031',
     '우리들': '146504',
     '운디네': '192102',
     '일대종사': '49501',
     '조제, 호랑이 그리고 물고기들': '195970',
     '쥴 앤 짐': '18769',
     '초속5센티미터': '66820',
     '카리스마': '29388',
     '컨저링 3: 악마가 시켰다': '190726',
     '쿠오바디스, 아이다': '196576',
     '페이블': '186889',
     '퐁네프의 연인들': '11151',
     '프란시스 하': '98738',
     '프란츠': '149012',
     '플로리다 프로젝트': '164719',
     '해피 투게더': '19335',
     '혐오스런 마츠코의 일생': '58333',
     '홀리 모터스': '95251',
     '흐르는 강물처럼': '16792',
     '미스 사이공: 25주년 특별 공연': '155123',
     '쥐띠 부인': '23027'}




```python
movie_dict['보스 베이비 2']
```




    '191597'



### 하나의 함수로 만들기
- 파라미터로 **리뷰 끝페이지 수**랑 **영화 제목** 받아서
- 영화 제목, 리뷰작성일, 리뷰, 평점 출력


- 단, 현재 상영작 페이지에 있는 영화만 가능


```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
import datetime as dt

# 영화별 리뷰작성일, 리뷰, 평점 구하는 함수
def get_reviews(end_page, title):
    
    try:
        # 영화 제목, 코드 가져오는 딕셔너리 만들기
        movie_url = 'https://movie.naver.com/movie/running/current.naver' # 현재 상영작 페이지
        resp = requests.get(movie_url)
        soup = BeautifulSoup(resp.text, 'lxml')
    
        movie_dict = {}  # 빈 딕셔너리 만들기
        for tag in soup.find_all('dt', class_='tit'):
            m_title = tag.a.text  # 제목 추출
            m_code = tag.a.get('href').split('=')[1]  # 링크에서 코드만 추출  # 링크 : '/movie/bi/mi/basic.naver?code=192150'
            movie_dict[m_title] = int(m_code)  # 딕셔너리에 추출한 제목이랑 코드 넣어주기 # 데이터 타입 바꾸기 : str -> int
    
        code = movie_dict[title]   # 앞에서 만든 딕셔너리 이용해서 파라미터로 넣은 영화 제목의 코드 저장
    except:
        print('영화 제목 코드 딕셔너리 만들기 실패')
    
    # 날짜정보를 date 타입으로 변경하는 함수 정의
    def date_format(date):
        yyyy, mm, dd = [int(x) for x in date.split(".")]
        return dt.date(yyyy, mm, dd)
    
    date_list = []
    review_list = []
    score_list = []
    
    for page_n in range(1, end_page+1):
        
        try:
            # 리뷰 페이지 가져오기 # 위에서 저장한 영화 코드 넣기
            review_index = f'https://movie.naver.com/movie/bi/mi/pointWriteFormList.naver?code={code}&type=after&isActualPointWriteExecute=false&isMileageSubscriptionAlready=false&isMileageSubscriptionReject=false&page={page_n}'
            resp = requests.get(review_index)
            soup = BeautifulSoup(resp.text)
        except:
            print('해당 영화의 리뷰 페이지 가져오기 실패')
        
        try:
            # 날짜 10개 (한 페이지당 10개의 리뷰 있기 때문!)
            for i in range(1, 30, 3):
                date = soup.select('.score_reple em') # score_reple 클래스 하위의 em 가져오기 -> 날짜
                date = date[i].get_text().strip().split(' ')[0]  # date는 인덱스 1, 4, 7, 10 .. 규칙  # 시간 제외 : split(' ')[0]
                date = date_format(date)  # 날짜 정보 date 타입으로 변경하는 함수에 넣어서 데이터 타입 바꾸기
                date_list.append(date)    
        except:
            print('리뷰 작성일 가져오기 실패')
        
        try:
            # 리뷰 10개, 평점 10개
            for i in range(10):
                review = soup.find('span', id=f'_filtered_ment_{i}')
                review = review.get_text().strip()
                review_list.append(review)
            
                score = soup.select('.star_score em')  # star_score 클래스 하위의 em 가져오기
                score = float(score[i].get_text())     # 데이터 타입 바꾸기 : str -> float
                score_list.append(score)
        except:
            print('리뷰, 평점 가져오기 실패')
                
    # 데이터 프레임 만들기
    df = pd.DataFrame({'영화':title, '날짜':date_list, '리뷰':review_list, '평점':score_list})  
    return df  
```


```python
get_reviews(20, '보스 베이비 2')
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
      <th>영화</th>
      <th>날짜</th>
      <th>리뷰</th>
      <th>평점</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>보스 베이비 2</td>
      <td>2021-07-21</td>
      <td>가볍게 보러갔다가 감동까지 느끼고 옵니다올해 봤던 애니메이션 중에 제일 재밌게 봤네요</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>보스 베이비 2</td>
      <td>2021-07-21</td>
      <td>귀여워서 심장 아픔ㅠㅠ 세상에 이런 보스라면 매일 출근하궤쒀!!</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>보스 베이비 2</td>
      <td>2021-07-21</td>
      <td>귀여운 아가보스가 두명이나 나오는데 비주얼적으로 안볼수가 없지요!!^^ 1편도 재미...</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>보스 베이비 2</td>
      <td>2021-07-21</td>
      <td>너무 사랑스럽잖아!!ㅠㅠㅠ 진짜 1편도 다섯번정도 봤는데 앞으로 계속 나와줘라</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>보스 베이비 2</td>
      <td>2021-07-21</td>
      <td>귀여운 캐릭터로 기분까지 좋아지는 영화</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>195</th>
      <td>보스 베이비 2</td>
      <td>2021-07-21</td>
      <td>가족 경영이 이렇게 귀여워도 될일..? 형제들은 여전히 귀엽고 티나는 넘 카리스마 ...</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>196</th>
      <td>보스 베이비 2</td>
      <td>2021-07-22</td>
      <td>알바천국임 . 리뷰 아이디가 죄다 rz5x*** 이런식으로 막 친 아이디뿐인거만 봐...</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>197</th>
      <td>보스 베이비 2</td>
      <td>2021-07-21</td>
      <td>보스베이비들 너무 귀여워요!! ㅠㅠ 1편만큼 재밌었어요b</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>198</th>
      <td>보스 베이비 2</td>
      <td>2021-07-21</td>
      <td>재미있어요~♡ 나중에 또보고 싶어요!</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>199</th>
      <td>보스 베이비 2</td>
      <td>2021-07-21</td>
      <td>노노ㅗ노ㅗ노노ㅗㅗ잼</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>200 rows × 4 columns</p>
</div>



완성 ~~~

그런데 지금 만든 이 코드를 다른 사이트에 이용하거나, 나~~ 중에 다시 사용하려고 하면 안 될 가능성이 크다!

웹페이지마다 다르고, 시간이 지나면 웹페이지의 구조가 바뀔 수 있기 때문이다. 

즉 웹크롤러 코드를 일반화할 수 없다.

그렇기 때문에 실전 연습을 통해서 웹 환경을 이해하고, 코드가 웹 서버에 데이터를 요청해서 찾아내는 큰 로직을 이해하는 것이 중요하다. 규칙은 크게 어렵지 않으니 직접 찾는 걸 많이 해보자!
