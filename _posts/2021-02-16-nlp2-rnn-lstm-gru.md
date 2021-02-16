---
title: NLP2 RNN, LSTM, GRU
use_math: true
---

# Intro
자연어는 딥러닝으로 학습할 때 하나의 시퀀스를 가진 데이터로 간주한다.
한 단어를 예측하거나 생성하기 위해서 그 전까지의 단어들이 문장 안에서 영향을 주고 '문맥'을 형성하기 때문이라 생각한다.

시퀀스 데이터는 자연어만 있는것이 아닌, 날씨, 주가, 공장데이터 등 한 현재의 한 사건이 미래의 사건과 어느정도 연관이 되어있을 수 있다고 판단되는 일련의 데이터를 이야기한다.
따라서 시간과 순서를 고려할 수 있는 알고리즘으로 학습을 하였을 때 효과가있고 이전 정보들을 적절하게 처리하여 현재 데이터와 학습을 진행해야한다.

대표적인 알고리즘이 RNN이며 오늘은 RNN계열의 알고리즘에 대하여 알아보려한다.

# RNN
Recurrent Neural Network의 약자로 입출력을 시퀀스로 처리하는 대표적인 모델이다.

![]({{ 'assets/img/images/RNN.png' | relative_url }})


*출처: http://cs231n.stanford.edu/slides/2017/cs231n_2017_lecture10.pdf*

그림을 보면  $x$는 입력, 가운데 초록색 박스는 RNN hidden state, $y$는 출력이다. 그림으로 보면 RNN hidden state가 재귀적으로 업데이터되는것을 볼 수 있다.
하지만 저 안에서 대체 무슨일이 일어나는걸까??
## 계산과정

한동안 책으로 보아도 직관적으로 어떻게 계산되는지 알 수 없었는데 이를 보다 시각적으로 이해하기위한 그림을 그려보았다. (악필...)

![]({{ 'assets/img/images/RNN_1.jpg' | relative_url }})


![]({{ 'assets/img/images/RNN_2.jpg' | relative_url }})

핵심은 $h_{t+1}$을 만들기위하여 $x_{t}, h_{t-1}$이 사용되고 이를 $W$와 곱하는 과정이 필요하다. 
재귀적으로 $h_{n}$을 업데이트 하기때문에 업데이트 시켜야 할 weights가 별로 없을것 같지만 사실 시퀀스가 길어지면 길어질수록 업데이트 시켜야할 파라미터의 수는 급증한다.
~~그래서 파라미터가 그렇게 많았나...~~

Jaejun님의 블로그에도 RNN을 잘 설명하신 포스팅이 있어 참고로 남겨둔다.

