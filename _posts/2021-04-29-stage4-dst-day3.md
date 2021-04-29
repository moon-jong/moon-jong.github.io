---
title: stage4 DST Day3 train dialogue EDA
use_math: true
tags:
- NLP
---

# 세팅병... 글꼴부터...
주어진 환경은Ubuntu이므로 Ubuntu에서 matplotlib 사용시 한글 글꼴 설정부터 해주었다. (~~생각보다 개고생했다~~)
레퍼런스는 (Hooni's Playground: https://hooni-playground.com/961/
												성혁의 개발 블로그:https://seonghyuk.tistory.com/31) 를 참고했다.

먼저 네이버에서 배포한 나눔 글꼴부터 다운받았다

	apt-get install fonts-nanum*
	
그후`fc-cache -fv` 를 하여 캐시를 삭제한다.

*(제공받은 서버의 우분투에는 fontconfig이 없어 `apt install fontconfig`을 먼저 실행해 fontconfig을 설치하였다)*

그 후 다운받은 글꼴을 내가 사용하고 있는 파이썬 환경에 matplotlib/mpl-data/font에 넣고
.cache/matplotlib 폴더에 있는 캐시파일을 삭제한 뒤 노트북 커널을 재실행하였다.

나눔 바른 펜 글꼴을 사용하려 했기때문에 

	import matplotlib as mat
	mat.rcParams['font.family']= 'NanumBarunpen'
	
로 matplotlib 기본 글꼴을 설정해주었다.

적용한 뒤 타이틀을 찍어보면

![]({{ 'assets/img/images/gksrmf.png' | relative_url }})

위와같이 한글이 깨지지않고 출력되는 것을 확인할 수 있다.

# 어떤 Tokenizer를 사용해야할까?
데이터셋을 학습시키기 위하여 문장을 토큰으로 나누고 그것을 임베딩하는 과정을 거친다.
하지만 문장을 토큰화할 때 토크나이저의 Vocab에 들어있지 않은 '모르는'단어는 UNK토큰으로 나온다.

따라서 제공받은 데이터를 어떤 토크나이저를 사용하면 UNK토큰을 줄일 수 있을지 3개의 미리 학습된 토크나이저로 실험해보았다.

![]({{ 'assets/img/images/UNK_token_count.jpeg' | relative_url }})

실험한 토크나이저는 XLM-RoBerta-large, KOBERT, KOELECTRA-discriminator-v3이다.
한국어로 된 데이터임에도 불구하고 KOBERT를 사용하면 60%정도는 UNK토큰으로 나온다....

앞으로 토크나이저를 바꾸는 실험 등은 미리 제공된 텍스트로 실험을 한 뒤 UNK토큰이 KOBERT와같이 나오는 모델은 사용하기 힘들것으로 보인다.
