---
title : "🧩 데이터마이닝(22) 패턴분석_2 : Many Frequent Patterns"

categories:
    - Data_mining
tags:
    - [Pattern]

toc : true
toc_sticky : true 
use_math : true  

date: 2022-08-19
last_modified_at: 2022-08-19 
---  
* * *  

🧩 저번 포스팅에서는 패턴분석에 있어 가장 기초적인 개념들에 대해 알아보았다. 설명을 위해 사용한 예제에서 공통적으로 발견되는 패턴의 수가 그렇게 많은 예시들이 아니었기 때문에, 이번 포스팅에서는 패턴의 수가 많은 트랜잭션 데이터에 대해서 알아볼 것이다.  

* * *  

## 1. Too Many Frequent Patterns  

🧩 공통적인 itemset 패턴의 수가 많은 경우의 데이터를 압축하는 방법은 두가지가 있다. 첫번째 방법은 <a>Closed Patterns</a> 이고, 두번째 방법은 <a>Max-Patterns</a> 이다. 이제 이 각각의 방법에 대해 설명할 텐데, 설명을 위한 트랜잭션 데이터를 먼저 알아보도록 하자.<br>  

<center><b>트랜잭션 데이터 TDB</b></center><br>  

<center><b>T<sub>1</sub> : {a<sub>1</sub>,...,a<sub>50</sub>}</b></center><br>  
<center><b>T<sub>2</sub> : {a<sub>1</sub>,...,a<sub>100</sub>}</b></center><br>  
<center><b>minsup = 1</b></center><br>  

🧩 이 트랜잭션 데이터 T<sub>1</sub>, T<sub>2</sub> 에 대해서 공통적인 itemset, 즉 패턴을 찾아보자.<br>  

<b>1 - itemsets = {a<sub>1</sub>} : 2 / {a<sub>2</sub>} : 2 ...  {a<sub>50</sub>} : 2 / {a<sub>51</sub>} : 1 / {a<sub>52</sub>} : 1 ... {a<sub>100</sub>} : 1</b><br>  
<b>2 - itemsets = {a<sub>1</sub>, a<sub>2</sub>} : 2 ... {a<sub>1</sub>, a<sub>50</sub>} : 2 ...  {a<sub>1</sub>, a<sub>51</sub>} : 1 ... {a<sub>1</sub>, a<sub>100</sub>} : 1 ... {a<sub>99</sub>, a<sub>100</sub>} : 1</b><br>  
<b>...</b><br>  
<b>99 - itemsets = {a<sub>1</sub>, a<sub>2</sub>, ..., a<sub>99</sub>} : 1 / {a<sub>2</sub>, a<sub>3</sub>, ..., a<sub>100</sub>} : 1</b><br>  
<b>100 - itemsets = {a<sub>1</sub>, a<sub>2</sub>, ..., a<sub>100</sub>} : 1</b><br>  

👉 얼핏 보기만 해도 알 수 있겠지만, 정말 많은 패턴이 존재한다. 1개짜리부터 100개짜리까지 T<sub>1</sub>, T<sub>2</sub> 에서 구할 수 있는 모든 경우의 수를 따져보면<br>  

<center>$\begin{pmatrix}100\\1\\ \end{pmatrix}\;+\;\begin{pmatrix}100\\2\\ \end{pmatrix} +\;...\;+\;\begin{pmatrix}100\\100\\ \end{pmatrix}\;=\;2^{100}-1$</center><br>  

만큼의 경우의 수가 존재하며, 심지어 여기서 두개씩 존재하는 패턴을 고려히면 구해지는 패턴의 수는 천문학적인 숫자가 될 것이다. 따라서 이렇게 많은 패턴들을 압축할 방법이 필요하고, 이제 그 패턴들을 알아볼 것이다.<br>  

* * *  

### 🚩 1.1. Closed Patterns  

🧩 첫번째 방법인 <a>Closed Patterns</a> 에 대해서 먼저 알아보도록 하자. 기본적인 개념은 아래와 같다.<br>  

<center><a>X ⊂ Y 인 itemset_X 가 frequent하고, X와 support 가 같은 itemset_Y가 존재하지 않아야 한다.</a></center>  
<center><a>이때 우리는 itemset_X 가 closed 하다고 할 수 있으며, Y 는 Superset 이라고 할 수 있다.</a></center><br>  

🧩 설명이 굉장히 난감쓰하다...😥 위의 트랜잭션 데이터를 사용해서 Closed Pattern을 찾아보자<br>  

