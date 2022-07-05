---
title : "🌵Pandas 4. pd.Categorical( )"

categories:
    - Pandas_func
tags:
    - [Pandas, DataFrame, Data, sort, category]

toc : true
toc_sticky : true

date: 2022-03-03
last_modified_at: 2022-03-03
---
* * *  

🌵 가끔씩 데이터 전처리를 하다 보면 <a>sort_values( )</a> 함수나 <a>sort_index( )</a> 함수를 써도 원하는대로 정렬이 이뤄지지 않는 경우가 있다. 이 상황은 apply( ) 로 임의의 함수를 데이터에 적용할 때 주로 생기는데, 이번 포스팅에서는 이를 해결하는 함수에 대해서 알아보자.  

## pd.Categorical( ) 함수

🌵 앞선 포스팅에서 다룬 pd. to_datetime( ) 함수에 dt 메서드를 적용하면 년, 월, 일, 요일 등 시계열과 관련한 다양한 column 을 만들 수  있다. 하지만 이때 생기는 요일은 0, 1, 2...6 의 형태를 가지기 때문에 데이터를 가공하는 과정에서 우리는 익숙한 형태로 바꾸는 경우가 자주 있다. 먼저 데이터프레임을 한번 확인해보자.  

```py
birth_date['weekday'] = birth_date['date'].dt.weekday
birth_date.head()
```
```
>>
        year	month	day	births	date	        weekday
0	1969	1	1	8486	1969-01-01	2
1	1969	1	2	9002	1969-01-02	3
2	1969	1	3	9542	1969-01-03	4
3	1969	1	4	8960	1969-01-04	5
4	1969	1	5	8390	1969-01-05	6
```  

블로그의 판다스 연습한 포스팅 중에 한 데이터를 긁어왔다.  

위의 데이터프레임에서 확인할 수 있듯이 요일을 의미하는 열인 weekday 가 숫자로 적혀있다. 그럼 이 weekday를 익숙한 형태로 먼저 바꿔주자.  

```py
def weekday_func(row):
    if row['weekday'] == 0:
        row['weekday'] = 'Mon'
    elif row['weekday'] == 1:
        row['weekday'] = 'Tue'
    elif row['weekday'] == 2:
        row['weekday'] = 'Wed'
    elif row['weekday'] == 3:
        row['weekday'] = 'Thu'
    elif row['weekday'] == 4:
        row['weekday'] = 'Fri' 
    elif row['weekday'] == 5:
        row['weekday'] = 'Sat'
    elif row['weekday'] == 6:
        row['weekday'] = 'Sun'
    
    return row

birth_date = birth_date.apply(weekday_func, axis = 1)
birth_date.head()
```
```
>>
        year	month	day	births	date	        weekday
0	1969	1	1	8486	1969-01-01	Wed
1	1969	1	2	9002	1969-01-02	Thu
2	1969	1	3	9542	1969-01-03	Fri
3	1969	1	4	8960	1969-01-04	Sat
4	1969	1	5	8390	1969-01-05	Sun
```  

이제 이 친구를 weekday 기준으로 정렬해보자.  

```py
birth_date = birth_date.sort_values('weekday', ascending = False)
birth_date
```
```
>>
        year	month	day	births	date	        weekday
0	1969	1	1	8486	1969-01-01	Wed
6274	1985	7	3	11698	1985-07-03	Wed
6281	1985	7	10	11640	1985-07-10	Wed
2220	1974	10	16	9211	1974-10-16	Wed
2213	1974	10	9	9505	1974-10-09	Wed
...	...	...	...	...	...	        ...
1606	1973	3	9	9100	1973-03-09	Fri
6636	1986	6	27	11286	1986-06-27	Fri
5542	1983	7	22	11043	1983-07-22	Fri
1613	1973	3	16	8899	1973-03-16	Fri
4770	1981	7	3	9717	1981-07-03	Fri

7305 rows × 6 columns
```  

우리에게 익숙한 월요일부터 일요일까지 정렬되는 느낌을 원했는데, 음 실패했다😅.  

이제는 Categorical 함수를 사용해보도록 하자.  

```py
birth_date['weekday'] = pd.Categorical(birth_date['weekday'], categories=['Mon','Tue','Wed','Thu','Fri','Sat','Sun'], ordered = True)
birth_date = birth_date.sort_values(['weekday', 'year'], ascending = True)
birth_date
```
```
>>
        year	month	day	births	date	        weekday
198	1969	7	7	10634	1969-07-07	Mon
219	1969	7	28	10548	1969-07-28	Mon
189	1969	6	30	10588	1969-06-30	Mon
241	1969	8	18	10650	1969-08-18	Mon
234	1969	8	11	10706	1969-08-11	Mon
...	...	...	...	...	...	        ...
7226	1988	1	31	8515	1988-01-31	Sun
7369	1988	6	19	9038	1988-06-19	Sun
7282	1988	3	27	8534	1988-03-27	Sun
7311	1988	4	24	8485	1988-04-24	Sun
7275	1988	3	20	8646	1988-03-20	Sun

7305 rows × 6 columns
```  

🌵 이왕이면 년도도 순서대로 보고 싶어서 year 와 weekday 를 동시에 sort_values 했고, 결과는 보는 바와 같이 성공이다👍. 함수 내에 정렬을 원하는 시리즈와 카테고리를 지정해주고, 정렬값에 True 를 배정해서 사용한다. 문법을 살펴보고 끝내도록 하자.  

### 🔑 함수 문법
* * *

<b>pd . Categorical ( 시리즈, categories = [카테고리리스트], ordered = True )</b>  

```py
ex1 ) Series = pd.Categorical(Series, categories = [⚪, ⚪, ⚪],ordered = True)  

ex2 ) birth_date['weekday'] = pd.Categorical(birth_date['weekday'], categories=['Mon','Tue','Wed','Thu','Fri','Sat','Sun'], ordered = True)
```  
* * *

🌵 가볍게 Categoical 함수에 대해서 살펴봤다. 함수 내에 일일이 카테고리 리스트를 순서지어 넣어줘야한다는 점이 커다란 데이터 분석에 사용하기에는 약간 걸리지만 datetime 함수와 엮어서 사용할 때는 훨씬 깔끔한 결과를 얻을 수 있다고 생각한다. 정렬을 해도 원하는 결과가 안 나올때는 이 함수를 써보는 건 어떨까😊!!
