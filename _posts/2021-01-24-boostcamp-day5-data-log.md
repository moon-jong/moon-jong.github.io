---
title: Boostcamp Day5 Data & Log
---

# Intro
실습위주의 수업이 있는 날이었다. 
개발과정과 개발 후 배포과정에서 발생할 수 있는 예외처리하는 방법에 대해 학습하였고 
로그남기기, html, xml, JSON 파싱 등에 대하여 학습하였다.

이론적으로 중요한 날이라기보다 실습 위주의 학습을 하는 날이므로 오늘은 학습 내용에 대하여는 argparse 라이브러리에 대하여 이야기 하겠다

# argparse

![]({{ 'assets/img/images/arg_parser.png' | relative_url }})
*python help*

![]({{ 'assets/img/images/docker_help.png' | relative_url }})
*docker help*


python, docker등을 셸에서 실행 후 -h 커맨드를 입력하면 위와같은 help 리스트가 나오는 것을 확인할 수 있다.
처음 python을 배울때 셸 커맨드는 어떻게, 누가 지정할 수 있고 어디에 쓰이는지 궁금했던지라 그 궁금증이 조금은 해소되었다.

아래의 코드는 게임 실행 시 캐릭터 세팅이라 가정하고 만들어보았다. ~~실제로 그렇게 쓰이는지 장담은 못하겠지만~~

<script src="https://gist.github.com/moon-jong/766ab231e90a59432cad784319f02681.js"></script>

먼저 parser 객체에 argparser를 지정해준뒤 description을 적어준다.(필수 X)

그 뒤 add_argument method로 argument를 지정한다. (파라미터는 짧은이름, 긴이름, 타입지정, attribute 명, default값, help 설명 등등 수많은 옵션이 있다)

parser에담긴 argument들을 parse_args()로 해석한다. (네임드 튜플로 저장)
이후 파싱된(해석된) argument들은 'dest'에 지정한 attribute 이름으로 접근이 가능하다.

위의 코드를 파이썬 셸에서 실행시키면 다음과 같은 결과가 나타난다.

![]({{ 'assets/img/images/ex_help.png' | relative_url }})
*help command 입력시*
*help command는 자동으로 지정되어있기때문에 -h, -help 옵션을 바꾸게 되면 에러가 발생한다.*

![]({{ 'assets/img/images/argparse_ex_2.png' | relative_url }})
*argument 입력시*

# Outro
1주차 Python basic이 끝나고 이제 2주차에 들어간다. 
AI를 위한 수학, numpy, pandas, 시각화 툴 등을 다룬다.

짧은시간 안에 많은 툴, 이론 등을 다루기때문에 정리, 복습하기도 벅찬감이있다. 
하지만 이렇게 무언가에 집중할 수 있다는 사실에 감사하며....
