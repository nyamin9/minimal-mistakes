---
title : "🧩 데이터마이닝 (26) - 패턴분석_6 : Project_Support, Confidence, Lift_(1)"

categories:
    - Data_mining
tags:
    - [Data, Data Mining, Pattern, itemset, support, confidence, lift]

toc : true
toc_sticky : true 
use_math : true  

date: 2022-09-26
last_modified_at: 2022-09-26  
---  
* * *  

🧩 저번 포스팅 이후 거의 20일 만의 포스팅이다. 이렇게 공백이 길었던 이유에는 뭐 추석 연휴다 개강 시즌이다 요런 이유가 있지만, 가장 큰 이유는 내가 정말 하고 싶은 게 무엇일지 생각하는 시간을 가져보았기 때문이다😥. 지난 학기에 바이오메디컬에 관련된 인공지능 수업과 데이터마이닝 수업을 들으면서 전공을 살리려면 대학원을 꼭 가야겠구나 생각하며 여름 방학을 맞았고, 이번 학기 초만 해도 뭐랄까, 대학원은 필수다 하는 생각만을 가지고 있었다. 마침 3학년 2학기이고, 대부분의 동기들이 이맘때쯤 자신이 가고 싶은 대학원 연구실을 찾으면 좋다는 조언을 해주었기에 나도 자연스럽게 이런저런 곳을 찾아보고 있었던 것 같다. 그런데 뭐랄까, 내가 하고 싶은 것은 의료 데이터에서 어떠한 인사이트를 뽑아서 환자 혹은 동일 질병 보유자들에게 적용하는 것이었는데 애초에 그와 관련된 연구실이 적기도 했고 그 주제를 주로 잡고 연구를 수행하는 경우가 드물었던 것 같다.<br>  

🧩 위에 언급한대로 내가 하고 싶은 것은 데이터 분석과 시각화를 통한 인사이트 도출이었기 때문에, 그냥 단순히 구글에 데이터 분석 이라고 검색했던 것 같다. 그리고 그 검색 결과가 나름 앞으로의 나의 진로를 정해준 검색이 되었다. 그 검색으로부터 나는 DA(Data Analyst) 와 BA(Business Analyst) 라는 직무를 알 수 있었고, 의외로 내가 학부에서 배운 파이썬, R, 통계학, 데이터마이닝 등이 많은 부분을 커버하는 도메인임을 알 수 있었다. 물론 SQL과 추가적인 BI 툴을 다룰 줄은 알아야겠지만, 어쩌면 지금의 나에게는 둘도 없이 흥미로운 직무라고 생각했다. 그래서 이렇게 진로를 정한 이후 며칠간 인터넷을 뒤지며 데이터 분석가가 되기 위한 준비사항을 찾아다니느라 바빴다😀😀.<br>  

🧩 요런 이유로 포스팅 공백기가 상당히 늦어졌다. 앞으로의 포스팅은 바이오와 관련된 분야보다는 SQL이나 순수 데이터 분석📊에 대한 내용이 많아질 예정이기 때문에, 아마도 조금의 준비시간을 거친 이후에 와다다닥 🏃‍♂️🏃‍♂️🏃‍♂️ 포스팅 할 것 같다. 그래도 공부하면서 틈틈이 데이터마이닝 관련 글은 조금식 올릴 생각이다😊.<br>  

🧩 뻘글이 길어졌다!! 이번 포스팅에서는 저번 학기에 수행한 프로젝트에서 support, confidence, lift를 통해 패턴을 분석하는 첫번째 시간을 가질 것이다. 먼저, 원래 있는 원본 데이터로부터 범주형 데이터와 트랜잭션 데이터를 만드는 과정을 살펴보자.<br>  

***  

## 1. support / confidence / lift 이론적 배경  

- 🐍 <b>Support</b>  
  - 지지도
  - x와 y를 동시에 포함하는 비율
  - 신뢰도(Confidence)를 지지하는 척도
  - confidence에 의한 규칙이 지지받기 위해서는 support 값이 높아야 함.<br>  
     
- 🐍 <b>Confidence</b> 
  - 신뢰도
  - x를 포함하는 거래 내역 중, y가 포함된 비율
  - 규칙의 신뢰도에 대한 척도
  - P(Y\|X)<br>  
    
