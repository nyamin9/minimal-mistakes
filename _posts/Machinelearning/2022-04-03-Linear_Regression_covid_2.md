---
title : "⚙ 5. [파이토치] Covid-19 데이터로 Linear Regression 구현하기(2)"

categories:
    - Machinelearning
tags:
    - [AI, Regression, Prediction, Mini_Batch]

toc : true
toc_sticky : true
use_math : true

date: 2022-04-03
last_modified_at: 2022-04-03
---  
* * *

🔨 앞선 포스팅에서 샘플링한 train_set(X)와 target(y)를 이용해 모델을 학습해보자.  

***
## 1. model, loss, optimization algorithm, data loader 정의  

```py
### 필요한 함수들의 정의 (model, loss, algorithm, data loader) ###

## 1.Linear Regression model 생성
# y = XW + b
# retrun predictions(= y_hat)

def lin_model(X, W, b): 
    return torch.matmul(X, W) + b

## 2.loss 함수 생성
# squared loss : ((predictions - labels)^2) / 2
# y는 torch.Size가 ([314]), y_hat은 matmmul 연산으로 인해 torch.Size가 ([314, 1])이므로 연산을 위해 reshape()함수 사용
# y를 y_hat의 shape으로 만들어서 연산

def loss(y_hat, y):  
    return (y_hat - y.reshape(y_hat.shape))**2 / 2


## 3.optimization algorithm 정의
# Minibatch stochastic gradient descent
# parameter(W,b) / learning_rate / batch_size 를 인수로 받음
# parameter(W,b) 업데이트

def sgd(W, b, lr, batch_size):  
    # with torch.no_grad() 구문 내에서는 gradient 연산 옵션을 꺼줌
    # sgd 함수 호출 순간의 gradient를 사용하겠다는 의미
    with torch.no_grad():
        W -= lr * W.grad / batch_size         # W.grad : W의 gradient 
        W.grad.zero_()                        # W의 gradient 0으로 초기화
        
        b -= lr * b.grad / batch_size         # b.grad : b의 gradient 
        b.grad.zero_()                        # b의 gradient 0으로 초기화
            

## 4.data loader 정의
# 학습데이터 X와 label y, batch_size를 인수로 받음
# 학습데이터의 길이(314)를 num_examples로 받음
# num_example의 range 리스트(0~313)를 indices로 받음
# indices를 random으로 섞음 - 데이터의 순서가 섞임

def data_iter(batch_size, X, y):
    num_examples = len(X)                     
    indices = list(range(num_examples))        
    random.shuffle(indices)                    
    
    # 학습을 위해 데이터를 batch_size 크기로 나눠서 받음
    # 전체 데이터를 한바퀴 순회함 : 1 epoch
    # batch_indices에 랜덤하게 섞은 indices를 슬라이싱해서 인덱스를 받아줌
    # 민약 i + batch_size가 총 데이터의 길이보다 길어지면 마지막 데이터까지만 받아옴
    # ex) i = 310, batch_size = 10이면 마지막에는 인덱스 310:314 까지만 슬라이싱해서 받음
    # yield 로 batch_size만큼의 X와 y를 반환해줌 
    
    for i in range(0, num_examples, batch_size): 
        batch_indices = torch.tensor(indices[i: min(i + batch_size, num_examples)])
        yield X[batch_indices], y[batch_indices]
```  

🔨 data_iter() 함수를 직접 구현해서 학습을 진행한다. 보통은 파이토치에서 제공하는 <a>Dataset, DataLoader</a> 모듈을 사용하는 편이지만, 배운 내용을 복습해보기 위해 구현했다. 모듈을 사용하는 방법은 다음 포스팅에서 다룰 생각이다.  

* * *
## 2. model parameter 초기화 / hyperparameter 설정
  

🔨 머신러닝에 있어서 몇가지 설정할 매개변수들이 있는데, 각 특징은 아래와 같다.  
- parameter : 실제로 모델의 학습 과정에서 업데이트되는 변수
    - <b>W</b> (weight)
    - <b>b</b> (bias)  


- hyperparameter : 업데이트되지는 않지만 모델의 예측에 영향을 주는 변수
    - <b>lr</b> (learning rate, 학습률)
    - <b>num_epochs</b> (epoch, 학습시행횟수)
    - <b>batch_size</b> (배치사이즈)  

```py
### model parameter 초기화 및 하이퍼파라미터 설정 ###

## parameter 초기화
# 각 parameter 업데이트를 위해 requires_grad = True 설정
# requires_grad = True 설정으로 W.grad와 b.grad에 각 parameter의 gradient가 저장되어 gradient로 사용가능함
# X와의 matrix multiplication 을 위해 X의 열의 개수와 W의 행의 개수를 맞춰줌

W = torch.zeros((8,1), requires_grad = True)        # Weight 초기값 0으로 설정
b = torch.zeros(1, requires_grad = True)            # bias 초기값 0으로 설정


## hyper parameter 설정

lr = 0.0001                  # learning rate 설정
num_epochs = 100000          # 시행횟수 설정
batch_size = 50              # 배치 사이즈 설정
```  

