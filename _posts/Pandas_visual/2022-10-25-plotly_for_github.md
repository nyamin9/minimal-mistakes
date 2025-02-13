---
title : "📊 Pandas 시각화 4 - plotly github 블로그에 출력"

categories:
    - Pandas_visual
tags:
    - [Pandas, plotly, Visualization]

toc : true
toc_sticky : true

date: 2022-10-25
last_modified_at: 2022-10-25
---  

***  

📊 중간고사가 끝났습니다. 그런 까닭에 오랜만에!! 이렇게 블로그에 글을 씁니다.  

📊 사실 저번 학기보다는 훨씬 편하긴 합니다. 일단 전공을 두 과목만 듣고 있고, 각고의 노력 끝에 최대한 시험없는 과목들로 수강신청을 했기 때문입니다. 그럼에도 프로젝트 두개의 압박감은 역시 함부로 무시할 수 없는 것 같네요... 괜히 겁먹을 필요는 없지만, 이미 겁을 잔뜩 집어먹은 상태입니다😥😥. 그래도 뭐 시험도 끝났으니 일단 이주는 좀 쉬엄쉬엄 살아봐야겠습니다!!<br>  

📊 오랜만의 글이라 어떤 내용을 다룰지 꽤나 고민이 많았는데, 오늘은 plotly 그래프를 우리의 깃허브 블로그에 출력하는 내용을 다루고자 합니다. plotly를 사용하는 이유는 기본 파이썬이 제공하는 시각화 도구보다 예쁜 시각화가 가능하기 때문입니다. 하지만 이보다 더 중요한 것은 사용자가 인터랙티브하게 그 그래프를 만질 수 있다는 것이라고 생각하기도 합니다. 하지만 지금까지 데이터분석 연습을 하면서 대부분 plotly와 iplot을 사용해서 시각화했는데, 이 친구들을 블로그에 포스팅 할 때마다 그냥 화면만 캡처해서 올린다는 게 항상 아쉬웠습니다. 구글링을 통해 알아낸 방법을 지금부터 소개하고자 합니다.<br>  