- 🐍 <b>Lift</b> 
  - 신뢰도의 결과가 0.9라고 가정하였을 때 p(Y)가 0.9면 x,y가 서로 독립이 되기 때문에 x는 y를 설명하는 데에 아무런 도움을 줄수 없음
  - 규칙이 진짜 의미가 있는지 확인하기 위한 척도
  - P(Y\|X) / P(Y)
    - Lift = 1 : x와 y는 아무 관계가 없음. 독립.
    - Lift > 1 : x가 y의 발생 증가를 예측하는 데에 도움이 됨. (양의 상관관계).
    - Lift < 1 : x가 y의 발생 감소를 예측하는 데에 도움이 됨. (음의 상관관계).<br>  
      
## 2. Preprocessing  

🐍 우리가 가지고 있는 데이터는 범주형 자료와 수치형 자료가 이것저것 섞여있다. 패턴 분석을 통해 규칙을 찾기 위해서는 데이터가 <span style="background-color:#ffdce0">트랜잭션 데이터, 즉 Boolean 형태로 구성된 데이터</span>여만 한다. 따라서 우리는 각 attribute들을 일정한 기준을 가지고 모두 범주화 한 뒤, 최종적으로 이렇게 범주화된 데이터를 Boolean 표현형으로 바꿔 트랜잭션 데이터를 구할 것이다.<br>  

- 🐍 Support, Confidence 계산을 위해 데이터를 <span style="background-color:#ffdce0">transaction table 형태</span>로 변경<br>  
  - pre_tran : 각 attribute의 binary 값을 category 형태로 바꾼 dataframe 생성  
  - transaction : mlxtend 메소드의 transform 함수를 사용하여 boolean dataframe 생성<br>  
    
- 🐍 Support, Confidence 계산<br>  
  - <span style="background-color:#ffdce0">mlxtend.frequent_patterns 모듈의 apriori, association_rules 함수</span>  
  - <span style="background-color:#ffdce0">apriori()</span> : itemsets 간의 Support를 계산하여 dataframe으로 반환  
    - 설정한 min_support를 만족하는 경우만 반환  
  - <span style="background-color:#ffdce0">association_rules() 함수의 metric, min_threshold 옵션</span>  
    - 설정한 metric이 min_threshold 이상인 경우만 반환<br>  
    
- 🐍 우리가 찾고자 하는 것은 cardio와 영향을 미치는 attribute 간의 인과관계이기 때문에 cardio를 consequents로 하는 경우를 주로 살펴볼 예정임<br>  
  - confidence, Lift, support 순서로 우선순위를 설정  
  - <span style="background-color:#ffdce0">min_confidence = 0.6 / Lift > 1 / min_support = 0.01</span>  
  - support를 낮게 설정한 이유는 confidence와 Lift를 만족하는 경우에 antecedents의 support가 너무 작아 전체적인 support가 낮게 나오는 경우를 고려한 것이다.<br>  

## 3. Code  

### 🚩 3.1. Categorical Data Code  


```py
# BMI attribute를 위한 categorize 함수 생성
# BMI < 18.5 : 저체중
# 18.5 =< BMI < 25 : 정상
# 25 =< BMI < 30 : 과체중
# 30 =< BMI < 39.9 : 비만
# 39.9 =< BMI  : 고도비만S
def bmi(x):
    if x < 18.5:
        x = 'LOW'
    elif (x >= 18.5) & (x<25):
        x = 'NORMAL'
    elif (x >= 25) & (x < 30):
        x = 'OVER'
    elif (x >= 30) & (x < 39.9):
        x = 'OBESITY'
    else:
        x = 'HIGH_OBESITY'
    return x
```  

