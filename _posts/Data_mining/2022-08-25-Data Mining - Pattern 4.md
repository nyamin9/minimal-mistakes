---
title : "🧩 데이터마이닝 (24) - 패턴분석_4 : Pattern Evaluation (1)"

categories:
    - Data_mining
tags:
    - [Data, Data Mining, Pattern, itemset, support, Lift, chi-square]

toc : true
toc_sticky : true 
use_math : true  

date: 2022-08-25
last_modified_at: 2022-08-25  
---  
* * *  

🧩 저번 포스팅들에서 support, confidence가 무엇인지 그리고 이를 사용해서 pattern을 발견할 수 있는 방법이 무엇인지에 대해서 알아보았다. 이번 포스팅에서는 내가 찾아낸 패턴이 데이터를 잘 설명하는가 평가하는 방법에 대해서 알아보도록 하자.  

***  

## 1. Pattern Evaluation Index  

🧩 어떻게 보면 패턴을 찾아내는 것보다 중요한 내용이라고 생각한다. 이번 포스팅과 다음 포스팅에 걸쳐서 다룰 것이기 때문에, 목차 느낌으로 한번 살펴보도록 하자.<br>  

- <b>1.</b> <span style="background-color:#ffdce0">Limitaion</span> of the Support-Confidence framework  
- <b>2.</b> Interestingness Measures : <span style="background-color:#ffdce0">Lift, chi-square</span>  
- <b>3.</b> <span style="background-color:#ffdce0">Null-Invariant</span> measures ⭐<br>  

🧩 앞선 두 포스팅에서 봤듯이 Pattern Mining은 다양한 Pattern과 Rule을 만들 수 있다. 하지만 내가 발견한 Rule들이 항상 의미있을 것이라 단정지을 수는 없고, 이에 더해서 좀 더 숨겨져 있는 것들을 찾아내기 위해 여러가지 measure를 알아본다고 생각하면 좋을 것 같다🙂. 이번 포스팅에서는 1,2 주제에 대해서 다룰 것이다.<br>  

## 2. Limitaion of the Support-Confidence framework  

🧩 이때까지 우리가 패턴을 찾기 위해 사용한 Measure는 <a>Support, Confidence</a> 이다. 하지만 이 친구들에게는 꽤나 큰 한계가 있다. 이 절에서는 그에 대해서 알아볼 것이다. 직관적인 이해를 위해 먼저 성별에 따른 정책 찬반에 대한 예시를 하나 살펴보자.<br>  

<p align="center"><img src="https://user-images.githubusercontent.com/65170165/186563425-768fa30c-9cdd-48aa-8f13-e667b7d66b95.png" width="600" /></p>  

🧩 이때 각 attribute의 itemset들 간의 관계를 알아볼 텐데, 일단 우리가 배운 건 support와 confidence 뿐이기 때문에 이를 통해서 구해보도록 하자.<br>  

<center>🔑 $A\rightarrow{B}(support,\;\,confidence)$</center><br>  

<center>📌 $1.\;\;Y\rightarrow{M}(400/1000,\;\,400/600)$</center><br>  
<center>📌 $2.\;\;N\rightarrow{M}(350/1000,\;\,350/400)$</center><br>  

🧩 이 예시에서 우리가 알고 싶은 것은 <span style="background-color:#ffdce0">Male과 attribute B 간의 연관관계</span>이다. <📌<b>1</b>>을 먼저 살펴보면 전체 표본에 대한 Male의 비율은 $750/1000=75\%$ 인 반면에, 1의 조건에 의한 confidence는 $400/600=66.7\%$ 로 떨어진 것을 알 수 있다. 또한 <📌<b>2</b>>의 조건에 의한 confidence는 $350/400=87.5\%$ 로 상승한 것을 알 수 있다. <span style="background-color:#ffdce0">즉, Support와 Confidence는 그 표본이 달라짐에 따라서 알고자 하는 비율이 크게 변동한다. 따라서 그 값만 가지고는 attribute 간의 정확한 Association Rule을 알아내기 어렵다.</span><br>  

