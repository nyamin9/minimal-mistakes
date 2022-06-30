---
title : "🧩 Data Mining (2) - Distance 1"

categories:
    - Data_mining
tags:
    - [Pandas, Data, Data Mining, Distance]

toc : true
toc_sticky : true

date: 2022-06-30
last_modified_at: 2022-06-30
---  
* * *  

🧩 우리가 다룰 데이터에는 정말 많은 종류가 있다. 숫자로 표현된 형식도 있을 것이고, 혈액형처럼 범주형으로 표현된 데이터도 있을 것이다. 본격적으로 Distance measure에 대해 알아보기 전에, Data의 구조부터 알아보도록 하자.  

* * *  
  
## 1. Data 구조  
  
- Data Set은 <a>objects</a> (= observation)의 집합 : 행 (row)  
    - ex) customers / patients / students...  

- 각 data object 는 <a>attribute</a> (= features)로 표현됨 : 열 (column)  
    - object가 가지고 있는 속성  
    - ex) age / grade / department...  

``` 
    <Data Set>
                            features
                            1   2   3   4   ...
            objects  1
                     2
                     3
                     4
                     ...
```  
* * *  
## 2. Attributes Type  
 
- 1. <b>Categorical Data</b>  

    - Nominal  
        - categories / states / 이름 / <a>순서 없음</a>  
        - ex) Hair color / Blood type  
          
    - Binary  
        - <a>0 / 1</a> 의 두가지 state로만 표현되는 data. nominal type.  
        - <a>Symmetric binary</a> : 0 / 1 이 발생할 확률이 비슷하여 서로 대칭적인 데이터. ex) gender  
        - <a>Asymetric binary</a> : 0 / 1 이 발생할 확률의 차이가 큰 경우. 비대칭적인 데이터. ex) 질병의 양성 / 음성  

    - Ordinal  
        - ranking / <a>순서 있음</a>  
        - ex) size / grade / 설문조사 결과  


- 2. <b>Numerical Data</b>  
  
    - Discrete  
        - <a>정수</a> / 이산형 / 카운트 가능. ex) age  
        - binary type은 특별한 경우의 discrete data type 이다.  
      
    - Continuous  
        - <a>실수</a> / 연속형. ex) 기온 / 체온 / 키 / 몸무게  
          
* * *  
🧩 이번 포스팅에서는 data set의 구조와 자료형에 따른 분류에 대해 간략하게 알아보았다. 앞으로 사용할 많은 measure들이 자료형에 따라 다르게 적용되는 경우가 있기 때문에, 간단하지만 확실하게 알고 넘어가는 것이 중요할 거라고 생각한다😉.  
  
🧩 다음 포스팅에서는 데이터 상에서 object의 위치를 알아낼 수 있는 Quatile plot과 scatter plot에 대해 알아보자.  
  
* * *  
<div style="text-align: left">💡위 포스팅은 한국외국어대학교 바이오메디컬공학부 고윤희 교수님의 [생명정보학을 위한 데이터마이닝] 강의 내용을 바탕으로 함을 밝힙니다.</div>