```py
# cardio 데이터 범주화
pre_tran = cardio.copy()

# gender : 1 2
pre_tran = pre_tran.replace({'gender':1},'Women')
pre_tran = pre_tran.replace({'gender':2},'Men')

# cholesterol : 1 2 3
pre_tran = pre_tran.replace({'cholesterol':1},'Normal_cho')
pre_tran = pre_tran.replace({'cholesterol':2},'Above_Normal_cho')
pre_tran = pre_tran.replace({'cholesterol':3},'Well_Above_Normal_cho')

# gluc : 1 2 3
pre_tran = pre_tran.replace({'gluc':1},'Normal_gluc')
pre_tran = pre_tran.replace({'gluc':2},'Above_Normal_gluc')
pre_tran = pre_tran.replace({'gluc':3},'Well_Above_Normal_gluc')

# smoke : 0 1
pre_tran = pre_tran.replace({'smoke':0},'No_Smoke')
pre_tran = pre_tran.replace({'smoke':1},'Smoke')

# alco : 0 1
pre_tran = pre_tran.replace({'alco':0},'No_Alcohol')
pre_tran = pre_tran.replace({'alco':1},'Alcohol')

# active : 0 1
pre_tran = pre_tran.replace({'active':0},'No_Active')
pre_tran = pre_tran.replace({'active':1},'Active')

# cardio : 0 1, target
pre_tran = pre_tran.replace({'cardio':0},'No_cardio')
pre_tran = pre_tran.replace({'cardio':1},'Cardio')

# ap_hi가 140이상이면 HBP_SYS(고혈압), 그 외에는 NBP_SYS(정상)
# ap_lork 90 이상이면 HBP_DIAS(고혈압), 그 외에는 NBP_DIAS(정상)
pre_tran["ap_hi"] = np.where(pre_tran["ap_hi"] >=140, 'HBP_SYS', 'NBP_SYS')
pre_tran["ap_lo"] = np.where(pre_tran["ap_lo"] >=90, 'HBP_DIAS', 'NBP_DIAS')

# age : 연령대로 분류
pre_tran.loc[pre_tran['age'] // 10 == 3, 'age'] = 30
pre_tran.loc[pre_tran['age'] // 10 == 4, 'age'] = 40
pre_tran.loc[pre_tran['age'] // 10 == 5, 'age'] = 50
pre_tran.loc[pre_tran['age'] // 10 == 6, 'age'] = 60

# BMI : 앞서 생성한 BMI 함수 사용
pre_tran['BMI'] = pre_tran['BMI'].apply(bmi)
print('row : ', len(pre_tran))
print('columns : ', len(pre_tran.columns))
pre_tran.head()
```  

```
>>
row :  64500
columns :  11
```  

<p align="center"><img src="https://user-images.githubusercontent.com/65170165/190163699-df8dfc0b-70a4-4447-ba27-52dc3c1113ef.png" width="1000" /></p><br>  

📌 위와 같은 과정을 거치면 우리가 가지고 있는 데이터가 모두 범주형으로 변하게 된다. 이제는 이 범주형 데이터를 트랜잭션 데이터로 만들어주면 된다.<br>  

***  

### 🚩 3.2. Transaction Data Code  

```py
# transaction table 생성
# mlxtend.preprocessing 모듈의 TransactionEncoder 임포트
from mlxtend.preprocessing import TransactionEncoder

# transaction 데이터 생성
# 범주형 데이터를 mlxtend 메소드의 transform 함수에 넣기 위해 list형태로 변환 : trans_data
trans_data = np.array(pre_tran)
trans_data = np.array(trans_data.tolist())

# transform() 함수로 trans_data가 one-hot encoding 된 형태의 boolean list를 te_ary로 받음
# te_ary를 데이터프레임 형태로 변환하여 transaction data 생성 
# transaction : attribute의 각 category에 대한 value를 column으로 받음
te = TransactionEncoder()
te_ary = te.fit(trans_data).transform(trans_data)
transaction = pd.DataFrame(te_ary, columns=te.columns_)
transaction
```  

<p align="center"><img src="https://user-images.githubusercontent.com/65170165/190164777-82916228-210e-4990-9c4b-c32433538ad1.png" width="1500" /></p><br>  

😥 어엇 사진이 잘 안보인다,,, 한번에 최대한 많은 attribute를 보여주고 싶어서 캡처를 저렇게 떴는데 아쉽다. 혹시 더 자세히 보고 싶으신 분들은 사진을 한번만 더 클릭해주시면 좋을 것 같다!!<br>

***  

🐍 드디어 원하는 형태의 트랜잭션 데이터를 만들었다. 이제부터는 이렇게 만들어진 데이터프레임에 대한 패턴을 분석해서 같이 나오는 친구들이 무엇이 있는지, 그 수치는 어떠한지에 대해서 분석하면 된다.<br>  


🐍 데이터에서 그 상관관계를 뽑아내서 사용하는 것 역시 중요하지만, 이를 위해서 전처리 단계를 진행하면서 데이터 분석에 대한 좀 더 넓은 시야를 가지게 되었던 것 같다. 수업시간에 배운 이론만을 바탕으로 정말 많은 삽질을 하면서 배운 방법들이기 때문에, 아마 두고두고 생각나지 않을까 싶다😀😀.<br>  


🐍 다음 글에서는 트랜잭션 데이터에서 support, confidence, lift를 구하고 시각화하는 부분을 다룰 것이다.<br>  

***  

