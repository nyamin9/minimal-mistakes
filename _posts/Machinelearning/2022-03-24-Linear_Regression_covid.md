---
title : "⚙ 6. [파이토치] Covid-19 데이터로 Linear Regression 구현하기(1)"

categories:
    - Machinelearning
tags:
    - [AI, Regression, Prediction, Mini_Batch]

toc : true
toc_sticky : true
use_math : true

date: 2022-03-24
last_modified_at: 2022-03-24
---  
* * *

🔨 오랜만에 머신러닝 포스팅이다!! 그동안 데이터 다루는 연습도 했고, 개강 후에는 인공지능 강의도 수강하면서 이론적으로, 프로그래밍적으로 생각할 수 있는 길이 넓어진 것 같다. 특히 파이토치를 배우면서 만들어둔 틀래스를 사용하는 게 정말 편하다고 다시 한번 느꼈다. 만들어주신 모든 분들께 박수를 보내고 싶다👏👏.  

🔨 이번 포스팅에서는 2020 년도의 전국/서울 이동에 따른 코로나 확진자 빈도를 다뤄볼 것이다. 일주일 전의 몇가지 데이터를 가지고 일주일 후의 코로나 신규 확진자를 예측하는 모델을 만들어보자.
* * *

## 1. 데이터 로드

```py
import pandas as pd
import matplotlib.pyplot as plt
import torch
import numpy as np
import random

d = pd.read_excel('C:\\Users\mingu\Desktop\\2020_covid19_KR_seoul.xlsx')
print(d.shape)
```
```
>> (321, 16)
```  

shape 함수로 데이터가 321일치의 데이터를 가지고 있는 걸 확인할 수 있다.  
대략적인 의미를 파악하기 위해 데이터프레임의 열을 한번 출력해보자. 학습데이터에 feature의 인덱스 값을 넣어줄 것이기 때문에 한눈에 알아보기 쉬운 형식으로 출력했다.  

```py
col_names = list(d.columns)
i = 0
for n in col_names:
    print(f'feature {i}: {n}')
    i=i+1
```
```
>>
feature 0: date
feature 1: retail_and_recreation_percent_change_from_baseline
feature 2: grocery_and_pharmacy_percent_change_from_baseline
feature 3: parks_percent_change_from_baseline
feature 4: transit_stations_percent_change_from_baseline
feature 5: workplaces_percent_change_from_baseline
feature 6: residential_percent_change_from_baseline
feature 7: seoul_retail_and_recreation_percent_change_from_baseline
feature 8: seoul_grocery_and_pharmacy_percent_change_from_baseline
feature 9: seoul_parks_percent_change_from_baseline
feature 10: seoul_transit_stations_percent_change_from_baseline
feature 11: seoul_workplaces_percent_change_from_baseline
feature 12: seoul_residential_percent_change_from_baseline
feature 13: confirmed_new
feature 14: deaths_new
feature 15: recovered_new
```  
데이터의 전체적인 정보를 알아보기 위해 .info() 함수를 호출해보자.  

```py
d.info()
```
```
>>
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 321 entries, 0 to 320
Data columns (total 16 columns):
 #   Column                                                    Non-Null Count  Dtype         
---  ------                                                    --------------  -----         
 0   date                                                      321 non-null    datetime64[ns]
 1   retail_and_recreation_percent_change_from_baseline        321 non-null    int64         
 2   grocery_and_pharmacy_percent_change_from_baseline         321 non-null    int64         
 3   parks_percent_change_from_baseline                        321 non-null    int64         
 4   transit_stations_percent_change_from_baseline             321 non-null    int64         
 5   workplaces_percent_change_from_baseline                   321 non-null    int64         
 6   residential_percent_change_from_baseline                  321 non-null    int64         
 7   seoul_retail_and_recreation_percent_change_from_baseline  321 non-null    int64         
 8   seoul_grocery_and_pharmacy_percent_change_from_baseline   321 non-null    int64         
 9   seoul_parks_percent_change_from_baseline                  321 non-null    int64         
 10  seoul_transit_stations_percent_change_from_baseline       321 non-null    int64         
 11  seoul_workplaces_percent_change_from_baseline             321 non-null    int64         
 12  seoul_residential_percent_change_from_baseline            321 non-null    int64         
 13  confirmed_new                                             321 non-null    int64         
 14  deaths_new                                                321 non-null    int64         
 15  recovered_new                                             321 non-null    int64         
dtypes: datetime64[ns](1), int64(15)
memory usage: 40.2 KB
```  

