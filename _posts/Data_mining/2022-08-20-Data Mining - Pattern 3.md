---
title : "🧩 데이터마이닝 (23) - 패턴분석_3 : Apriori Algorithm / ECLAT"

categories:
    - Data_mining
tags:
    - [Data, Data Mining, Pattern, itemset, support, Apriori]

toc : true
toc_sticky : true 
use_math : true  

date: 2022-08-20
last_modified_at: 2022-08-20 
---  
* * *  

🧩 이번 포스팅에서는 효율적인 패턴분석을 위한 <a>Apriori Algorithm</a> 에 대해서 알아볼 것이다. 지난학기에 진행했던 프로젝트에서도 꽤나 유용하게 썼던 만큼, 예시를 가지고 자세하게 알아볼 예정이다🙂🙂.  

***  

## 1. Efficient Pattern Mining Methods  

🧩 우선 패턴분석을 위해서 사용할 수 있는 방법들의 종류에 대해서 알아보자.<br>  

- Downward closeure property of frequent patterns  
- <a>Apriori Algorithm</a>  
- Extensions or Improvements of Apriori  
- Mining frequent patterns by exploding vertical data format (<a>ECLAT</a>)  
- Mining closed patterns<br>  

🧩 이 개념들 중에서 우리는 주로 <a>Apriori Algorithm</a> 과 <a>ECLAT</a> 에 대해서 배울 것이다.  
  

## 2. Downward closeure property of frequent patterns  

🧩 Apriori Algorithm 을 이해하기 위해 우선적으로 알아야 할 패턴분석의 특징을 알아보자.  

- <a>Apriori</a><br>  
    - <span style="background-color:#ffdce0">frequent itemset 의 모든 subset 은 반드시 frequent 해야 한다.</span>  
    - 만약 itemset_S 의 어떤 subset이 infrequent 하면 S 역시 frequent 하지 않다.  
    - 즉 subset중 하나라도 infrequent하면, 그 itemset 역시 infrequent 하다.<br>  

📌 간단하게 예를 들자면 itemset {B,D,N} 이 frequent 하면 {B,D}{B}{N} 등의 어떤 subset이라도 frequent 함을 의미한다는 것이다. 이 특징이 앞으로 알아볼 Apriori Algorithm 의 가장 기본적인 작동원리이기 때문에 확실히 알고 가는 것이 좋을 것 같다😃.  

## 3. Apriori  

🧩 트랜잭션 데이터를 스캔하면서 진행한다.  

- <b>1.</b> 데이터의 frequent한 1 - itemset부터 찾기 시작한다.<br>  
- <b>2.</b> 아래의 과정을 <span style="background-color:#ffdce0">반복</span>한다.<br>  
    - <b>2.1.</b> frequent K - itemset 으로부터 <span style="color:red">(K+1) - itemset</span> 의 후보군을 형성한다.  
    - <b>2.2.</b> 생성한 후보들로부터 <span style="color:red">frequent (K+1) - itemset</span> 을 선택한다.  
    - <b>2.3.</b> <span style="color:red">K 를 K+1 로 설정</span>하여 다시 위의 과정을 반복한다.<br>  

- <b>3.</b> 더 이상 frequent 한 set을 만들지 못할 때까지 <span style="background-color:#ffdce0">반복</span>한다.<br>  
- <b>4.</b> 구한 모든 frequent 한 itemset 을 <span style="background-color:#ffdce0">리턴</span>한다.<br>  

🧩 예제를 하나 살펴보도록 하자!!  

* * *  


### 🚩 3.1. Apriori Algorithm - example  

🧩 트랜잭션 데이터에 대해 <span style="background-color:#ffdce0">minsup = 2</span> 로 주어진 예시이다. 풀이에서 <a>$C_{n}$</a> 은 candidate-n-itemset을 의미하며, <a>$F_{n}$</a> 은 frequent-n-itemset 을 의미한다.<br>  

<p align="center"><img src="https://user-images.githubusercontent.com/65170165/185737293-f80e2f02-a6b8-46c0-bc8f-67163cd381cd.png" width="1000" /></p><br>  

📌 위의 풀이과정을 보면 ① / ③ / ⑤ 에서 먼저 <span style="background-color:#ffdce0">K+1 candidate itemset</span> 을 구해주고,  ② / ④ / ⑥ 에서 순차적으로 <span style="background-color:#ffdce0">K+1 frequent itemset</span> 을 구한다. K = 1 부터 시작해 점점 K를 증가시키면서 frequent itemset 을 구해주고, 더 이상 frequent itemset 이 없으면 알고리즘이 끝난다.<br>  

📌 위에서 <span style="background-color:#ffdce0">$F_1$ 에서 $C_2$ 로 넘어갈 때와 $F_2$ 에서 $C_3$ 로 넘어갈 때</span> 주목해야 하는 부분이 있다. 바로 $F_1$ 과 $F_2$ 각각에서 가능한 조합만을 고려해서 candidate itemset 을 만들어 줘야 한다는 것이다. 어차피 우리가 찾고자 하는 것이 frequent itemset 이기 때문에, <span style="color:red">frequent itemset 의 모든 subset 은 반드시 frequent 해야 한다</span> 는 Apriori Algorithm 의 기본 원리에 의해 불필요한 연산 과정을 줄이고자 하는 것이다. 이때 주의할 점은 단순히 만들 수 있는 조합이 아니라 각 itemset 간의 <span style="background-color:#ffdce0">관계</span> 가 있어야 한다는 점이다. 무슨 소린가...😨😨 하시는 분들이 없지는 않을 것 같아 $F_2$ 에서 $C_3$ 로 넘어가는 과정을 통해 설명하도록 하겠다.<br>  

