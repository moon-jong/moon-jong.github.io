---
title: Boostcamp Day3 Data structure and Pythonic
tags:
- python
---

# intro
python의 기본적인 데이터구조(?)와 pythonic한 코드를 작성하는 요령에 대하여 학습하는 날이었다.
다양한 자료구조를 배웟지만 오늘은 자료구조파트의 deque와 set, 
pythonic code에선 list comprehension,generator에 대하여 이야기하겠다.

# Data Structure
## deque
- double-ended queue의 약어로 스택과 큐의 기능을 동시에 갖고있는 자료구조이다. 따라서 아래와 같은 동작이 가능하다
<script src="https://gist.github.com/moon-jong/e69733a422717dc221010102ae947deb.js"></script>
list 자료구조로 que를 구현하기위해  ```list_data.pop(0)```와 같은 방식으로 작성을 많이 해보았다.
다만 ```pop()```에 들어가는 인자가 단순히 0이라 표현되어 직관적이지 않을뿐만아니라 list자료구조 특성상 인덱스 정렬을 다시 해야되므로 시간복잡도 역시 높아진다.

deque를 이용하여 위와같이 간단하게 스택과 큐를 간단하게 구현할 수 있다는 장점이 있다.

- 양방향 연결리스트로 구현되어 삭제, 삽입시 시간복잡도가 낮다
양방향 연결리스트는 내 이전 노드와 다음 노드 의 주소 그리고 현재 데이터로 연결되어있는 자료구조를 의미한다.  따라서 삭제시 단순히 내 이전노드와 다음노드를 연결하고 삽입시 이전노드와 나, 나와 다음노드를 연결하는 간단한 연산으로 이루어진다. ~~다만 간혹 구글링을 해보면 여러 블로그에 삽입과  삭제 연산이 O(1)로 마무리될 수 있다는 이야기가 있는데 n개의 노드에서 특정 노드를 처음부터 타고 올라간다면 O(n)이 될 수 있다 생각하여 더 알아봐야겠다~~

## set
중복된 원소를 허용하지 않는 특이한 자료구조이다. 
이름처럼 집합과같이 동작하며 교집합, 합집합, 차집합 등의 연산이 가능하다

<script src="https://gist.github.com/moon-jong/999a40fe524e678ad7fb152d24ef0003.js"></script>


# Pythonic code
pythonic code는 다른 언어와 달리 파이썬만의 작성방식, 방법을 이야기한다
대표적으로 list comprehension, 가변인자 등이 있지만 현재 다른 언어들에서 가져온 특징, 다른 언어에 영향을 준 특징 등이 마구 뒤섞여 그 의미가 조금 퇴색되었다고 한다.

우리는 왜 pythonic한 코드를 작성해야할까? 
세가지 대표적인 이유가 있다.

1. 다른 사람이 작성한 코드에 대한 이해도를 높일 수 있다.
수업시간에 교수님이 말씀하셨듯이 Python만의 작성방식은 영어쓰는 나라에서 영어를 사용하듯이 만연하다.
때문에 영어권에서 영어를 자연스럽게 사용하는게 당연하듯이 ~~영어권에서 슬랭을 자연스럽게 사용하는게 조금 더 와닿는다~~ Python을 이용하여 코드를 작성하는 개발자들에게는 Pythonic한 방법으로 코드를 작성하는 것이 당연하기 때문에 나홀로 외딴섬 외톨이가 될 수 있다. ~~필자도 처음 list comprehension봤을때 띠용했다~~

2. 아주 미세하지만 조금 빠르다.
for loop을 돌려서 list를 만드는것과 comprehension을 사용하는것은 아주 미세하지만 차이가 있다고한다. 
또한 연산과정에서 (조금)빠른 속도를 자랑하는것 외에 코드 몇줄을 줄일 수 있으므로 개발속도에도 차이가 있을거라 생각한다. 
~~다만 필자는 숏코딩보단 변수하나 더 쓰더라도 가독성 높은 코드가 더 좋은 코드라 생각한다~~

3. 간지
프로그래머스나 백준 정답코드를 찾아보면 남들 20줄씩 작성한 코드를 list comprehension한줄로 간지나게 푼 사람들을 볼 수있다. 간지난다.

## list comprehension
각설하고 list comprehension에 대하여 정리하겠다.

1. 기본 작성법
[] 내부에 for문을 넣고 변수 i가 내부 규칙에 따라서 list의 원소가 된다.

단순 if조건만을 넣을 때에는 **for문 뒤에 if condition을**  
else를 넣어 삼항식을 만들 때에는 **for문 앞에 if else condition을** !!!!!

~~elif는 지원하지 않는것으로 보인다.~~
 <script src="https://gist.github.com/moon-jong/c9ab06eeea32b13922eb0ee6e7bed380.js"></script>
 
 2.nested loop & 2D list
 이중(또는 다중) for문을 이용한 리스트 역시 작성할 수 있다.
 
 하지만 nested loop과 2D list는 아주 미묘하게 다른데
 
 먼저 nested loop을 살펴보면 i를 변수로 한 for문이 바깥, j를 변수로한 for문이 안쪽에 위치한다.
 
 반면에 2D list를 생성할때는 ㅓ를 변수로 한 for문이 바깥, i를 변수로한 for문이 안쪽에 위치한다.
 <script src="https://gist.github.com/moon-jong/7bca23e8311cf6aaac8b8320dbb4b3c4.js"></script>


## generator
CNN으로 이미지를 학습시킬 때 tensorflow의 ImageDatagenerator를 얼핏 사용한 기억이 있다.
이미지, 동영상, 음성의 경우 데이터셋의 크기가 굉장히 크기때문에 모든 데이터셋을 메모리에 한번에 올린 뒤 처리하는것은 공간낭비가 될 수 있다.

따라서 이미지가 저장되어있는 폴더의 이미지 파일명을 알려주고 batch마다 특정 이미지들만 불러와 (batch_size만큼의 이미지) 학습하는 방식이다.

이를 가능하게 해주는 것이 generator인데 추상적인 개념으로 요리사가 필요한 레시피만을 요리속에 담고있고 주문이 오면 그때그때 요리를 내놓듯이
generator역시 특정 레시피를 갖고있고 호출될 때 필요한 값을 전달하는 것을 이야기한다.

간단한 예시를 들어보면
<script src="https://gist.github.com/moon-jong/a6482fa45daac6008a164a6ba01680e2.js"></script>

위의 코드는 ```ex_generator```라는 generator를 선언한 뒤 ```test```객체에 할당한다.
그 뒤 한줄마다 ```next```로 다음 값을 호출한다.

단순히 for문을 이용하여 list를 미리 만든 뒤 원소 하나하나를 호출하는 것은 모든 list값이 메모리에 올라가있기 때문에 메모리 관리 차원에서 낭비이다.
마치 모든 요리를 미리 만들어놓고 주방에서 주문이 들어오기를 기다리는 꼴이다.

음식의 가지수가 많지 않다면 다행이지만 그 수가 많아진다면 주방은 온갖 음식들로 가득 차게되어 발디딜 틈이 없을것이다.
이와 마찬가지로 generator는 로직을 기억하고 호출이 될 때마다 처리된 값을 메모리에 올리게되어 메모리를 절약할 수 있다.

따라서 대용량 데이터를 처리하는 작업에 많이 이용된다.