🧩 위의 예제에서 알아본 이러한 한계 때문에 우리는 이를 보완할 수 있는 Measure 가 필요해졌다. 그리고 이로써 등장한 방법들이 다음 절에서 살펴볼 <span style="background-color:#ffdce0">Lift와 chi-square</span> 이다.<br>  
  

## 3. Interestingness Measures  
 

### 🚩 3.1. Lift  

***
  
🧩 Lift는 각 itemset가 <span style="background-color:#ffdce0">서로 positive 하냐 negative 하냐에 따라</span> 그 연관관계를 파악하는 방법이다. 계산식은 아래와 같다.<br>  

<center>📌 $Lift\;(A,\;B) = \frac{C\;(A\rightarrow{B})}{S\;(B)}=\frac{S\;({A}\cup{B})}{S\;(A)\;\times{\;S\;(B)}}$</center><br>  
<center>$when\;\;S(A)=support(A)\;\;and\;\;C(A)=confidence(A)$</center><br>  

<center>그리고 이때,</center><br>  

<center>$0\lt{Lift}\lt{\infty}$</center><br>  

<center>$If\;\;Lift(A,B)=1,\;\;A\;\;and\;\;B\;\;is\;\;Independent\;\;each\;\;other$</center><br>  

<center>$If\;\;Lift(A,B)\gt{1},\;\;A\;\;and\;\;B\;\;is\;\;Positively\;\;correlated$</center><br>  

<center>$If\;\;Lift(A,B)\lt{1},\;\;A\;\;and\;\;B\;\;is\;\;Negatively\;\;correlated$</center><br>  

***

🧩 앞서 다룬 예시를 가지고 직접 Lift 값을 구해보자.<br>  

<p align="center"><img src="https://user-images.githubusercontent.com/65170165/186563425-768fa30c-9cdd-48aa-8f13-e667b7d66b95.png" width="600" /></p>  

<center>📌 $1.\;\;Lift\;(Y,M)=\frac{S\;(Y\cup{M})}{S\;(Y)\;\times{\;S\;(M)}}=\frac{400/1000}{(600/1000)\;\times\;(750/1000)}=0.89\;\;\rightarrow{Negatively\;\;correlated}$</center><br>  

<center>📌 $2.\;\;Lift\;(Y,F)=\frac{S\;(Y\cup{F})}{S\;(Y)\;\times{\;S\;(F)}}=\frac{200/1000}{(600/1000)\;\times\;(250/1000)}=1.33\;\;\rightarrow{Positively\;\;correlated}$</center><br>  

🧩 위의 예시에서 얼핏 봤을 떄 정책의 찬반 여부에 좀 더 관련이 있어 보이는 성별은 M 이지만, 실제로 <span style="background-color:#ffdce0">Lift</span>를 통해 연관관계를 분석해보면 오히려 F가 더 positive한 관계가 있음을 알 수 있다.<br>  

***  
### 🚩 3.2. Chi-square ($χ^2-test$)  

***

🧩 Chi-square 에 대해서는 앞서서 데이터 전처리에 대해 배울때 이미 한번 짚었던 적이 있다. 링크를 첨부해 둘테니 이 포스팅과 같이 공부하면 도움이 될 것 같다.<br>  

