---
title: stage4 DST Day2
tags:
- NLP
use_math: 'True'
---

## Ontology?
DST에서 state를 나타내는 요소는 
Slot과 value이다. 예를들자면 '숙소-가격대' Slot에 대한 value는 ['저렴', '적당', '비싼', 'none', 'don'tcare'] 정도로 나타낼 수 있을것이다.
여기서 모든 슬롯에 대한 value set을 Ontology라 부른다.

따라서 모델이 내보내는 모든 value는 오직 Ontology에서만 등장하게 된다.

## Ontology based DST

![]({{ 'assets/img/images/unseen_ontology.png' | relative_url }})

앞서 언급한바와 같이 Ontology based DST는 모든 value는 Ontology안에 존재한다 가정한다.
따라서 Ontology 외부의, Unseen value는 알아낼 수 없다는 분명한 한계를 갖는다.


## SUMBT
Ontology based DST에는 NBT, GLAD, GCE등 여러 방법론이 있지만 SUMBT가 BERT를 base로 한 방법이며 성능도 준수하다

![]({{ 'assets/img/images/SUMBT.png' | relative_url }})

*출처: https://www.aclweb.org/anthology/P19-1546.pdf

SUMBT는 BERT를 이용하여 턴 t에대한 value와 slot에 대한 임베딩을 진행한다.
여기서 중요한것은 value와 slot을 임베딩하는 BERT는 freeze되어 parameter update를 막아놓아야 한다.
즉, BERT를 말그대로  임베딩을위한 인코더로 사용한다.
이로써 임베딩된 slot $q^s$,  value $y^{s}_t$를 얻는다

그리고 BERT에 사용자와 시스템의 발화를 넣고 이를통해 $U_t = BERT([x^{sys}_t, x^{usr}_t])$를 얻는다 $U_t$는 이후 Multihead attention에서 key와 value로 활용된다

이후 임베딩된 slot $q^s$를 Q로 사용하고 $U_t$를 K, V로 사용하여 Multihead attention을 거쳐 $h_t$를 얻어낸다 

$h_t$는 RNN을 거쳐 해당 턴의 $d^{s}_t$ Layer Norm을 거쳐 $\hat{y}^{s}_t$를 산출하게되고 임베딩된 value $y^{s}_t$와 비교하여 가장 similarity가 높은 value를 뽑아낸다.

*(RNN에서는 해당 턴 이전의 $d^{s}_{t-1}$가 hidden input으로 들어오게되어 전체 문맥을 학습하게된다)*