* * *  
## 3. 학습 - Minibatch Stochastic Gradient Descent
  
🔨 머신러닝 공부를 하다 보면 꼭 만나는 내용인 <a>gradient descent</a> 의 종류들이다. 쉬운 이해를 위해서 학습 시간을 임의로 설정했다🙄.  

- 1. <b>Gradient Descent</b> (,Batch Gradient Descent)
    - <a>데이터를 한 바퀴 다 돌면 한번 업데이트</a>
    - 업데이트 한번에 걸리는 시간 : 1시간  

- 2. <b>Stochastic Gradient Descent</b>
    - <a>데이터의 각 feature 하나 돌 때마다 업데이트</a>
    - 업데이트 한번에 걸리는 시간 : 1초  

- 3. <b>Minibatch Stochastic Gradient Descent</b>
    - <a>설정한 batch_size를 돌때마다 업데이트</a>
    - 업데이트 한번에 걸리는 시간 : 1분  

```py
### for loop를 이용한 model의 학습 ###

# 총 시행횟수 num_epochs 동안 for문 실행
# data_iter 함수의 반환값 X[batch_indices], y[batch_indices]를 각각 batch_features, batch_labels으로 받아서 squared loss 연산 진행
for epoch in range(num_epochs):
    for batch_features, batch_labels in data_iter(batch_size, X, y):
        l = loss(lin_model(batch_features, W, b), batch_labels)  
        
        # Compute gradient on `l` with respect to [`w`, `b`]
        #.backward()를 호출하여 loss에 대한 W와 b의 gradient를 계산하고 저장함
        l.sum().backward()
        
        # sgd함수 - parameter(W,b) 업데이트
        sgd(W, b, lr, batch_size)
        
    # with torch.no_grad() 구문 내에서는 학습가능한 parameter들을 업데이트 하지 않음
    # W, b의 업데이트 없음
    # train_l에 loss값을 받아서 epoch 1000번당 한번씩 loss의 평균을 출력함
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
* * *
## 4. 결과

🔨 학습결과를 아래와 같이 요약하자.
- 314개 example들에 대한 loss의 평균
- 314개 example에 대한 label, prediction의 scatter plot
- 학습이 완료된 model의 weight들의 값  

```py
# with torch.no_grad() - requires_grad = True 인 Tensor(weight, bias)의 업데이트를 멈춤
# linear regression model의 결과값을 y_hat(prediction) 으로 받음
# train_l에 loss를 받아서 loss의 평균을 출력함  
with torch.no_grad():
    y_hat = lin_model(X, W, b)
    train_l = loss(y_hat, y)
    print(f'average loss: {float(train_l.mean()):f}')

# scatter plot 생성
# x축에 실제 label(y) 값, y축에 prediction(y_hat) 값을 찍어줌. 점의 size는 2로 설정
# x축과 y축에 np.arange()함수를 이용해서 0~1200 구간의 배열을 간격 10을 기준으로 찍어줌. 점의 size는 1로 설정
# .grid('on') 옵션으로 배경에 격자무늬 생성
plt.figure(dpi=100)
plt.scatter(y,y_hat,2)
plt.scatter(np.arange(0,1200,10),np.arange(0,1200,10),1)
plt.grid('on')

# x축 이름, y축 이름, scatter plot의 이름 설정
plt.xlabel('real value (y)')
plt.ylabel('prediction (y_hat)')
plt.title(f'final loss {float(train_l.mean()):f}')
plt.show()

# 각 weight들의 값과 bias 값 출력
# .dethach()를 사용해 기존 Tensor에서 gradient 업데이트가 일어나지 않는 텐서를 생성하여 출력
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

🔨 마지막 scatter plot에서 볼 수 있듯이 실측값과 예측값 사이의 예측률이 많이 떨어지는 편이고, 그에 따라 선형관계도 거의 없음을 파악할 수 있다🤦‍♂️. 애초에 데이터 역시 linear하지 않았고, 충분한 학습을 시키기에는 train_set의 개수도 많은 편이 아니라서 위와 같은 결과가 나온 것 같다.  

* * *
🔨 이렇게 데이터를 train_set과 target으로 나누고 학습하는 일련의 과정을 저번 포스팅과 이번 포스팅을 통해 다뤘다. 다음 포스팅에서는 파이토치의 모듈을 가지고 데이터를 로드하는 법을 알아보자😉. 