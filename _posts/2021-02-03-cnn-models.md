---
title: CNN models
ues_math: true
tags:
- AI
---

# Intro
2012년부터 ILSVRC에서 딥러닝을 이용한 학습방법으로 이미지를 분류한 모델이 매년 우승하였고 그 후로 딥러닝이 왕좌를 빼앗긴적은 단한번도 없다.
그만큼 딥러닝이 이미지 분류에서 뛰어난 성능을 보이고있고 현재까지도 발전중이다.

이번 포스트에선 각 딥러닝 모델들이 어떠한 방법을 취하여 모델의 성능을 향상시킬 수 있었고 시사점은 무엇인지 적어보겠다.

# Classification

## Alexnet
![]({{ 'assets/img/images/Alexnet.png' | relative_url }})
Alexnet은 2012년 이미지 분류대회에서 딥러닝의 시대를 알린 모델이다.

8개의 층으로 되어있으며 첫 커널은 11 by 11 크기로 되어있는것을 볼 수 있다.

Alexnet은 학습을 위해 다양한 전략을 사용하였는데 내용은 다음과 같다.

- ReLU optimizer 사용
- GPU 사용
- Data Augmentation
- Dropout

사실 전략이라고 하기에는 지금 우리가 이미지 분류를 위해 사용하는 여러가지 기법이다. 하지만 당시Alexnet이 이러한 기법들을 사용하여 좋은 퍼포먼스를 이끌어내었고 지금의 학습방법의 토대를 제공한 모델이기도 하다.

## VGG net
![]({{ 'assets/img/images/vgg_net.png' | relative_url }})

VGG net에서 가장 주목해야할 점은 커널의 크기이다. Alexnet에서 11 by 11 크기의 커널을 사용한것을 볼 수 있다. 하지만 convolution kernel이 커지면 커질수록 학습시킬 파라미터 역시 늘어난다. 

따라서 VGGnet은 오직 3 by3 kernel만을 이용하였다. 하지만 3 by3 kernel을 사용하면 크기가 큰 kernel보다 표현력이 좋은가, 그렇다면 근거는 무엇일까에 대한 이야기를 할 수 있다.

결론은 크기가 작은 kernel을 이용하며, 레이어를 깊게 쌓는다면 학습시켜야 할 파라미터를 줄임과 동시에 크기가 큰 kernel로 학습시킨 것과 동일한 결과를 얻을 수 있다.
아래의 그림을 보자

![]({{ 'assets/img/images/conv_retrieve.png' | relative_url }})

왼쪽은 3 by3 kernel을 이용하여 두겹으로 쌓았고 오른쪽은 5 by5 kernel을 이용하여 같은 128개의 channel을 만들었다.
하지만 왼쪽과 오른쪽의 레이어에서 한 커널이 커버한 영역은 같다. [그림필요]
반면에 작은 kernel을 이용한 모델이 파라미터의 갯수를 더 줄일 수 있었다. 때문에 파라미터가 기하급수적으로 늘어나는 현상을 방지할 수 있고 이로인해 overfitting을 줄일 수 있고 연산자원을 아낄 수 있는 장점이 있다.

VGGnet의 핵심은 작은 커널을 이용하여 학습시켜야 할 파라미터의 갯수를 줄여 파라미터가 기하급수적으로 증가할 시 생기는 문제점을 해결하였다.
~~하지만 VGGnet의 파라미터는 Alexnet보다 많다...~~

## GoogLeNet

Lenet의 핵심은 Inception block이다.
~~network in network 역시 있지만~~

![]({{ 'assets/img/images/inceptionblock.svg' | relative_url }})

정확히 말하자면 Inception block의 1 by 1convolution이다.

VGGnet의 3 by 3 kernel과 마찬가지로 파라미터를 획기적으로 줄여 연산자원을 절약하고 overfitting을 줄였다.
아래의 그림을 보면서 이야기하자.

![]({{ 'assets/img/images/1by1.png' | relative_url }})

왼쪽과 오른쪽 모델 모두 3 by 3 kernel로 128 채널의 tensor를 만들고자한다. 왼쪽은 원래 tensor에 곧바로 3 by 3 kernel을 적용한것이고 오른쪽은 1 by 1convolution을 통해 채널을 미리 줄인 후 3 by 3 kernel을 적용하여 128개로 늘렸다. 하지만 파라미터의 수는 14만개 vs 4만개로 1 by 1convolution을 적용하여 파라미터의 수를 30% 수준으로 줄일 수 있었다.

## 파라미터와 모델의 깊이

| Model | AlexNet | VGGnet | GoogLeNet |
| -------- | -------- | -------- |  -------- |
| number of parameter     | 60M     | 110M     | 4M     |
| model depth     | 8  layers     | 19 layers    | 22 layers     |

모델들의 변화를 살펴보면 학습 파라미터의 수는 점점 줄어들고 그 깊이는 점점 깊어진다.
이는 파라미터를 줄이기위해 1 by 1convolution을 적용하고 그로인해 깊이가 추가적으로 깊어지기 때문이다. 

여기까지의 모델 퍼포먼스의 목적은 파라미터의 갯수를 줄이는것이 퍼포먼스를 높이고 좋은 모델을 만드는 기준이었다.
하지만 이러한 방법으로 Layer를 아무리 깊게 만들어봤자 더 좋은 모델을 만드는것이 불가능하였다.

이러한 문제를 어느정도 해결한 Resnet이 등장하였다.

## Resnet

![]({{ 'assets/img/images/cant_reduce_error.png' | relative_url }})

레이어를 아무리 깊게 쌓아도 더이상 모델의 성능이 나아지지 않았다. 56개의 레이어를 가진 모델보다 20개의 레이어를 가진 모델이 성능이 더 좋았다.
레이어가 깊어질수록 gradient vanishing현상이 발생하였고 결국 깊이가 중요한가? 라는 질문을 던졌다.

따라서 조금 다른 접근방식이 필요했다.

Resnet은 skipconnection이라는 방법을 이용하였다.

![]({{ 'assets/img/images/skipconnection.png' | relative_url }})

단순히 weight을 곱하고 activation을 취한 뒤 다른 노드에 보내는 것이 아닌, 그런 결과에 원래 input x를 더해줌으로써 원래 input과의 차이를 학습시킨것이 key idea이다.
한가지 중요한건 $F(x) + x$를 미분하더라도 최소 gradient를 1로 보장하여 gradient vanishing을 방지하였다는 것이다.


## DenseNet
Resnet이 더했다면 Densenet의 아이디어는 적층(concat)하는것이다.
하지만 이 전의 채널들을 모두 쌓는다면 파라미터가 기하급수적으로 증가할 우려가 있기때문에 1 by 1convolution을 통해 채널의 갯수를 중간중간 줄여가며 쌓아간다.

# Outro
정말 얕게 몇가지 모델들을 살펴보았다.
모델들을 보면서 가장 크게 느낀점은 시간이 지나고 새로운 모델들이 나오더라도 그 모델의 구조가 전체적으로 바뀌는것이 아닌 이 전 모델이 가지고있던 이점들을 그대로 취하면서 하나의 아이디어를 추가하여 성능을 끌어올린다는것이다. 

앞으로 분류모델을 설계할 때 여러 논문들을 읽고 어떤 이점이 있고 내가 어떻게 나의 모델에 적용해야하는지 혹은 적용시킬 수 있는지 살펴보며 설계해야겠다는 생각이 든다.