<center>itemset_X 를 {a<sub>1</sub>, a<sub>2</sub>, ..., a<sub>50</sub>} 이라 하면 X 의 support는 2 이며, itemset_Y 를 {a<sub>1</sub>, a<sub>2</sub>, ..., a<sub>100</sub>} 이라 하면 그때의 support는 1이다. 이때 X 는 Y의 부분집합이고, 두 itemset의 support가 서로 다르기 때문에 Y 를 superset 이라고 할 수 있으며, Closed Pattern 인 itemset_X {a<sub>1</sub>, a<sub>2</sub>, ..., a<sub>50</sub>} 가 T<sub>1</sub>과 T<sub>2</sub> 모두에 존재하기 때문에 우리가 구하는 closed pattern의 수는 2개가 된다.</center><br>  

🧩 이때 itemset_Y 가 {a<sub>1</sub>, a<sub>2</sub>, ..., a<sub>99</sub>} 인 경우라도 상관이 없지 않냐는 의구심이 들 수 있는데, 이 itemset_Y 는 결국 {a<sub>1</sub>, a<sub>2</sub>, ..., a<sub>100</sub>} 의 부분집합이기 때문에 Superset이라고 하기에는 적절하지 않다. 따라서 최대 범위를 포함하는 itemset을 선택하는 것이다.<br>  

🧩 또한 Closed Pattern은 Superset에서 공통적으로 나올 수 있는 모든 패턴을 고려하는 방법이기 때문에 <a>Lossless Compression</a> 이라고 한다.  

* * *  

### 🚩 1.2. Max-Patterns  

🧩 이번에는 두 번째 <a>Max-Patterns</a> 에 대해서 알아보도록 하자.<br>  

<center><a>itemset_X 가 frequent하고, X ⊂ Y 인 frequent super pattern Y가 없으면</a></center>  
<center><a>이때 우리는 itemset_X 가 max-pattern 이라고 할 수 있다.</a></center><br>  

🧩 이 친구 역시 예제를 살펴보자.<br>  

<center>위의 T<sub>1</sub>, T<sub>2</sub> 에서 max pattern의 개수는 다음과 같이 구해진다. 만약 Closed pattern을 구할 때 처럼 itemset_X 가 {a<sub>1</sub>, a<sub>2</sub>, ..., a<sub>50</sub>}가 되면 이를 포함하는 frequent super pattern Y가 얼마든지 존재할수 있기 때문에 이보다 큰 범위의 패턴을 찾아야 한다. 따라서 minsup = 1에 대해서 max-pattern을 구하면 {a<sub>1</sub>, a<sub>2</sub>, ..., a<sub>100</sub>} 가 될수 있으며, 그때의 개수는 1개가 된다.</center><br>  

<center> 반면에 minsup = 2인 경우에는 frequent 를 만족하는 최대 itemset_X 가 {a<sub>1</sub>, a<sub>2</sub>, ..., a<sub>50</sub>}이며, 이때 X를 포함하는 subset은 많지만 이 경우 minsup = 2를 만족할 수 없으므로 frequent 하지 않다. 따라서 이때의 max-pattern의 수는 2개이다.</center><br>  

🧩 Max-pattern은 Closed pattern과는 달리 공통적인 모든 패턴을 고려하는 것이 아닌 이를 포함하는 최대 패턴을 구하기 때문에 데이터의 손실이 생길 수 있다. 따라서 <a>Lossy Compression</a> 이라고 한다. 이에 대부분의 경우에 Max-pattern보다 Closed pattern을 쓰는 게 바람직하다😃.  

* * *  

🧩 이렇게 해서 너무 많은 패턴을 가진 경우에 대해서 데이터를 다루는 방법에 대해 알아보았다. 개념 자체가 꽤나 헷갈리고, 관련된 예시도 많은 편은 아니지만 위에 있는 것들만 제대로 이해하고 직접 minsup도 바꿔가면서 구해보면 생각보다는 쉽게 이해 할 수 있을 거라 생각한다.  

🧩 대부분의 패턴분석은 큰 데이터나 일련의 시퀀스에서 진행하는 경우가 많다. 따라서 이런 방법들을 효율적으로 진행하는 알고리즘이 필요한데, 다음 포스팅에서는 이 알고리즘에 대해서 알아보도록 하자🏃‍♂️🏃‍♂️.  

* * *  

<div style="text-align: left">💡위 포스팅은 한국외국어대학교 바이오메디컬공학부 고윤희 교수님의 [생명정보학을 위한 데이터마이닝] 강의 내용을 바탕으로 함을 밝힙니다.</div>
