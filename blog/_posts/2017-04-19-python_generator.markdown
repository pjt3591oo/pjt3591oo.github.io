---
layout: post
title:  "제너레이터(generator)를 활용한 효율적인 코드작성"
date:   2017-04-19 22:58:05 +0900
categories: programming
tags: [programming python]
published: true
count: 125
descript: '효율적인 코드 작성을 위한 generator 사용방법 입니다.'
writer: 박정태
---

오늘은 generator에 대해서 알아볼까 합니다

우선 generator는 python말고도 `javascript es6`에서도 새롭게 추가된 부분이기도 합니다.

우선 python docs의 generator에 대한 정의를 봅시다.

```
generator

A function which returns an ierator.
It looks like a normal function except that it comtains yield statements for
producting a series of values usable in a for-loop that can be retrieved
one at a time with the next() function. Each yield temporarily suspends processing,
remembering the location execution state (including local variables and pending
try-statements). When the generator resumes, it picks-up where it left-off
(in contrast to functions which start fresh on every invocation).
```

첫 줄에 한 줄로 정의가 됐다.
반복자를 반환하는 함수
모든 호출에서 새롭게 시작되는 함수가 아닌 위치를 기억하는 함수

즉 특정 위치를 기억하여 루프를 돌릴 수 있도록 반환을 하는 것이다.
해당 함수는 **next()**를 이용하여 호출이 가능.

```python
# app.py
def generator(n):
    i = 0
    while i < n:
        yield i
        i += 1

_generator = generator(10)

print(_generator)

for i in _generator:
    print(i)
```

실행결과

```bash
$ python3 app.py
<generator object generator at 0x10213d8e0>
1
2
3
4
5
6
7
8
9
```

얼핏 보면 일반 리스트 루프를 돌리는 것과 별반 다를건 없어 보인다.

하지만 함수 generator()를 보면 내부에 **yeild**라는 것을 볼 수 있다. 또한 해당 함수는 generator형 object를 반환을 하게된다.
해당 함수가 호출되어 yeild를 만나게 되면 해당 함수의 yeild지점에서 return과 같이 값을 반환을 하게 되는데 이때 완변한 함수 종료가 아닌 해당 지점의 위치를 기억하여 다음에 호출되는 시점을 저장을 하게 된다.

즉 값을 반환을 하면서 다시 그 지점으로 호출을 할 수있도록 되어있는 구조이다.
(신기하지 않은가?? 이 yeild에 대한 이해를 완벽히 하길 바란다. 필자는 yeild를 무시하고 generator라는 놈을 완벽히 이해하기 까지 상당한 오랜 시간이 걸렸던것 같다. 그렇게 때문에 왜 써야 하는지가 가장 큰 의문이었다. 이유는 아래에 설명을 계속 해보도록 하겠다.)


여기까지 함수에 yeild를 이용하여 generator를 만드는 방법에 대해서 알아보았다.

하지만 python에서는 generator를 좀더 쉽게 사용하는 방법을 제공을 하고 있다.
[], {}는 무엇을 의미하는가??
[]는  list(), {}는 dictionary를 의미한다.

generator도 위와같이 쉽게 쓰는 표현법을 제공을 한다 바로 ()이다.


```python
# app.py
_generator = (1, 2, 3, 4, 5, 6)
```

그러나 위와같이 작성을 하면 안된다.

사실 python에는 **tuple**이라는 타입이 있다.

()을 사용하기 위해서는 **list comprehension**에서 []대신 ()을 사용을 하는 것이다.

```python
# app.py
_list = [i for i in range(10)]
_generator = (i for i in range(10))

print(_list)
print(_generator)
```

실행결과

```python
$ python3 app.py
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
<generator object <genexpr> at 0x10213d8e0>
```

상적으로 generator object를 반환을 하게 된다.
generator는 일반 iterator와 마찬가지로 for - loop를 똑같이 작동을 시킬 수 있다.


여기까지가 generator의 모든 것이다.
그렇다면 의문이 들 것이다.

첫번째 : 왜쓸까? iterator를 돌릴 수 있다면 list를 안쓰고 굳이 generator를 쓰는 이유가 무엇일까?
generator를 쓰는 가장 큰 이유는 **memory의 사용성** 입니다.

```python
# app.py
import sys

maxed = 10

_list = [i for i in range(maxed)]
_generator = (i for i in range(maxed))

print(sys.getsizeof(_list))
print(sys.getsizeof(_generator))
```

sys의 getsizeof를 이용하면 해당 변수의 메모리 사용크기를 확인을 할 수있습니다.

위 결과를 확인 해보면
list인 _list는 192
generator인 _generator는 88만큼의 메모리 공간을 사용할 수 있음을 확인 할 수 있습니다.

만약 maxed값이 커지면 메모리의 크기는 어떻게 될까?
list인 경우는 커진만큼 더 커지고 generator의 경우는 88로 고정이 된다.
generator는 해당 시점의 주소값만 저장을 하고 다른 데이터는 더 저장을 할 필요가 없기때문에 크기가 늘어날 필요가 없다


generator를 사용을 하면 코드를 원하는 시점에서 실행이 가능해지는 장점이 있습니다.

상당히 많은 python 라이브러리들이 generator형태로 값을 만들어 내는데 바로 이 때문이다.