---
title : "⚙ 8. [파이토치] Data_Loader"

categories:
    - Machinelearning
tags:
    - [AI, Regression, Prediction, Mini_Batch, Data_loader]

toc : true
toc_sticky : true
use_math : true

date: 2022-04-05
last_modified_at: 2022-04-05
---  
* * *  

🔨 저번 포스팅에서는 <a>dataloader</a>를 직접 구현해 학습을 진행했다. 하지만 모든 데이터에 대해서 각각을 바꿔주고 적용하는 게 쉽지 않기 때문에 파이토치는 이를 위한 기능을 제공한다.  

🔨 Dataset, DataLoader에 대해 알아보자.  

* * *
## 1. CustomDataset / DataLoader

🔨 필요한 라이브러리는 아래와 같다.  

```py
import torch
from torch.utils.data import Dataset, DataLoader
```  

여기에 pandas나 numpy, 다른 필요한 라이브러리를 추가하면 된다.  

이렇게 라이브러리를 가져왔으면 이젠 <a>CunstomDataset( )</a>를 정의해보자. 대략적인 코드는 아래와 같다.  

```py
class CustomDataset(Dataset):
    def __init__(self):

        
    def __len__(self):
    

    def __getitem__(self, idx):
```  

- <b>__init__(self)</b>
    - 필요한 변수들을 선언하는 메서드. 
    - input으로 오는 x와 y를 load 하거나, 파일목록을 load한다.  

- <b>__len__(self)</b>
    - x, y의 길이를 넘겨주는 메서드.  

- <b>__getitem__(self, index) </b>
    - index번째 데이터를 return 하는 메서드.  
  
🔨 이렇게 CustomDataset을 정의하고 난 뒤에는 그냥 이 클래스를 사용할 수 있다. 저번에 사용한 Covid_19 데이터를 사용해서 train_set과 label을 직접 가져와보자.  

* * *
## 2. 코드 작성 

```py
import pandas as pd
import matplotlib.pyplot as plt
import torch
import numpy as np
import random
from torch.utils.data import Dataset, DataLoader

d = pd.read_excel('C:\\Users\mingu\Desktop\\2020_covid19_KR_seoul.xlsx')

y = d.iloc[7:,13].to_numpy()
X = d.iloc[:-7,[2,6,5,4,7,8,3,12]].to_numpy()

X = torch.tensor(X).type(torch.FloatTensor)
y = torch.tensor(y).type(torch.FloatTensor)

# model parameter 설정
W = torch.zeros((8,1), requires_grad = True)
b = torch.zeros(1, requires_grad = True)

## hyper parameter 설정
lr = 0.0001
num_epochs = 50000
batch_size = 30

# linear Regression model 생성
def lin_model(X, W, b): 
    return torch.matmul(X, W) + b

# loss 함수 생성
def loss(y_hat, y):  
    return (y_hat - y.reshape(y_hat.shape))**2 / 2

# optimization algorithm 정의. 학습
def sgd(W, b, lr, batch_size):  
    with torch.no_grad():
        W -= lr * W.grad / batch_size         
        W.grad.zero_()                        
        
        b -= lr * b.grad / batch_size          
        b.grad.zero_()      
```  


🔨 이렇게 해서 데이터를 읽고, train_set(X)와 label(y)로 나눈 뒤에 각 모델과 파라미터들을 정의해주었다. 이제는 위에서의 <a>CustomDataset( )</a>을 사용해서 커스텀데이터를 가져오자.  

```py
class CustomDataset(Dataset):
    def __init__(self, data):
        self.features = X            # train_set
        self.labels = y              # labels
        
    def __len__(self):
        return len(self.features)
    
    def __getitem__(self, idx):
        features = torch.FloatTensor(self.features[idx])
        labels = torch.FloatTensor(self.labels[idx])
        return features, labels

# 커스텀데이터(d) 이름 : dataset    
dataset = CustomDataset(d)           

# dataloader : 커스텀데이터를 랜덤하게 섞어 batch_size 만큼 가져옴
dataloader = DataLoader(dataset, batch_size = batch_size, shuffle = True)
```  

🔨 제대로 가져왔는지 확인해보자. 만약 커스텀데이터 dataset을 <a>batch_size</a> 만큼 제대로 잘라서 가져왔다면 총 데이터의 길이가 314이고 batch_size는 30이므로 dataloader의 마지막 인덱스는 14의 길이를 가져야 할것이다.  