[📝Chi-square Data prerocessing](https://nyamin9.github.io/data_mining/Data-Mining-Preprocessing-2/)<br>  

🧩 위에서 알아본 Lift는 그 값을 1을 기준으로 positive 한 관계가 있는지 negative 한 관계가 있는지를 파악하는 것이라면, Chi-square 는 단순히 연관관계가 있는지를 알아보기 위해 사용한다. 먼저 수식을 보도록 하자.<br>  

<center>📌 $χ^2=\sum{\frac{(Observed-Expected)^2}{Expected}}\;\;\;when\;\;\;Expected=\frac{A\;\times{\;B}}{N}$</center><br>  

<center>$and\;\;\;0\lt{χ^2}\lt{\infty}$</center><br>

🧩 그리고 이때 <span style="background-color:#ffdce0">chi-sqare 값이 클수록 두 itemset 간의 연관관계가 큼을 의미한다</span>. 즉, 독립이 아니다. 예시를 하나 보고 넘어가자.<br>  

***  

<p align="center"><img src="https://user-images.githubusercontent.com/65170165/186563425-768fa30c-9cdd-48aa-8f13-e667b7d66b95.png" width="600" /></p>  

📌 위에서 주어진 데이터는 Observed 값만 들어간 상태이다. 이 데이터에 Expected 값을 구해서 표기해주면 아래와 같다. 검은 글씨가 Observed, 빨간 글씨가 Expected를 나타낸다.<br>  

<p align="center"><img src="https://user-images.githubusercontent.com/65170165/186609123-88d15069-d8a1-40ec-9dee-6677a6b290c4.png" width="600" /></p>  

📌 이 전처리된 데이터를 가지고 chi-sqare 값을 구하면<br>  

<center>$χ^2=\frac{(400-450)^2}{450}+\frac{(350-300)^2}{300}+\frac{(200-150)^2}{150}+\frac{(50-100)^2}{100}=55.56$</center><br>  

📌 $55.56$ 이라는 굉장히 큰 값이 나온다. <span style="background-color:#ffdce0">즉, attrubute A와 B가 서로 독립일 가능성은 현저히 떨어진다는 결론을 얻을 수 있다.</span><br>  


***  

## 4. Limitation of Lift / chi-sqare  

🧩 이렇게 Lift 와 Chi-square 값들을 가지고 각 itemset의 연관관계를 보다 정확하게 알아낼 수 있다. 그렇다면 이 두 가지 방법이 어떠한 데이터에 대해서도 좋은 성능을 보여줄 수 있을까?? 예시를 한번 살펴보도록 하자.<br>  

<p align="center"><img src="https://user-images.githubusercontent.com/65170165/186640093-f1364fdd-fc04-4adf-a784-e21125a4ba61.png" width="650" /></p>  

📌 이 데이터에서 $Lift\;(Y,M)$ 값을 구하면 무려 $8.44$로 원래 기준치인 $1$보다 훨씬 큰 값이 나온다. 또한 Observed 값에 비해 Expected 값이 몹시 작은 경우 역시 존재한다.<br>  

🧩 우리가 앞서 살펴본 데이터와 이 데이터의 가장 큰 차이는 $N-F\;\;pattern$ 값의 차이이다. 즉, <span style="background-color:#ffdce0">두 itemset 모두 음성</span> (이 데이터에서 우리가 알고 싶은 것이 성별 M 에 대한 분석이므로 성별 F 를 음성이라고 하겠다🙂) <span style="background-color:#ffdce0">인 경우가 다른 경우에 비해 터무니없이 큰 값을 가지면 이런 경우가 생길 가능성이 높다</span>는 것이다.<br>  

🧩 그리고 이렇게 두 itemset이 모두 음성인 경우를 <span style="background-color:#ffdce0">Null-transaction</span> 이라고 한다. 정리하자면, <span style="background-color:#ffdce0">Lift 와 Chi-square 는 Null-transaction 의 영향을 너무 많이 받아 정확한 itemset 간의 관계를 파악하는 데에는 한계가 있다.</span> 따라서 실제로 우리가 찾고 싶은 패턴을 제대로 찾기 어려울 수 있다😥😥. 이에 다른 몇가지 방법들이 고안되었는데, 이 방법들을 Null-Invariant measure 라고 한다. 이를 다음 포스팅에서 다룰 것이다.<br>  

***  

🧩 이번 포스팅에서는 support 와 confidence 를 보완할 수 있는 두가지 방법인 Lift 와 Chi-square 에 대해서 알아보았다. 다음 포스팅에서는 이 친구들을 보완할 수 있는 몇가지 방법에 대해 알아보도록 하자🏃‍♂️🏃‍♂️.  

***  
<div style="text-align: left">💡위 포스팅은 한국외국어대학교 바이오메디컬공학부 고윤희 교수님의 [생명정보학을 위한 데이터마이닝] 강의 내용을 바탕으로 함을 밝힙니다.</div>  

***  

