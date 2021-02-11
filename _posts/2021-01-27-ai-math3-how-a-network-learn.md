---
title: AI math3 How a network learn?
use_math: true
comments: true
tags:
- AI
---

# Intro 
neural net이 어떻게 학습하고 파라미터들을 업데이트하는지 학습하였다.
잘 알고있다고 생각한 부분에서도 역시나 생소한 부분이 나왔다. ~~공부를 제대로 하지 않았구나 이놈...~~

# Neural net
인공신경망의 구성은 Input layer & hidden layer & output layer로 구성되어있다 말할 수 있다.

![]({{ 'assets/img/images/neural_networkpng.png' | relative_url }})
*from Wikipedia 인공신경망*

지금 활발히 연구되고 활용되고있는 딥러닝이라는 분야는 hidden layer를 겹겹이 쌓아올려만든 다층구조로 되어있다. ~~겹겹이 쌓다보면 deep해지는 모양을 볼 수 있으므로 deep 이라는 단어가 붙은것같다~~


처음부터 네트워크가 어떻게 동작하는지 들어갈 수 있겠지만 우선은 그 전에 퍼셉트론부터 알아보자

## 퍼셉트론

퍼셉트론은 인공 신경망을 구성하고있는 최소단위라고 할 수 있다.

퍼셉트론은 [뉴런](https://ko.wikipedia.org/wiki/%EC%8B%A0%EA%B2%BD_%EC%84%B8%ED%8F%AC)을 모델링한것이다.
뉴런은 다른 뉴런들로부터 전달받은 신호를 일련의 처리과정을 거쳐 다른 뉴런으로 보내주는 역할을 하는 세포이다.

따라서 퍼셉트론역시 다른 퍼셉트론으로부터 받은 신호를 일련의 처리과정을 거친 후 다른 퍼셉트론으로 보내주는 역할을 한다.
조금 더 구체적으로 이야기하자면 인공 신경망에서 퍼셉트론은 들어온 값들을 전부 더하고 activation을 한 뒤 다른 퍼셉트론들로 전달한다.

![]({{ 'assets/img/images/percepton.png' | relative_url }})

위의 그림을 보자
input이 2개 ($x1, x2$) input과 곱해지는 weight이 2개($w1 ,w2$) output이 1개($y$)가 있다.

output $y$에는 $x1$에 weight $w1$이 곱해진 값과 $x2$에  $w2$가 곱해진 값이 들어간다.

따라서 $y = x1 * w1 + x2 * w2$라는 값을 갖는다.

## activation
위의 퍼셉트론에서 $y$는 다른 퍼셉트론으로 다시 값을 보내주어야한다. 
하지만 $y = x1 * w1 + x2 * w2$에서 볼 수 있듯이 $y$는 단순히 곱셈과 덧셈으로 이루어진 값이다. 즉, 선형구조로 되어있어 비선형 문제를 풀지 못한다는 한계가 있다. *[XOR 문제](https://medium.com/@jayeshbahire/the-xor-problem-in-neural-networks-50006411840b)*

따라서 이러한 문제를 해결하기위해 activation 함수를 취해 비선형성을 첨가하여 신경망의 한계를 극복하였다.
activation의 종류는 대표적으로 3가지이다.

![]({{ 'assets/img/images/activations.png' | relative_url }})
요즘 딥러닝에서 가장 많이 사용되는 activation은 ReLU함수이다.
(시그모이드, tanh 함수는 그레디언트값이 1 밑으로 계산되어 계속 곱해지다보면 Gradient vanishing이 발생한다.)


## Feed Foward!
위의 두 과정을 거친 것을 간단히 수식으로 표현하자면 $activation(x1 * w1 + x2 * w2)$으로 표현할 수 있을것이다. 인공 신경망은 이러한 수식이 병렬적으로  수십~ 수백만개의 연속이다.

neural net은 수많은 신경망이 아래와 같이 연결되어있는 구조이다.

![]({{ 'assets/img/images/networkgraph.png' | relative_url }})
*image from missinglink.ai*

이를 fully connected layer라 하며 마지막은 output노드들로 이루어져있다.
결론적으로 ouput을 산출하기위해 수많은 퍼셉트론은 자신에게 들어온 값들을 전부 더한 뒤 activation을 취해준 형태로 다음 레이어의 퍼셉트론들에게 전달하는 과정이다.

input에서 output까지의 과정을 feed foward라 부른다


## 오차를 줄이자 Backpropagation!!
지금까지의 과정은 단순히 입력이 있고 초기화된 weight들로 계산된 값들이 output으로 도출된다.
학습을 하는 과정, 다시말해 오차를 줄이는 과정이 들어가지 않았다. 

여기서 지난번 포스팅하였던 경사하강 알고리즘이 적용된다.
**어떻게??**

우선 우리가 수정할 수 있는 변수, 수정하지 못하는 변수를 미리 살펴보아야한다.
우리는 Input데이터를 변경할 수 없다. 여기서 변경할 수 없다는 이야기는 새로운 데이터를 추가할 수 없다는 이야기가 아니라 애초에 1이라 넣었던 값을 0.9로 수정할 수 없다는 이야기이다.~~바꾸면 데이터 조작이다!!~~

따라서 우리는 bias값과 위의 그림에서 화살표로 나타난 weight을 조정하여 output의 오차를 줄여야한다.

그렇기때문에 우리가 구해야 하는것은 weight들의 그레디언트이다.

하지만 층이 깊어지면 깊어질수록 input쪽에 가까운 Weight과 output의 관계를 설명하기는 어려워보인다.
여기서 chain rule이 등장한다.

![]({{ 'assets/img/images/backprop.jpg' | relative_url }})

따라서 계산된 그레디언트는 $W_{t+1} = W_t - {\partial y\over\partial W_t}$로 업데이트된다.

# outro

어렵다 어려워 
하지만 공부하면 할수록 피상적이었던 개념이 점점 구체화가 되어가는 느낌을 받는다.
매번 진행하는 피어세션에서도 조원들과 이야기를 나누다보면 나도모르게 레벨업을 하는 느낌도 있다.

AI 엔지니어의 길로.... 화이팅...