📌 <span style="background-color:#ffdce0">$F_2$ - {AC} / {BC} / {BE} / {CE}</span> 에서 얼핏 생각하면 {AC}와 {CE} 둘 다 있으니 당연히 {ACE}를 만들 수 있을 것 같고, {AC}와 {BC} 모두 있으니 {ABC} 도 만들 수 있을 것 같지만, 그렇지 않다. 왜냐하면 첫번째 경우에는 A와 E의 관계가 없고, 두번째 경우에는 A와 B의 관계가 없기 때문이다. 즉, 단순 조합보다는 각각의 관계를 설명할 수 있는 경우에 순차적인 frequent itemset 을 구할 수 있다. 따라서 위의 경우 만들수 있는 조합은 <span style="background-color:#ffdce0">{BCE}</span> 하나 뿐이다🙃🙃.<br>  

* * *  

## 4. Implementation Tricks  

🧩 앞서 말한 조합에 대해서 조금 더 알아보도록 하자. 이제 이 과정은 <a>self-joining / pruning</a> 이라는 두가지 개념으로 나눠진다.<br>  

- <a>self-joining</a> : 기존 frequent itemset 이 가지고 있는 items 만을 사용해서 candidate itemset 을 만들기 위한 연산량을 줄인다.<br>  
- <a>pruning</a> : candidate itemset 을 만들 때 가능한 관계만을 사용해서 만들기 위해 자체적으로 만들 수 없는 itemset 에 대한 가지치기를 진행한다.<br>  

🧩 간단하게 예시를 하나 살펴보도록 하자.<br>  

<center>$with\;\;\;F_3 = \{\;\,abc.\;\,abd.\;\,acd.\;\,ace.\;\,bcd.\;\,\},\;\;\;Make\;\;F_4.$</center><br>  

📌 만들어야 할 것이 4 - itemset 이기 때문에, 얼핏 보면 뭔가 굉장히 복잡해 보인다. 3개의 item 들 간의 관계를 가지고 4 개의 item 을 만들어야 하기 때문이다. 하지만 이 역시 차분히 생각해보면 그렇게 어려운 것은 아닐 거라고 생각한다.<br>  

- 먼저 우리는 abc 와 abd, bcd 를 가지고 있고, 이 itemset 들 간에는 <span style="background-color:#ffdce0">a - b의 관계, a - c 의 관계, b - c 의 관계, b - d 의 관계, a - d 의 관계, c - d 의 관계</span> 가 모두 들어 있기 때문에 <span style="background-color:#ffdce0">abcd</span> 을 만들 수 있다.<br>  

- 하지만 우리가 <span style="background-color:#ffdce0">acde</span> 를 만드려고 하는 경우를 생각해보자. acd, ace 를 통해 a - c, a - d, c - d, a - e, c - e 의 관계를 알 수는 있지만, 위에 있는 3 - itemset 에는 d - e 의 관계를 알 수 있는 ade, cde 등이 없다. 따라서 우리는 위에 있는 경우를 가지고 acde 를 만들 수는 없다🙄🙄.<br>  

🧩 살짝 헷갈리는 개념이다. 나도 중간고사에 나온 이 문제를 보기 좋게 틀려서 기말고사 공부 할때 한번씩 더 봤던 기억이 난다. 물론 이런 알고리즘이 이미 컴퓨터에 다~~ 구현되어 있으니 사용하는 데 어려울 것이라고 생각하지는 않는다. 프로젝트에서도 사용을 했기 때문에, 패턴분석에 대한 내용을 정리할 때 한 번 더 살펴보도록 하자🙃🙃.<br>  

## 5. Exploding Vertical Data Format (ECLAT)  

🧩 패턴분석의 연산 속도 향상을 위한 방법이라고 생각하면 될 것 같다. 트랜잭션 데이터의 형태를 살짝 바꾸는 방법인데, 한번 살펴보도록 하자.<br>  

<p align="center"><img src="https://user-images.githubusercontent.com/65170165/185749680-f8f2a954-eefc-48f9-87b0-1a2bab785f98.png" width="750" /></p>  

📌 컴퓨터가 데이터를 인식하는 방식이 숫자 기준이기 때문에, itemset을 그 자체로 인식하기 보다는 숫자를 통한 좌표로 인식하는 방식이 연산 과정이 훨씬 빠르고, 가볍다. 이렇게 간단하게 데이터를 변형하는 것만으로도 연산속도가 빨라질 수 있다는 것에 초점을 맞추면 좋을 것 같다.  

* * *  

🧩 정말 중요한 개념을 배웠다. 프로그래밍을 통해서는 간단한 함수만으로도 구현이 가능하지만, 어쨌든 이 알고리즘을 생각해 낸다는 점이 참 대단하다고 생각한다. 그 멋진 분들 덕분에 이렇게 편리한 세상에 살 수 있다는 점에 새삼 감사함을 느낀다🙂🙂.  

🧩 이렇게 패턴을 분석하는 법은 한번씩 짚어 보았다. 다음 포스팅에서는 어떻게 보면 분석보다 더 중요한 점이라고 할 수 있는 Pattern Evaluation 에 대해 알아보도록 하자🏃‍♂️🏃‍♂️.  

* * *  

<div style="text-align: left">💡위 포스팅은 한국외국어대학교 바이오메디컬공학부 고윤희 교수님의 [생명정보학을 위한 데이터마이닝] 강의 내용을 바탕으로 함을 밝힙니다.</div>
