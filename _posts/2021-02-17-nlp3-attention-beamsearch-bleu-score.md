---
title: NLP3 Attention, Beamsearch, BLEU score
use_math: true
tags:
- NLP
---

# Intro

![](https://wikidocs.net/images/page/24996/%EC%9D%B8%EC%BD%94%EB%8D%94%EB%94%94%EC%BD%94%EB%8D%94%EB%AA%A8%EB%8D%B8.PNG)

*출처: 위키독스 딥러닝을 이용한 자연어 처리 입문*

seq2seq 모델은 위와같은 구조를 갖고있다. 
시퀀스데이터가 주어지면 그것을 LSTM으로 인코더를 학습시킨 후 인코딩된 hidden state vector, cell state vector를 디코더에 넣어 다른 시퀀스 데이터를 출력해내는 구조이다.

하지만 시퀀스가 길어지면 길어질수록 처음에 학습되었던(sequnce의 초반 단어) 데이터는 시퀀스를 거듭할수록 사라지거나 희미해질것이다. 
또한 시퀀스가 아무리 길더라도 디코더에 주는 입력은 결국 하나의 벡터이기때문에 긴 시퀀스의 데이터가 하나의 벡터에 우겨넣어질것이다.

때문에 정상적인 학습이 어렵고 정보를 점차 잃어가게된다.

이를 해결하기위해 고안된 방법이 Attention이다.

# Attention
언급한바와같이 LSTM 인코더는 크게 두가지 문제가 있다

- 인코딩된 hidden state가 시퀀스가 길어지면 길어질수록 그 정보가 희미해 진다는점
- 한정된 벡터에 모든 시퀀스의 정보를 우겨넣어야 한다는점

이를 어떻게 해결할 수 있을까?

요약해서 말하자면 **디코더의 입력벡터**와 **인코더의 모든 hidden state와의 유사도를 계산, 가중평균을 통해 얻어진 벡터** 이 두 벡터를 이용하여 output을 뽑아내게된다.

그림으로 보도록 하자

![]({{ 'assets/img/images/seq2seq-6.jpg' | relative_url }})

위의 그림을 보면 디코더는 인코더로보터 모든 정보가 함축된 $h_{0}$라는 입력과 start 토큰을 통하여 하나의 임베딩 벡터가 만들어낸다(초록색 벡터). 그 후 초록색 벡터와 인코더의 hidden state들과 유사도를 구한 뒤 이를 softmax를 통하여 가장 유사도가 높은 벡터에 가중치를 두어 하나의 attention output을 만들어내는것을 확인할 수 있다.(context 벡터라 부른다.)

따라서 인코더에서 시퀀스가 길어지며 처음의 정보가 희미해진 $h_{0}$뿐만아니라 그 전 $h_{1} ~ h_{n}$까지의 정보를 모두 활용할 수 있어 단지 벡터 하나를 입력으로 받는것이 아닌 전체 시퀀스의 문맥을 확인할 수 있다는 장점이 있다.

![]({{ 'assets/img/images/seq2seq-12.jpg' | relative_url }})

또한 Attention은 인코더로의 Backpropagation shortcut을 만들어줌으로써 Gradient Vanishin현상도 완화할 수 있다.

Attention 구조를 이용하면 한가지 재밌는사실이 있다.

각 디코더 벡터들이 어떠한 hidden state에 가중을 두어 context 벡터를 만들었는지 추적하면 어떠한 단어가 다음 시퀀스를 출력하는데 영향을 주었는지 
해석할 수 있는 여지를 제공하는 것이다.

![]({{ 'assets/img/images/seq2seq-16.jpg' | relative_url }})
*Attention은 디코더가 어떠한 단어에 가중치를 두어 학습하였는지 해석할 여지를 제공한다*


# Beam Search
seq2seq 모델이 inference단계에서 문장을 출력하는 방식은 수많은 단어들중 각 타임스텝에서 가장 확률이 높은 단어 하나를 출력하는 그리디 방식이다.
하지만 부분해가 전체의 최적해를 보장하지 않는다.

이를 해결하기위해선 모든 경우의 수를 완전탐색처럼 전부 수행한 후 출력을 내놓을 수 있지만 그 경우 $O(V^t)$시간이 걸리므로 이 역시 불가능하다.

Beam search는 차선책으로 K개의 경우를 찾아보는 경우이다.

각 타임스텝마다 K 개의 후보들을 두고 이를 비교하여 가장 확률이 높은 문장을 출력하는 간단한 방식이다.

![]({{ 'assets/img/images/beamsearch.jpg' | relative_url }})

각 후보군에서 K개의 확률을 계산 그중 Joint probability가 가장 높은 K개 선택 , 다시 K개의 후보군 선택의 반복이다.

각 후보는 End token이 생성되거나 모델 설계자가 지정한 일정 타임스텝을 수행하면 Joint probability를 계산한 뒤 최적의 문장을 선택한다.
하지만Joint probability는  $\sum{logP_{LM}(y_{i} \mid y_{1}, y_{2}, \cdot, y_{i-1},  x)} $ 와 같이 계산되기때문에 end token이 먼저 생성된 짧은 문장이 더 높을 수 밖에 없다.

이를 막기위해 전체 시퀀스의 길이(단어 길이)로 나누어주어 정규화 시켜 비교하여 최적해를 찾아낸다. 

*최종 식*

$score(y_{1}, \cdot, y_{t}) = \frac{1}{t}\sum{logP_{LM}(y_{i} \mid y_{1}, y_{2}, \cdot, y_{i-1},  x)} $

# BLEU score
이렇게 출력된 문장들을 어떻게 평가할것인지 역시 이슈중 하나이다. 

![]({{ 'assets/img/images/nlp_loss.jpeg' | relative_url }})

위와 같이 예측문장이 전부 틀렸다고 이야기할 수 있을까? 
출력단어의 순서가 올바른 자리에 위치하지않거나 위의 그림처럼 한칸씩 shift되어있다해서 전부 틀렸다 이야기할 수 없을것이다.

때문에 처음 제안된 방식이 precision, recall이다.

$precision = \frac{맞힌 단어의 갯수}{예측 문장 단어의 갯수}$


$recall = \frac{맞힌 단어의 갯수}{레이블 단어의 갯수}$

$F - measure = \frac{precision \times recall}{\frac{1}{2}(precision + recall)}$

하지만 위의 수식들은 '내가 예측한 문장과 정답 문장의 단어가 얼마나 많이 맞았나'에 초점을 두고있으며 '순서'는 전혀 신경쓰지 않는듯 보인다. 따라서  다음과같은 문제가 발생한다.

![]({{ 'assets/img/images/problem_of_precision.png' | relative_url }})

model2가 출력한 입력은 모든 단어가 맞았지만 어순이 맞지않아 문장이라 부르기 민망하다. 차라리 model1이 출력한 문장이 조금더 그럴싸하게 느껴질 정도이다.

이를 보완하기위해 BLEU score가 등장하였다.

BLEU score는 n개의 단어가 순서대로 등장하는데 이것을 몇개나 맞추었는가에 초점을 둔다.

$BLEU = min(1, \frac{length_of_prediction}{length_of_reference})($ $$\prod_{i=1}^4 precision_{i}$$ $)^\frac{1}{4}$


 $min(1, \frac{length_of_prediction}{length_of_reference})$ 는 Brevity panalty term으로 정답 문장보다 길이가 짧으면 패널티를 부여하며 길이가 같거나 길면 1로 패널티를 부여하지 않는다
 
 정답 문장보다 길이가 길다면 어느정도 recall이 높아질것이라는 가정에서 나온 term이다.
 
 뒤의 텀은 n-gram을 1-gram부터 4-gram까지 수행한 후 값들의 기하평균을 낸 값이다. 연속한 단어가 많이 나올수록 점수는 높게 올라간다.
 
 ![]({{ 'assets/img/images/BLEU_score.png' | relative_url }})
 
 다시 이전의 그림에서 model1과 model2가 BLEU score에서 어떠한 값을 얻는지 확인해보자. 
 
 1-gram에선 model1보다 model2의 성능이 높지만 이후 수행되는 n-gram에선 model2로 만들어진 문장은 점수를 얻지 못했다.
 
 따라서 BLE score는 model1이 52점을 얻게되고 model2는 점수를 얻지 못하는 결과가 나온다.
