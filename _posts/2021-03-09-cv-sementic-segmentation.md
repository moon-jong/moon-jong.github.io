---
title: CV Sementic segmentation
use_math: true
tags:
- ComputerVision
---

# Intro
Sementic segmentation은 주어진 이미지의 픽셀별로 어떤 클래스를 나타내는지 분류하는 문제다.
말이 어렵다 그림으로 보자

![](https://www.jeremyjordan.me/content/images/2018/05/Screen-Shot-2018-05-17-at-7.42.16-PM.png)

*출처 :https://www.jeremyjordan.me/semantic-segmentation*

위의 그림처럼 어떤 픽셀이 사람, 자전거, 배경을 나타내는지 분류하는 문제로 볼 수 있다.

때문에 단순 클래스 분류보다 까다로운 경향이 있다.


# FCN
이미지 클래스 분류는 출력을 하나의 logit값으로 뱉어낸다. 
예를들어 256 * 256 이미지를 입력으로 넣으면 개(1)인지 고양이(0)인지 한가지 스칼라 값 혹은 원핫벡터를 출력한다.

하지만 Sementic segmentation은 입력도 이미지, 출력도 이미지 사이즈로 뱉어내야하기때문에 뭔가 다른 과정이 추가될것같은 생각이 든다.
또 픽셀별로 잘 분류하기 위해서 어떤 과정이 추가되었을까?

## 1  by 1 Convolution
![]({{ 'assets/img/images/class_vs_segmentation.png' | relative_url }})

클래스 분류문제와 극명한 차이점은 언급한 바와 같이 출력이 단일 스칼라 혹은 원핫 벡터인지, 이미지형태의 출력인지 다른것이다.
위의 그림은 앞서 한 이야기를 잘 성명해주는데 클래스분류는 가장 큰 로짓을 출력, Segmentation은 이미지형태의 출력을 보여준다.

클래스분류의 마지막 레이어는 Fully connected로 이미지를 하나의 벡터로 펼친 후 출력을 단일 스칼라로 만드는 과정이다.
Segmentation역시 학습 가능한 파라미터들이 존재하며 이를 공간정보(이미지형태)를 갖고있는 출력이 되어야 한다.

FCN은 1 by 1 convolution으로 해결하였다.

![]({{ 'assets/img/images/fully_connected_image.jpeg' | relative_url }})

Fully connected Layer를 공간정보를 유지하며 하나의 벡터를 만든다고 생각해보면 한개의 채널에서 한 행을 벡터로 만드는것이 아닌,
동일한 위치의 다른 채널의 정보를 벡터로 만들어 하나의 픽셀로 학습시킬 수 있다.

다르게 이야기하면 일렬로 펴진 픽셀 채널과 Fully connected Layer의 weight들과 곱해져 하나의 픽셀을 만들어낸다.

하지만 Fully connected Layer를 추가하면 각 input사이즈가 정해져야하고 크기가 다른 이미지들은 대응할 수 없다.

![]({{ 'assets/img/images/1by1conv_mfilter.png' | relative_url }})

이는 1 by 1 convolution과 일치하는데 1 by 1 conv역시 각 픽셀별로 모든 채널을 1 by 1커널과 곱해지기 때문이다.
때문에 Fully connected Layer형식을 유지하면서 크기가 다른 이미지가 들어오더라도 학습이 가능하다.


## Upsampling
하지만 convolution, pooling, stride와 같은 옵션으로인해 feature map의 사이즈는 필연적으로 줄어든다.
따라서 입력사이즈만큼 feature map역시 커져야한다.

처음 제안된 방법이  Transposed convolution이다.

![](https://miro.medium.com/max/1400/1*kOThnLR8Fge_AJcHrkR3dg.gif)

위의 그림과같이 하나의 픽셀에서 더 큰 사이즈의 feature map을 만들고 이를 반복한다.
하지만 보이다시피 연산이 중첩되어 한 픽셀에 n번의 연산이 중첩된다.  (진한 녹색을 띄는 부분은 연산이 중첩된 부분이다)

![]({{ 'assets/img/images/checker_box.png' | relative_url }})

연산이 중첩되게되면 위의 그림과 같이 특정 부분이 도드라진 checker board형태를 띄게되고 이는 학습에 영향을 미친다.

따라서 upsampling시 interpolation, nn-resize convolution을 이용하여 feature map을 만든 후 확장된 feature map에 convolution을 적용하여 학습한다.
하지만 한번 압축한 데이터를 다시 펼치는 transpose를 한다 하더라도 압축되어 사라진 데이터를 복원하는것은 불가능하다.

## skip connection
이로써 우리는 압축된 feature map과 이를 다시 재확장하는 방법까지 알아보았다.
하지만 앞서 언급한바와 같이 압축된 feature map을 upsampling한다고 하더라도 모든 정보를 담고있지않기때문에 하나만을 이용하여 예측하는것은 정확도가 떨어진다.

![]({{ 'assets/img/images/finc_coarse.png' | relative_url }})

또한 compact하게 압축된 feature map은 이미지 전반의 맥락정보를 담고있고 pooling을 비교적 덜한 feature map은 윤곽선, 국지적 정보를 담고있다
따라서 이 둘을 모두 이용하여 segmentation을 진행하게된다.

![]({{ 'assets/img/images/image_skipconnection.png' | relative_url }})

위의 그림처럼 output을 산출할 때 가장 많이 압축된 feature map 뿐만아니라 pooling을 비교적 덜 거친 input image의 디테일 정보를 잘 담고있는 feature map을 
몇개 더 이용하여 동일 해상도의 upsampling을 한다.

![]({{ 'assets/img/images/FCN_skipconnection_result.png' | relative_url }})

결과는 더 많은 feature map을 참조한 예측이 Ground truth와 더 비슷한 예측을 한 것을 볼 수 있다.
