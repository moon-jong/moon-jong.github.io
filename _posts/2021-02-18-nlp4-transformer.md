---
title: NLP4 Transformer
use_math: true
tags:
- NLP
- AI
---

# Intro
지금 딥러닝의 헤게모니를 쥐고있는 모델구조라 하면 단연 Transformer일것이다. 범용 인공지능을 넘보는 OpenAI의 GPT-3 역시 Transformer 구조로 이루어져있고 BERT, BART 등 규모와 월등한 성능을 뽐내는 모델들이 차용하고있는 구조이다.

마찬가지로 OpenAI의 최근 발표된[DALL-E](https://openai.com/blog/dall-e/)역시 이 구조를 차용하고있고 문장을 입력하는 그에 맞는 이미지로 바꾸어주는 language to image 모델이다. (신기하다....)
(~~학습시키는데 전기료만 수십억원이 들었다고한다~~)(GPT-3)

수십억원의 전기요금을 감당하면서까지 모델을 만들 정도면 사측에서 어떤 모델구조를 사용할 것인지 어떻게 학습시킬 것인지 여러 검증과정을 거쳐 채택되었을 것이다.
그만큼 핫한 구조임에 틀림이 없으며 현재 NLP쪽을 더불어 여러 도메인으로 퍼져나가고있을 정도로 선풍적인 인기를 보이고있다.

오늘은 Transformer에 대하여 전부 다루기는 힘들겠지만 어떤 방식으로 학습하는지, 왜 좋은지 또 어떠한 한계가 있을지 알아보겠다

# Transformer
논문 제목이 상당히 전투적이다([Attention is all you need](https://arxiv.org/abs/1706.03762)) 

논문 제목에서도 알 수 있듯이 Transformer는 Attention 구조가 핵심이다. 하지만 지난 seq2seq 역시 Attention을 사용한 모델이다. 
어떤 차이점이 있을까?

## RNN을 탈피한 Attention
![](http://jalammar.github.io/images/t/transformer_resideual_layer_norm_3.png)
*이미지 출처: http://jalammar.github.io/illustrated-transformer/*


막바로 Transformer에 들어가기 전 위의 그림부터 보자.
seq2seq과의 차이점은 Transformer는 LSTM, RNN, GRU등의 RNN계열의 구조에 Attention이 에드온처럼 서브모듈로써의 역할을 하는것이 아닌, Self-Attention이라는 구조를 통하여 오로지 Attention만을 이용한 학습 모델이라는 것이다.

그렇다면 어떤방식으로 학습한다는 것일까?

[이전 포스팅 Attention부분](https://moon-jong.github.io/2021/02/17/nlp3-attention-beamsearch-bleu-score.html#attention)을 참고하면 seq2seq은 디코더에 입력으로 들어온 임베딩 벡터와 
인코더에 있는 hidden state 벡터들을 내적하고 이를 softmax취해 가중평균을 통한 context 벡터를 생성했다.

Transformer는 단지 seq2seq의 디코더 벡터와 인코더 벡터를 모두 입력으로부터 도출해낸다. (그래서 self-Attention이라 부르는가보다)
**어떻게?**


## Query, Key, Vlaue 벡터
Transformer는 스스로 모든 입력에 대하여 Query, Key, Vlaue 벡터를 만들어낸다.

![](http://jalammar.github.io/images/t/transformer_self_attention_vectors.png)
*이미지 출처: http://jalammar.github.io/illustrated-transformer/*

입력으로 들어간 임베딩 벡터와 $W_{q, k, v}$행렬을 내적하여 앞서 언급한 세가지 (Query, Key, Vlaue 벡터)를 만들어낸다.
Query 벡터는 seq2seq에서 디코더의 입력과 같은 역할을 한다. 
입력 타임스텝 자신을 포함한 다른 모든 Key 벡터들과 내적 연산을 통해 유사도를 측정한다.

마찬가지로 이를 softmax를 취하면 각 Key 벡터에 대한 가중치가 나올것이다. 
이를 각 value벡터와 가중평균을 통해 타임스텝 히든벡터를 만들어낸다. 

잘 보면 seq2seq의 인코더 부분 hidden state벡터 역할을 value 벡터가 수행하고 이를통해 만들어진 context vector를 히든벡터라 할 수 있다.

self-attention의 대략적인 연산은 여기서 마무리된다. 

![]({{ 'assets/img/images/transformer_oper.png' | relative_url }})
*한 타임스텝에서의 self-attention 연산과정*

가장 복잡한 self-attention은 마무리 되었다. 하지만 한 인코더의 한 블록에서 점선, add & normalize feed forward등 여러 과정이 남아있다.

## after self-attention
attention 과정이 끝난 벡터는 add & norm 블록에서 자기 자신 입력값과 더해지게된다. 
점선으로 보이는 선이 residual connection으로 resNet에서 skip connection과 같은 역할로써 입력값 대비 차이값을 학습시키고 gradient가 사라지는 현상을 방지한다.

또한 이 값들을 Normalize해주는 과정을 거치고 이를 Fully connected layer로 보내준다

![]({{ 'assets/img/images/encoder.png' | relative_url }})
*인코더의 구조*

## Positional Encoding
하지만 자연어와 같은 시퀀스 데이터는 '순서'가 있다. 
'나는 공부를 한다' 라는 문장과 '한다 공부를 나는' 이라는 문장은 엄연히 다른 문장이다.

하지만 Transformer가 학습하는 방식은 한 문장을 시퀀스로 생각하지 않고 모든 토큰과 유사도를 계산하고 가중평균을내어 학습하게된다. 
즉, 순서가 무시될 수 있다는 이야기이다.

이를 보완하기위해 시퀀스마다 positional encoding을 해준다. 
위의 인코더의 구조를 보면 입력 벡터가 Attention에 들어가기 전 positional encoding을 더해주는 것을 볼 수 있다. 이를 통해 Transformer에게 순서를 알려주게 된다.

position encoding의 대표적인 방법은 주기가 다른 사인, 코사인 함수를 여러개(시퀀스의 길이만큼 = 포지션 갯수만큼) 만들어 input 벡터에 더해주는 것이다.

![]({{ 'assets/img/images/position_encoding.png' | relative_url }})


## Decoder 
디코더는 인코더와 크게 다르지 않다. 우선 들어온 라벨을 Positional encoding해준 후 self-attention, add & norm layer를 거치는 것까지는 동일하다.

하지만 self-attention을 한번 더 거치게 되는데 여기서 Key, Value 벡터는 인코더에서 넘겨준 벡터를 사용하며 유사도를 측정할 기준벡터(Query 벡터)를 디코더에서 생성한 것을 사용한다. 
마찬가지로 self-attention, add & norm layer를 거친 뒤 Fully connected layer를 거쳐 vocab에서 가장 확률이 높은 단어를 softmax를 거쳐 출력하는 방식으로 최종 output이 생성된다.


# Outro
아직 빠진 내용이 굉장히 많다. Layer normalization, Leaning Rate Scheduling, Masked Self-Attention등등 Transformer에서 행해지는 여러 연산들의 내용이 빠져있다.

추후 포스팅을 통해 해당 내용을 업데이트하겠다.
