---
title: Pytorch basic
use_math: true
tags:
- pytorch
---

# Intro
처음 딥러닝 프레임워크를 접한건 Tensorflow였다. 
당시 TF 2.0이 출시되기 전이라 이상한 세션을 열고 .... 닫고 .... placeholder를 어쩌구....
굉장히 난해했던 기억이있다.

TF 2.0이 출시된 후 (keras API 역시 지원한 뒤) 굉장히 직관적으로 바뀌었다.

프레임워크가 중요하진 않다지만 점점 Pytorch로 넘어오는 추세이며 부스트캠프 역시 pytorch를 딥러닝 프레임워크로 채택하였다.
조금 벗어난 이야기지만 후에 조직에 들어가 일을 진행한다면 둘다 알고있는것 역시 메리트가 될것이라 생각한다.

# Pytorch

## tensor
tensor flow, pytorch 모두 tensor기반의 연산이며 numpy array를 지원한다.

때문에 연산도 numpy 연산을 지원한다.

### 기본연산
<script src="https://gist.github.com/moon-jong/880edddde7962c0429b6514a375a614a.js"></script>
numpy와 굉장히 유사하다.

reshape같은 경우는 numpy가 더 직관적인 이름을 갖고있다고 느꼈지만 
squeeze 등 편리한 기능들이 많이 있는것같다.


### functional module

<script src="https://gist.github.com/moon-jong/6f07d4e558b3dd03544b742ba9c1e3f6.js"></script>

pytorch의 functional 메소드는 activation function, one-hot encoding등 다양한 함수적 기능들이 들어있는 메소드이다.



## auto_grad
다음의 식을 미분한다고 하자

$y = w^2$

$z = 2*y + 5$

$z = 2*w^2 + 5$

<script src="https://gist.github.com/moon-jong/e4cb21d5b00d6b81a20840e7ec6777cd.js"></script>

pytorch는 backward라는 메소드를 이용하여 chain rule을 적용하고 w의  gradient가 w.grad에 저장된 것을 볼 수 있다.