(http://jaejunyoo.blogspot.com/2017/06/anyone-can-learn-to-code-LSTM-RNN-Python.html)

## 종류
![](http://karpathy.github.io/assets/rnn/diags.jpeg)

RNN은 입력과 출력의 갯수(시퀀스)에따라 종류가 나누어진다. ~~one to one은 어디에 사용되는지 잘 감이... 일반적이 nn과 모양과 쓰임이 비슷한것같다~~

one to many는 image captioning같이 하나의 이미지가 입력으로 주어졌을 때 그 이미지를 묘사하는 문장(시퀀스)을 출력하는 모델과 같은 용도로 사용된다.

many to one은 지난 포스팅에서 예시로 든 문장을 이용한 글의 클래스분류, 영화 감상평을 이용한 감정분석 등 하나의 문장이 입력으로 주어지고 출력이 단일차원인 경우이다.

many to many는 두가지로 나뉘는데 첫번째 many to many를 보면 입력시퀀스가 종료되었을 때 출력 시퀀스가 나오기 시작한다.
기계번역을 수행하는 경우 문장이 끝나지도 않았는데 미리 번역한 문장을 내보낸다면 좋은 번역문장을 출력할 수 없을것이다. 따라서 입력시퀀스가 끝난 뒤 출력시퀀스가 출력된다.

두번째의 경우는 입력이 들어가자마자 곧바로 출력이 되는것을 확인할 수 있다. 입력과 동시에 출력이 나오는 경우를 생각할 수 있는데 영상 내에서 특정 분류를 시행한다 하였을 때 프레임단위로 object detection을 하는경우처럼 입력함과 동시에 출력이 되는 경우를 생각할 수 있다.


## Backpropagation 문제

###  Resource
RNN과같이 시퀀스가 길게 늘어선 (수천, 수만개의 시퀀스) 데이터는 컴퓨터 메모리 리소스를 굉장히 많이 잡아먹는다. 
이를 어느정도 해결하기 위하여 고안된 방법이 BPTT이다.

![]({{ 'assets/img/images/BPTT.png' | relative_url }})

그림에서 볼 수 있듯이 전체 시퀀스를 한번에 backpropagation을 수행하는 것이 아니라 일정 블록을 끊어서 수행하므로써 리소스의 부족 현상을 어느정도 해결할 수 있다.

하지만 BPTT를 사용하더라도 RNN은 다른 문제를 갖고있다.


### Gradient vanishing, exploding

![]({{ 'assets/img/images/gradient_ex.png' | relative_url }})
위의 예시의 수식처럼 단 세번의 시퀀스만을 거쳤는데도 $h_{3}$에 대한 식이 길어진 것을 볼 수 있다.
$h_{1}$에 대하여 편미분한다고 생각한다면 겉미분, 속미분 등 여러 단계를 거치며 공비에따라 기울기가 점점 증폭되거나 0에 수렴할 가능성이 있다.
(위의 예제는 Gradient exploding의 경우이다. ~~시퀀스가 길어진다면~~) 

따라서 이를 보완하기 위하여 고안된 모델이 LSTM이다.


# LSTM

![](http://colah.github.io/posts/2015-08-Understanding-LSTMs/img/LSTM3-chain.png)
*출처:http://colah.github.io/posts/2015-08-Understanding-LSTMs/img/LSTM3-chain.png*

그림만보면 끔찍하다 ~~오늘 포스팅은 여기서 이만..~~

하나하나씩 차근차근 살펴보도록 하자.
우선 LSTM의 A라는 셀 안으로 두가지 정보가 흘러들어오는것을 확인할 수 있다.

박스의 왼쪽 위에서 흘러들어오는 정보를 Cell state, 왼쪽 아래에서 흘러오는 정보를 hidden state라 한다.

또한 노란색 박스는 4개의 게이트를 의미하는데 왼쪽부터 forget, input, gate, output게이트이다.(~~세번째 게이트를 '게이트'게이트라 한다~~)
게이트들은 이 전 셀로부터 온 정보들을 적절히 취합하고 필터링하는 역할을 한다.

![]({{ 'assets/img/images/LSTM.png' | relative_url }})

LSTM도 마찬가지로 RNN계열이다 (앞서 언급한 순수 RNN을 vanila RNN이라 부른다)
따라서 RNN과 마찬가지로 다음 hidden state, cell state를 만들 때 이 전의 hidden state와 cell state가 사용된다. 
다만 다른점은 네개의 게이트에서 정보를 취합, 필터링하고 이를 **더해**준다는 것이다.

## Forget gate

![]({{ 'assets/img/images/forget_gate.jpg' | relative_url }})

forget gate는 이전 hidden state와 inpu x를 RNN과 같이 weight으로 업데이트 한 후 이를 0부터 1까지의 값으로 정규화한다.
이를 이전 cell state와 곱해줌으로써 cell state의 정보의 일정 부분만을 사용하고 나머지는 *잊어버리는* 식의 계산이므로 이를 forget gate라 부른다.

## Input, gate gate

![]({{ 'assets/img/images/input,gate.jpg' | relative_url }})

input gate의 의도는 $x_{t}$의 정보를 dimension별로 어느정도 일부 덜어내어 보내주겠다는 의도이다.
gate gate는 들어온 입력을 -1 부터 1까지의 값으로 정규화하여 스케일을 맞춰주는 역할을 수행한다.
## Output gate
![]({{ 'assets/img/images/output.jpg' | relative_url }})
output gate는 현재 셀에서의 출력으로 나가고 다음 셀에 hidden state로써 흘러가는 정보이다.
따라서 현재 필요한 출력만을 시그모이드를 통해서 지금 당장 필요한 정보만 필터링된 상태라 이야기할 수 있다.
## 핵심
vanila RNN의 gradient관련 문제를 LSTM은 특정 게이트를 통하여 비선형 곱을 취하고 서로 *더해주는*과정을 거쳐 gradient를 **복사**하는 효과를 얻을 수 있다.
이는 ResNet에서 skip connection을 통하여 gradient vanishing을 해결하여 모델의 레이어를 더욱 깊게 쌓아 학습을 시키는 원리와 비슷한 원리라 생각한다.

따라서 기존 RNN의 문제를 보완하고 더 나은 학습이 진행되는것을 확인하였다.
