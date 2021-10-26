---
title: "Markdown Extra Components"
layout: post
date: 2016-02-24 22:48
image: /assets/images/markdown.jpg
headerImage: false
tag:
- markdown
- components
- extra
category: blog
author: jamesfoster
description: Markdown summary with different options
---
# urllib 패키지 활용 웹크롤링

* urlopen 
* 전체 페이지 소스 
* beautifulsoup 으로 필요한 데이터 크롤링


```python
from urllib.request import urlopen

URL = "http://comp.fnguide.com/SVO2/ASP/SVD_Finance.asp?pGB=1&gicode=A005930"

req = urlopen(URL)
html = req.read()
```


```python
from bs4 import BeautifulSoup

soup = BeautifulSoup(html, "html.parser")
```


```python
res = soup.find_all("table")
```


```python
len(res)
```




    6




```python
soup_table = soup.find("table", attrs={"class":"us_table_ty1 h_fix zigbg_no"})
```


```python
from html_table_parser import parser_functions as parser
import pandas as pd

table = parser.make2d(soup_table)
df = pd.DataFrame(table[1:], columns = table[0])
df.head()
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
      <th>IFRS(연결)</th>
      <th>2018/12</th>
      <th>2019/12</th>
      <th>2020/12</th>
      <th>2021/06</th>
      <th>전년동기</th>
      <th>전년동기(%)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>매출액</td>
      <td>2,437,714</td>
      <td>2,304,009</td>
      <td>2,368,070</td>
      <td>1,290,601</td>
      <td>1,082,913</td>
      <td>19.2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>매출원가</td>
      <td>1,323,944</td>
      <td>1,472,395</td>
      <td>1,444,883</td>
      <td>785,659</td>
      <td>667,129</td>
      <td>17.8</td>
    </tr>
    <tr>
      <th>2</th>
      <td>매출총이익</td>
      <td>1,113,770</td>
      <td>831,613</td>
      <td>923,187</td>
      <td>504,942</td>
      <td>415,784</td>
      <td>21.4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>판매비와관리비계산에 참여한 계정 펼치기</td>
      <td>524,903</td>
      <td>553,928</td>
      <td>563,248</td>
      <td>285,446</td>
      <td>269,848</td>
      <td>5.8</td>
    </tr>
    <tr>
      <th>4</th>
      <td>인건비</td>
      <td>64,514</td>
      <td>64,226</td>
      <td>70,429</td>
      <td>36,000</td>
      <td>34,779</td>
      <td>3.5</td>
    </tr>
  </tbody>
</table>
</div>




```python
from urllib.error import HTTPError
from urllib.request import urlopen
from bs4 import BeautifulSoup
from html_table_parser import parser_functions as parser
import logging
import time

def collect_sheet(code, try_cnt):
    try:
        URL = "http://comp.fnguide.com/SVO2/ASP/SVD_Finance.asp?pGB=1&gicode={}".format(code)
        
        req =urlopen(URL)
        html = req.read()
        
        soup = BeautifulSoup(html, 'html.parser')
        soup_table_all = soup.find_all("table")
        soup_table = soup.find("table", "us_table_ty1 h_fix zigbg_no")
        table = parser.make2d(soup_table)
        df = pd.DataFrame(table[1:], columns = table[0])
        
        return df
    
    except HTTPError as e:
        if try_cnt>=3:
            logging.warning(e)
            return None
        else:
            time.sleep(3)
            collect_div(corp_code, try_cnt=+1)
