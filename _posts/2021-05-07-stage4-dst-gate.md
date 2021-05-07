---
title: stage4 DST Gate 늘리기
---

# TRADE
![]({{ 'assets/img/images/slot_gate.png' | relative_url }})

TRADE 구조에서 value는 slot gate를 통해 value를 none을 내보낼 것인지(값 생성 안함), don't care를 value로 삼을것인지, vocap space나 history에서 찾아 generate할것인지 3진분류를 통해
결정된다.

저자의 의도는 모든 value는 none, don't care, 그외 나머지로 이루어져있기 때문에 gate를 통해 none과 don't care를 먼저 걸러내고 나머지에 대해서만 value를 생성할 목적이라고 파악된다.

그리고 현재 제공받은 데이터는 다른 value들 보다 don't care, none이 압도적으로 많기때문에 don't care와 none만 잘 걸러내더라도 성능이 상승할 수 있을거라 생각했으며

그러기 위해선 3진분류로 나누는 것이 아닌, 2진분류를 2번 실행하는 방식으로 바꿔볼 수 있을거라 생각했다.

한 전문가가 3가지 클래스를 예측하는것이 아닌, 두명의 전문가가 각자의 분야만 몰두하여 분리하는 형태라 비유할 수 있을것같다.

따라서 gating을 진행하는 layer를 none, care, ptr 세개로 구성하였고 각각의 레이어는 none과 not_none  care와 don't care ptr과 not_ptr을 각각 2진분류하는 역할을 수행한다.

따라서 gating_input 역시 3개로 나누어져 라벨([0, 1]로 구성된 벡터)로 들어가게 되고 이를 각각 cross entropy해주는 형식으로 코드를 바꾸었다.

오후 1시부터 모델이 학습을 시작하였고 학습 종료 예상시간은 내일 새벽 4시로 약 13 - 14시간정도 소요된다.

결과가 기대된다.