```py
i = 0
for batch_features, batch_labels in dataloader:
    print(i, '.\t', 'features: ', batch_features.shape, '\n\t', 
          'labels: ', batch_labels.shape, '\n')
    i += 1
```
```
>>
0 .	 features:  torch.Size([30, 8]) 
	 labels:  torch.Size([30]) 

1 .	 features:  torch.Size([30, 8]) 
	 labels:  torch.Size([30]) 

2 .	 features:  torch.Size([30, 8]) 
	 labels:  torch.Size([30]) 

3 .	 features:  torch.Size([30, 8]) 
	 labels:  torch.Size([30]) 

4 .	 features:  torch.Size([30, 8]) 
	 labels:  torch.Size([30]) 

5 .	 features:  torch.Size([30, 8]) 
	 labels:  torch.Size([30]) 

6 .	 features:  torch.Size([30, 8]) 
	 labels:  torch.Size([30]) 

7 .	 features:  torch.Size([30, 8]) 
	 labels:  torch.Size([30]) 

8 .	 features:  torch.Size([30, 8]) 
	 labels:  torch.Size([30]) 

9 .	 features:  torch.Size([30, 8]) 
	 labels:  torch.Size([30]) 

10 .     features:  torch.Size([14, 8]) 
	 labels:  torch.Size([14]) 
```  

아주 기깔나게 가져왔다😤😤!! 성공!!  

이제는 그냥 for문에서 학습만 시켜주면 된다.  

```py
for epoch in range(num_epochs):
    for batch_features, batch_labels in dataloader:
        l = loss(lin_model(batch_features, W, b), batch_labels)  
        l.sum().backward()
        sgd(W, b, lr, batch_size)

    with torch.no_grad():                              
        train_l = loss(lin_model(X, W, b), y)  
        if epoch%1000==0:
            print(f'epoch {epoch + 1}, loss {float(train_l.mean()):f}')
```
```
>>
epoch 1, loss 38003.773438
epoch 1001, loss 19245.431641
epoch 2001, loss 18821.029297
epoch 3001, loss 18648.062500
epoch 4001, loss 18526.097656
epoch 5001, loss 18416.160156
epoch 6001, loss 18331.574219
epoch 7001, loss 18309.513672
epoch 8001, loss 18262.779297
epoch 9001, loss 18163.675781

. . .

epoch 90001, loss 17740.068359
epoch 91001, loss 17673.435547
epoch 92001, loss 17668.839844
epoch 93001, loss 17686.435547
epoch 94001, loss 17681.234375
epoch 95001, loss 17723.623047
epoch 96001, loss 17675.238281
epoch 97001, loss 17684.761719
epoch 98001, loss 17671.396484
epoch 99001, loss 17670.376953
```  
```py
with torch.no_grad():
    y_hat = lin_model(X, W, b)
    train_l = loss(y_hat, y)
    print(f'average loss: {float(train_l.mean()):f}')

plt.figure(dpi=100)
plt.scatter(y,y_hat,2)
plt.scatter(np.arange(0,1200,10),np.arange(0,1200,10),1)
plt.grid('on')
plt.xlabel('real value (y)')

plt.ylabel('prediction (y_hat)')
plt.title(f'final loss {float(train_l.mean()):f}')
plt.show()

print('W:',W.detach())
print('b:',b.detach())
```
```
>> average loss: 17668.511719
```  

<p align="center"><img src="https://user-images.githubusercontent.com/65170165/161427606-f25a81b6-d127-43cb-b61e-58ee4728f3d8.png" width="600" /></p>  

```
>>
W: tensor([[ 21.0286],
        [ -5.6660],
        [ 11.9505],
        [-26.1676],
        [  0.5967],
        [ -0.3037],
        [ 10.0532],
        [ 48.4699]])
b: tensor([177.4695])
```  
* * *
🔨 이렇게 해서 파이토치의 모듈을 사용한 커스텀데이터셋을 만들고, 불러오는데 성공했다. 직접 구현해서 데이터를 불러오는 것이 원하는 데이터의 형태에 더욱 잘 맞고 에러가 생길 가능성도 적을 수 있지만, 그 형태가 특별히 생소한 경우가 아니라면 이렇게 만들어둔 오픈소스를 사용하는 것도 효율적인 방법일 수 있다고 생각한다.  

🔨 다음 머신러닝 포스팅에서는 softmax 를 통한 학습을 다뤄볼 것이다.