```


```python
collect_sheet('A086450', 3)
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
      <th>IFRS(연결)</th>
      <th>2018/12</th>
      <th>2019/12</th>
      <th>2020/12</th>
      <th>2021/06</th>
      <th>전년동기</th>
      <th>전년동기(%)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>매출액</td>
      <td>4,008</td>
      <td>4,823</td>
      <td>5,591</td>
      <td>2,987</td>
      <td>2,694</td>
      <td>10.9</td>
    </tr>
    <tr>
      <th>1</th>
      <td>매출원가</td>
      <td>1,629</td>
      <td>1,914</td>
      <td>2,217</td>
      <td>1,215</td>
      <td>1,062</td>
      <td>14.4</td>
    </tr>
    <tr>
      <th>2</th>
      <td>매출총이익</td>
      <td>2,379</td>
      <td>2,908</td>
      <td>3,374</td>
      <td>1,771</td>
      <td>1,632</td>
      <td>8.6</td>
    </tr>
    <tr>
      <th>3</th>
      <td>판매비와관리비계산에 참여한 계정 펼치기</td>
      <td>1,828</td>
      <td>2,223</td>
      <td>2,527</td>
      <td>1,417</td>
      <td>1,256</td>
      <td>12.8</td>
    </tr>
    <tr>
      <th>4</th>
      <td>인건비</td>
      <td>464</td>
      <td>533</td>
      <td>590</td>
      <td>347</td>
      <td>306</td>
      <td>13.2</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>74</th>
      <td>계속영업이익</td>
      <td>494</td>
      <td>591</td>
      <td>579</td>
      <td>267</td>
      <td>312</td>
      <td>-14.5</td>
    </tr>
    <tr>
      <th>75</th>
      <td>중단영업이익</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>76</th>
      <td>당기순이익</td>
      <td>494</td>
      <td>591</td>
      <td>579</td>
      <td>267</td>
      <td>312</td>
      <td>-14.5</td>
    </tr>
    <tr>
      <th>77</th>
      <td>지배주주순이익</td>
      <td>469</td>
      <td>563</td>
      <td>559</td>
      <td>264</td>
      <td>299</td>
      <td>-11.5</td>
    </tr>
    <tr>
      <th>78</th>
      <td>비지배주주순이익</td>
      <td>25</td>
      <td>27</td>
      <td>20</td>
      <td>2</td>
      <td>13</td>
      <td>-82.4</td>
    </tr>
  </tbody>
</table>
<p>79 rows × 7 columns</p>
</div>



# requests 패키지 이용 웹 크롤링



