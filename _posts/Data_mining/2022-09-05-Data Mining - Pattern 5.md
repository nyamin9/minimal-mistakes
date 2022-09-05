---
title : "🧩 Data Mining (25) - Pattern_5 : Pattern Evaluation (2)"

categories:
    - Data_mining
tags:
    - [Data, Data Mining, Pattern, itemset, null invariant]

toc : true
toc_sticky : true 
use_math : true  

date: 2022-09-05
last_modified_at: 2022-09-05  
---  
* * *  

🧩 오랜만에 블로그에 글을 쓴다. 개강하고 이것저것 처리할 것도 있었고, 알아볼 것도 있어서 잠깐 뜸했다. 이번 학기는 그래도 저번 학기보다는 살짝 여유롭게 수업을 들을 수 있어서 블로그에 좀 더 신경을 쓸 수 있을 것 같아 다행이다😊😊. 그동안 듣고 싶었던 파이낸스어낼리틱스와 생명정보학을 드디어 듣게 되서 기대가 되는 학기이다. 두근두근!!  

🧩 저번 포스팅에서는 support와 confidence를 보완할 수 있는 Lift와 chi-square test에 대해서 알아보았다. 하지만 이 친구들은 null ransaction의 영향을 너무 많이 받기 때문에 다른 방법이 필요했다는 것이 저번 포스팅의 내용이었다. 이번 포스팅에서는 그 다른 방법들인 <span style="background-color:#ffdce0">Null-Invariant Measure</span>에 대해서 알아볼 것이다.<br>  

***  

## 1. Null-Invariant Measure  

<p align="center"><img src="https://user-images.githubusercontent.com/65170165/186640093-f1364fdd-fc04-4adf-a784-e21125a4ba61.png" width="650" /></p>  

🧩 위의 데이터처럼 두 itemset이 모두 null 값을 가지는 경우에는 앞서서 배운 Lift와 chi-square test가 좋은 방법이 아닐 가능성이 크다. 따라서 앞서서 말했듯이 다른 방법들이 필요해졌고, 이때 사용하는 방법들과 수식은 아래와 같다.<br>  

📌  $Allconf(A,B)\;\;=\;\;\frac{s(A\cup{B})}{max(s(A),\,s(B))}\;\;and\;\;\;range\,:\,[0,1]$<br>  

📌 $Jaccard(A,B)\;\;=\;\;\frac{s(A\cup{B})}{s(A)+s(B)-s(A\cup{B})}\;\;and\;\;\;range\,:\,[0,1]$<br>  

📌 $Cosine(A,B)\;\;=\;\;\frac{s(A\cup{B})}{\sqrt{s(A)\times{s(B)}}}\;\;and\;\;\;range\,:\,[0,1]$<br>  

📌 $Kulczynski(A,B)\;\;=\;\;\frac{1}{2}(\frac{s(A\cup{B})}{s(A)}+\frac{s(A\cup{B})}{s(B)})\;\;and\;\;\;range\,:\,[0,1]$<br>  

📌 $MaxConf(A,B)\;\;=\;\;max(\frac{s(A\cup{B})}{s(A)}, \frac{s(A\cup{B})}{s(B)})\;\;and\;\;\;range\,:\,[0,1]$<br>  

🧩  null transaction의 영향을 받지 않는 이러한 방법들을 통해서 itemset A와 B 사이의 보다 정확한 관계를 표현할 수가 있다. 이 중에서도 특히 <span style="background-color:#ffdce0">Kulczynski Measure</span> 를 많이 사용한다. 이는 <span style="background-color:#ffdce0">두 itemset이 서로 얼마나 중립적인 관계를 가지고 있는지를 나타내는 것으로, 그 값이 0.5에 가까울수록 neutral 하다고 할 수 있다. 결과적으로 이들의 관계가 positive한지, negative한지도 알 수 있는</span> 중요한 방법이다. 두 itemset 간의 관계를 잘 표현하기 위해 이 방법과 동시에 사용하는 Measure가 하나 있다. 이를 <span style="background-color:#ffdce0">Imbalance Ratio</span> 라고 하는데, 수식은 아래와 같다.<br>  

📌 $Imbalanced\;Ratio\;=\;IR(A,B)\;=\;\frac{|s(A)-s(B)|}{s(A)+s(B)-s(A\cup{B})}\;\;and\;\;\;range\,:\,[0,1]$<br>  


🧩 IR 은 <span style="background-color:#ffdce0">두 itemset 중 하나의 발생빈도가 다른 것의 발생빈도보다 큰지 작은지를 나타내는 measure</span> 이다. 값이 0에 가까울수록 balanced, 1에 가까울수록 imbalanced 라고 할 수 있다.<br>  

⭐ Kulczynski를 통해서 데이터가 얼마나 neutral 한지는 알 수 있지만, 데이터나 itemset이 어느 한쪽으로 치우쳤는가 여부는 정확히 알 수 없기 때문에, IR을 함께 사용함으로써 두 itemset의 balance 함을 판단한다.<br>

***  

🧩 Kulczynski와 Imbalanced Ratio 를 통한 분석의 예를 보고 가도록 하자.<br>  

<p align="center"><img src="https://user-images.githubusercontent.com/65170165/188451921-724247a9-218e-44ac-b40b-665300eceed7.png" width="650" /></p>  

***  

🧩 결국 우리가 이때까지 배운 수많은 방법들 중에서 데이터가 null transaction의 영향을 많이 받을 수 있는 경우에도 가장 잘 적용될 수 있는 방법은 Kulczynski와 Imbalanced Ratio 라고 할 수 있을 것 같다😀😀.  

***  
🧩 이렇게 해서 패턴분석에 대한 내용까지 알아보았다. 다음 포스팅에서는 지난 학기에 진행한 프로젝트를 바탕으로 이 방법들을 어떻게 구현하고 결과를 분석할 수 있는지 알아보도록 하자.  

***  
<div style="text-align: left">💡위 포스팅은 한국외국어대학교 바이오메디컬공학부 고윤희 교수님의 [생명정보학을 위한 데이터마이닝] 강의 내용을 바탕으로 함을 밝힙니다.</div>  

***