null값이 하나도 없는 깔끔한 데이터이다.  
같은 날짜에 대한 중복값이 있는지도 확인해보자.  

```py
d[d['date'].duplicated()]
```
```
>>
date	retail_and_recreation_percent_change_from_baseline	grocery_and_pharmacy_percent_change_from_baseline	parks_percent_change_from_baseline	transit_stations_percent_change_from_baseline	workplaces_percent_change_from_baseline	residential_percent_change_from_baseline	seoul_retail_and_recreation_percent_change_from_baseline	seoul_grocery_and_pharmacy_percent_change_from_baseline	seoul_parks_percent_change_from_baseline	seoul_transit_stations_percent_change_from_baseline	seoul_workplaces_percent_change_from_baseline	seoul_residential_percent_change_from_baseline	confirmed_new	deaths_new	recovered_new
```  

중복값 역시 존재하지 않는다. 아주 깨끗하게 정리된 데이터이기 때문에 곧바로 train set과 target으로 나누면 될 듯하다.
<<<<<<< HEAD

=======
>>>>>>> b9cb0fd5bf8e42a7ca7aaccf5c203f0f3394eee0

* * *
## 2. target / train set 정의  

🔨 <a>target</a> : 새로 확진된 사람의 수 - feature 13: confirmed_new  

🔨 1주 전의 데이터를 가지고 1주 후를 예측할 것이기 때문에 7번째 날부터의 데이터를 y로 가져올 것이다.  

```py
y = d.iloc[7:,13].to_numpy()
print(y.shape)
```
```
>> (314,)
```  

y.shape 에서 볼 수 있듯이 y의 데이터를 일반 array로 가져오고 있다. 아마 나중에 있을 loss function의 연산 전에 shape의 설정이 필요할 것 같다.  

🔨 이제 train set을 설정할 차례다. y보다 일주일 전의 데이터들로부터 일주일 후의 target을 예측하는 것이 목적이기 때문에 y보다 일주일 전의 데이터들만 가져오자. feature의 설정은 임의로 진행했다.  

🔨 target이 13번째 feature이므로 feature 13과 14,15는 제외하고 가져온다.  

```py
X = d.iloc[:-7,[1,3,4,7,8,9,10,12]].to_numpy()
print(X.shape)
```
```
>> (314, 8)
```  

저렇게 슬라이싱하는 걸로만 봐서는 무슨 소리인지 한번에 이해하기 힘들다. 그래서 과정을 아래처럼 도식화했다.

<p align="center"><img src="https://user-images.githubusercontent.com/65170165/159910754-3fb0b82a-a93d-4da5-b336-79e6dc04d9df.png" width="400" /></p>  
  

🔨 model 학습에 사용하기 위해 X, y를 모두 pytorch의 floattensor로 변환하자.  

```py
X = torch.tensor(X).type(torch.FloatTensor)
y = torch.tensor(y).type(torch.FloatTensor)
```
```py
# floattensor X의 shape 출력
X.shape
```
```
>> torch.Size([314, 8])
```
```py
# floattensor y의 shape 출력
y.shape
```
```
>> torch.Size([314])
```  
* * *
🔨 이렇게 해서 데이터를 가져오고, train_set(X)과 target(y)을 설정하는 것까지 끝냈다. 다음 포스팅에서는 이 데이터들을 가지고 학습모델을 만들어 보자.
