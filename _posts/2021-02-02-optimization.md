---
title: Optimization 어떻게 학습시켜야할까?
use_math: true
tags:
- AI
---

# Intro
딥러닝을 이용하여 학습을 시키다보면 고질적인 이슈가 하나 발생한다.
**어설프게 학습하면 학습이 되지 않는다**

야구데이터(테이블 데이터)에 멋모르고 CNN을 적용시켜보았던 경험이있다.
영상, 이미지 역시 따지고보면 테이블 데이터로 볼 수 있고 CNN이 이에 적합한 알고리즘이라 생각하여 무작정 적용해보았다.

결과는 단순 회귀모델모다 덜한 퍼포먼스를 갖는 모델이 출력되었다.
그야말로 겉만 CNN인 예쁜 쓰레기 모델이 탄생하였다.

![]({{ 'assets/img/images/pretty_trash.jpg' | relative_url }})



<center><em>예쁜 쓰레기의 대명사.... </em></center>

따라서 딥러닝을 이용한 학습은 파라미터의 설정과 모델에 맞는 알고리즘 및 방법론 선택 등 여러가지 고려해야 할 요소들이 따른다.

이를 전반적으로 Optimization 기법이라 부르는데 단순히 딥러닝 학습시 optimizer를 선택하는 것에서 그치는 것이 아닌, 좋은 퍼포먼스를 자랑하는 모델을 설계하기 위해 고려해야 할 사항들에 대한 이야기를 한다.


# 모델 최적화
## Bias, Variance

일상에서 '저사람은 bias되어있어'라고 말하는 경우가 있다. 올바른 표현은 아니겠지만 대체로 편향된 시각을 가진 사람을 일컫는다.
이는 모델에도 마찬가지로 적용된다. 모델이 학습한 뒤 출력한 값이 타겟으로부터 일정정도 편향된 경향을 보이는 경우 우리는 모델이 bias되어있다 라고 이야기한다.

Bias가 타겟으로부터 얼마나 떨어져있는지 이야기하는 값이라면 Variance는 탄착군이 형성되었는지 알려주는 지표이다. 
영점사격시 가정은 총에대한 영점이 맞춰져있지 않다는 것이다. 따라서 사람 모형에서 많이 벗어나더라도 탄 자국들이 모여있는 탄착군을 형성하였다면 괜찮은 영점사격을 하였다고 말할 수 있다.

그림을 보면 훨씬 쉽게 이해가 간다.

![]({{ 'assets/img/images/bias_var.png' | relative_url }})

중요한것은 모델을 학습시킬 때 이 둘이 trade-off 관계라는 것이다.
때문에 Bias가 낮아지면 Variance가 올라가며 이 반대의 경우도 마찬가지이다.
## Bootstrapping
모든 데이터를 전부 사용하여 한가지 강력한 모델을 만드는것도 모델 설계의 방법이지만
학습데이터를 여러갈래로 샘플링하여 수개의 모델을 만들어보고 결과를 결합하거나, 취약점을 상호 보완하는 방법도 있다.
ML의 Random forest, XGBoost 등 이러한 방법들에 착안하여 고안된 모델들이다. 어떤 방법을 사용하였을까?
### Bagging
모델을 만들 때 모든 데이터를 전부 이용하는 한가지 모델이 아닌 한가지 데이터를 여러번 subsampling하여 여러가지 모델들을 만드는 방법이 Bagging이다.  ~~앙상블이라고도 한다~~
하나의 모델이 데이터셋 전체를 사용하는것이 아닌, 작은 데이터 샘플마다 여러가지 모델을 만들어 그 결과를 모두 취합하여 투표를 하거나 평균을 취하는 방식이다.

여러가지 모델이 서로 학습하여 제각기 결과를 낼 수 있으므로 데이터의 overfitting을 완화시킬 수 있고 많은 대회에서 좋은 성과를 낸 방법이기도 하다.

### Boosting
하나의 샘플 데이터를 이용하여 모델을 구현한 뒤 오분류된 데이터셋을 이용하여 이 후의 모델이 분류하지 못하였던 레이블들을 잡아나가는 방법이다.
목적은 하나의 weak learner에서 점차적으로 strong learner를 만드는것이 목적이다.

<p><a href="https://commons.wikimedia.org/wiki/File:Ensemble_Boosting.svg#/media/File:Ensemble_Boosting.svg"><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/b/b5/Ensemble_Boosting.svg/1200px-Ensemble_Boosting.svg.png" alt="Ensemble Boosting.svg"></a>
	
	By <a href="//commons.wikimedia.org/wiki/User:Sirakorn" title="User:Sirakorn">Sirakorn</a><span class="int-own-work" lang="en">Own work</span> <a href="https://creativecommons.org/licenses/by-sa/4.0" title="Creative Commons Attribution-Share Alike 4.0">CC BY-SA 4.0</a>, <a href="https://commons.wikimedia.org/w/index.php?curid=85888769">Link</a></p>
	
	
	
## Batch-size

>"We ... present numerical evidence that supports the view that large batch methods tend to converge to **sharp minimizers** of the training and testing functions. In contrast, small-batch methods consistently converge to **flat minimizers**... this is due to the inherent noise in the gradient estimation." 
>- On Large-batch Training for Deep Learning: Generalization Gap and Sharp Minima, 2017


한 에폭당 몇개의 데이터를 이용하여 학습시킬것인가?
메모리와 시간이 충분하다면 마냥 full-batch를 이용하는것이 좋을까?

위의 논문에선 아니라고 이야기한다. batch-size를 늘릴수록 폭이 좁고 깊은 로컬 minimum에 도달하며 줄인다면 비교적 flat한 minumum에 도달한다고 이야기한다.
![]({{ 'assets/img/images/sharp_and_flat.png' | relative_url }})
그림을 보면 붉은색이 testing function, 검은선이 training function이다. 두 점이 학습 데이터셋에서 잘 훈련이되어 minimum을 잘 찾아간듯 보인다.

하지만 문제는 test 데이터에서 성능을 테스트할 때 나타난다. 두 점 빨간 점선과 맞닿는 곳을 수직으로 그어본다면 평평한 minimum에선 cost가 별로 다르지 않지만 깊은 협곡에선 cost가 매우 달라지는 것을 확인할 수 있다.

## Regularization
모델이 데이터셋을 너무 '잘' 설명하는것도 문제가 된다.
Overfitting의 문제가 생길 수 있기 때문이다. 아이러니하게 모델 학습시 적절히 '방해'를 해주면 데이터의 일반적인 특징을 조금 더 잘 설명하는 모델이 될 수 있다.

### Early stopping
![]({{ 'assets/img/images/es.png' | relative_url }})

training set으로 학습을 진행한다면 training error는 계속해서 줄어든다. 당연한 이야기다.
training set의 특징을 따라서 파라미터를 업데이트하기 때문이다. 이는 training data를 '너무 잘' 설명하기 때문인데 이로인해 다른 추가적인 데이터가 들어왔을 때 오히려 설명하지 못하는 현상이 발생한다.

따라서 validation error와 training error가 일정 차이를 보이면 학습을 중단시키는 기법이다. 

### Noise Robustness

![]({{ 'assets/img/images/noise.png' | relative_url }})

이유는 분명하지 않지만 데이터와 weight에 일정 노이즈를 추가하여 shake 하면 모델 성능이 올라간다는 연구 결과가 있다....