📊 이 방법은 [이 블로그](https://dschloe.github.io/python/python_edu/03_datavisualisation/ch_plotly_html/)를 참고하였습니다. 링크를 걸어두었으니 이 글도 참고하시면 도움이 될 것 같습니다.<br>  

***  

## 개요  

📊 plotly를 사용해신 분은 아시겠지만 인터랙티브하게 반응한다는 것은 정말 큰 도움이 됩니다. 가령 두 line이 겹쳐서 어느 line이 더 위에 있는지 알아내야 할 경우에, 일반 파이썬을 사용하는 경우에는 scaling을 통해 그 x, y축의 사이즈를 건드려줘야 할 텐데요, 우리의 plotly는 단순히 마우스만 가지고 줌을 땡겨 그래프의 세부사항을 쉽게 알 수 있게 도와줍니다.  

📊 나름 시각화를 좋아하는 사람으로써, 이를 단순히 캡처해서 올린다는 것은 있을 수 없는 일이라 생각합니다. 저와 같은 상황에 처하신 분들에게도 큰 도움이 됐으면 좋겠습니다.<br>  


## STEP1. plotly 회원가입  

📊 이미 plotly를 통해 출력한 그래프가 있다고 가정합니다. 아직 plotly를 잘 모르시는 분들은 [이 글](https://nyamin9.github.io/pandas_visual/plotly/)을 참고하시길 바랍니다.  
📊 인터랙티브한 그래프를 그대로 블로그에 올리기 위해, 이 그래프에 대한 URL을 만든다고 생각하면 될 것 같습니다. 이 URL을 plotly 클라우드에서 만들 수 있기 때문에 우선 plolty에 가입을 하도록 합시다.<br>  

🚩 [plotly 홈페이지](https://chart-studio.plotly.com/Auth/login/#/)에 가서 <span style="background-color:#ffdce0">가입</span>합시다. 저는 깃허브와 연동해서 가입했습니다.<br>  

🚩 가입하고 우측 상단의 본인 <span style="background-color:#ffdce0">계정 이름</span>을 클릭한 뒤 뜨는 창에서 <span style="background-color:#ffdce0">Settings</span>을 누르면 좌측 사이드바에 <span style="background-color:#ffdce0">API Keys</span>가 보일 텐데요, 클릭합시다.<br>  

🚩 그러면 API Setting을 위한 화면이 나타납니다. 여기서 <span style="background-color:#ffdce0">Regenerate Key</span>를 클릭해서 API Key를 만들어줍시다. 앞으로 사용할 키이기 때문에 기억하는 편이 좋을 것 같습니다.<br>  


## STEP2. 라이브러리  


📊 이 블로그에서 plotly와 iplot 시각화를 위해 사용한 라이브러리는 아래와 다습니다.  

```py
# plotly 라이브러리
import plotly.graph_objects as go
import plotly.offline as pyo
pyo.init_notebook_mode()
import plotly. io as pio

# iplot 라이브러리
import chart_studio.plotly as py
import cufflinks as cf
cf.go_offline(connected = True)
```  

📊 이 형식에서 벗어나지는 않지만, 한가지 추가해야 할 라이브러리가 있습니다.  

```py
# plotly 라이브러리
import plotly.graph_objects as go
import plotly.offline as pyo
pyo.init_notebook_mode()
import plotly. io as pio

# iplot 라이브러리
import chart_studio.plotly as py
import cufflinks as cf
cf.go_offline(connected = True)

# 추가
import chart_studio.tools as tls
```  

📊 앞으로의 plotly 작업은 이 라이브러리를 사용해서 진행할 것입니다!!<br>  


## STEP3. 그래프 URL 생성  

📊 URL 생성을 위해 먼저 우리의 이름과 API Key를 알려줘야 합니다. 이때 사용하는 코드는 아래와 같습니다.  

```py
username = 'username' # 본인 plotly user name
api_key = 'api key' # 본인 plotly api key
chart_studio.tools.set_credentials_file(username=username, api_key=api_key)
```  

📊 그 다음은 URL을 생성하는 단계입니다.  

```py
# filename은 본인이 다른 그래프와 구별할 수 있는 이름이면 됨
py.plot(fig, filename = 'plotly_PA_2013', auto_open=True)
```  
```
>> 'https://plotly.com/~nyamin9/7/'
```
- 저는 이 그래프를 URL로 만든 결과 https://plotly.com/~nyamin9/7/ 라는 링크가 만들어졌습니다.  
  
  
📊 깃허브 블로그에 포스팅하기 위해서는 마크다운 형태로 바꿔주는 것이 편합니다. 이때 `tls` 라이브러리를 사용합니다.  

```py
tls.get_embed('https://plotly.com/~nyamin9/7/')
```
```
>> '<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plotly.com/~nyamin9/7.embed" height="525" width="100%"></iframe>'
```  

- 이제 그냥 이 링크를 포스팅 마크다운 파일에 붙여넣으면 됩니다🙂. (작은따옴표는 제외)<br>  
  
***  

📊 그 결과 이렇게 이쁜 반응형 plotly 그래프가 나옵니다!!  

<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plotly.com/~nyamin9/7.embed" height="525" width="100%"></iframe>  

***  

📊 이 방법을 이제야 알게 되다니 너무 후회하는 중입니다😥 시각화를 다룬 모든 글이 그냥 캡처본이기 때문에 그 동안 상당히 불만족스러웠는데, 바꾸고 나니 속이 후련하긴 하네요!!  

📊 그래도 하나하나 알아가는 과정이 너무 재밌고 좋습니다. 당분간은 이렇게 고칠 점도 찾아보고 블로그 유지보수를 좀 해야겠다...는 다짐을 하면서!! 오늘의 포스팅을 마치도록 하겠습니다.  



***  

(2022.11.11 추가)<br>  

📊 예상치 못한 변수가 생겼습니다... plotly에서 제공하는 개인 디렉토리 할당량이 정해져 있어서 그걸 넘으면 아예 위의 방식이 진행이 되지 않더라구요... 그래서 어떻게 할지 고민하다가 학생 혹은 기업용 허가를 받으면 pro 버전을 사용할 수 있다는 것을 알고 빠르게 메일을 보냈습니다.  

<p align="center"><img src="https://user-images.githubusercontent.com/65170165/211176916-762f2459-3b33-4b64-a98e-16e70a46fe37.png" width="600" /></p><br>  


<p align="center"><img src="https://user-images.githubusercontent.com/65170165/211176946-78a03913-f145-4754-81d5-e3962421beed.png" width="600" /></p><br>  

📊 이렇게 며칠을 기다리다가 드디어 오늘!! 답장을 받았습니다.  

<p align="center"><img src="https://user-images.githubusercontent.com/65170165/211176967-43da3c8f-0fca-47ba-bd17-4f68f656d874.png" width="600" /></p><br>  

📊 보시면 아시겠지만 아쉽게도 pro 버전을 사용할 수는 없을 것 같습니다... 대시보드 앱을 사용하라고 했는데, 아직은 그 방식을 사용해본 적이 없어 우선 태블로 공부를 먼저 하고 plotly 대시보드도 공부해 봐야겠습니다.  

📊 비록 제가 원하던 결과는 아니지만, 그래도 이렇게 외국 기업과 메일도 주고 받아 본 게 나름 큰 경험이 된 거 같긴 해서 뿌듯하긴 하네요ㅎ 좀 더 열심히 살아봐야겠습니다!!  

***  