```python
import requests

input_data = {"pGB":1, "gicode":"A005930"}
result = requests.get("http://comp.fnguide.com/SV02/ASP/SVD_Finance.asp",
                     data = input_data)
print(result.text)
```

    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd"> 
    <html xmlns="http://www.w3.org/1999/xhtml"> 
    <head> 
    <title>IIS 8.5 자세한 오류 - 404.0 - Not Found</title> 
    <style type="text/css"> 
    <!-- 
    body{margin:0;font-size:.7em;font-family:Verdana,Arial,Helvetica,sans-serif;} 
    code{margin:0;color:#006600;font-size:1.1em;font-weight:bold;} 
    .config_source code{font-size:.8em;color:#000000;} 
    pre{margin:0;font-size:1.4em;word-wrap:break-word;} 
    ul,ol{margin:10px 0 10px 5px;} 
    ul.first,ol.first{margin-top:5px;} 
    fieldset{padding:0 15px 10px 15px;word-break:break-all;} 
    .summary-container fieldset{padding-bottom:5px;margin-top:4px;} 
    legend.no-expand-all{padding:2px 15px 4px 10px;margin:0 0 0 -12px;} 
    legend{color:#333333;;margin:4px 0 8px -12px;_margin-top:0px; 
    font-weight:bold;font-size:1em;} 
    a:link,a:visited{color:#007EFF;font-weight:bold;} 
    a:hover{text-decoration:none;} 
    h1{font-size:2.4em;margin:0;color:#FFF;} 
    h2{font-size:1.7em;margin:0;color:#CC0000;} 
    h3{font-size:1.4em;margin:10px 0 0 0;color:#CC0000;} 
    h4{font-size:1.2em;margin:10px 0 5px 0; 
    }#header{width:96%;margin:0 0 0 0;padding:6px 2% 6px 2%;font-family:"trebuchet MS",Verdana,sans-serif; 
     color:#FFF;background-color:#5C87B2; 
    }#content{margin:0 0 0 2%;position:relative;} 
    .summary-container,.content-container{background:#FFF;width:96%;margin-top:8px;padding:10px;position:relative;} 
    .content-container p{margin:0 0 10px 0; 
    }#details-left{width:35%;float:left;margin-right:2%; 
    }#details-right{width:63%;float:left;overflow:hidden; 
    }#server_version{width:96%;_height:1px;min-height:1px;margin:0 0 5px 0;padding:11px 2% 8px 2%;color:#FFFFFF; 
     background-color:#5A7FA5;border-bottom:1px solid #C1CFDD;border-top:1px solid #4A6C8E;font-weight:normal; 
     font-size:1em;color:#FFF;text-align:right; 
    }#server_version p{margin:5px 0;} 
    table{margin:4px 0 4px 0;width:100%;border:none;} 
    td,th{vertical-align:top;padding:3px 0;text-align:left;font-weight:normal;border:none;} 
    th{width:30%;text-align:right;padding-right:2%;font-weight:bold;} 
    thead th{background-color:#ebebeb;width:25%; 
    }#details-right th{width:20%;} 
    table tr.alt td,table tr.alt th{} 
    .highlight-code{color:#CC0000;font-weight:bold;font-style:italic;} 
    .clear{clear:both;} 
    .preferred{padding:0 5px 2px 5px;font-weight:normal;background:#006633;color:#FFF;font-size:.8em;} 
    --> 
    </style> 
     
    </head> 
    <body> 
    <div id="content"> 
    <div class="content-container"> 
      <h3>HTTP 오류 404.0 - Not Found</h3> 
      <h4>찾고 있는 리소스가 제거되었거나, 이름이 바뀌었거나, 일시적으로 사용할 수 없는 상태입니다.</h4> 
    </div> 
    <div class="content-container"> 
     <fieldset><h4>가능성이 높은 원인:</h4> 
      <ul> 	<li>지정한 디렉터리 또는 파일이 웹 서버에 존재하지 않습니다.</li> 	<li>URL을 잘못 입력했습니다.</li> 	<li>URLScan 등의 사용자 지정 필터 또는 모듈에서 파일에 대한 액세스를 제한합니다.</li> </ul> 
     </fieldset> 
    </div> 
    <div class="content-container"> 
     <fieldset><h4>가능한 해결 방법:</h4> 
      <ul> 	<li>웹 서버에서 콘텐츠를 만드십시오.</li> 	<li>브라우저 URL을 검토해 보십시오.</li> 	<li>실패한 요청에서 이 HTTP 상태 코드를 추적하는 추적 규칙을 만드십시오. 실패한 요청에 대한 추적 규칙을 만드는 방법을 자세하게 보려면 <a href="http://go.microsoft.com/fwlink/?LinkID=66439">여기</a>를 클릭하십시오. </li> </ul> 
     </fieldset> 
    </div> 
     
    <div class="content-container"> 
     <fieldset><h4>자세한 오류 정보:</h4> 
      <div id="details-left"> 
       <table border="0" cellpadding="0" cellspacing="0"> 
        <tr class="alt"><th>모듈</th><td>&nbsp;&nbsp;&nbsp;IIS Web Core</td></tr> 
        <tr><th>알림</th><td>&nbsp;&nbsp;&nbsp;MapRequestHandler</td></tr> 
        <tr class="alt"><th>처리기</th><td>&nbsp;&nbsp;&nbsp;ASPClassic</td></tr> 
        <tr><th>오류 코드</th><td>&nbsp;&nbsp;&nbsp;0x80070002</td></tr> 
         
       </table> 
      </div> 
      <div id="details-right"> 
       <table border="0" cellpadding="0" cellspacing="0"> 
        <tr class="alt"><th>요청한 URL</th><td>&nbsp;&nbsp;&nbsp;http://comp.fnguide.com:80/SV02/ASP/SVD_Finance.asp</td></tr> 
        <tr><th>실제 경로</th><td>&nbsp;&nbsp;&nbsp;D:\CompanyGuide\SV02\ASP\SVD_Finance.asp</td></tr> 
        <tr class="alt"><th>로그온 방법d</th><td>&nbsp;&nbsp;&nbsp;익명</td></tr> 
        <tr><th>로그온 사용자</th><td>&nbsp;&nbsp;&nbsp;익명</td></tr> 
         
       </table> 
       <div class="clear"></div> 
      </div> 
     </fieldset> 
    </div> 
     
    <div class="content-container"> 
     <fieldset><h4>추가 정보:</h4> 
      이 오류는 파일이나 디렉터리가 서버에 존재하지 않는다는 것을 의미합니다. 파일이나 디렉터리를 만든 다음 다시 요청해 보십시오. 
      <p><a href="http://go.microsoft.com/fwlink/?LinkID=62293&amp;IIS70Error=404,0,0x80070002,9600">기타 정보 보기 &raquo;</a></p> 
       
     </fieldset> 
    </div> 
    </div> 
    </body> 
    </html> 
    



```python

```
