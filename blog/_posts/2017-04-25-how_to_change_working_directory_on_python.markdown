---
layout: post
title:  "python working directory를 바꿔보자"
date:   2017-04-25 18:19:05 +0900
categories: programming
tags: [programming, python]
published: true
count: 125
descript: 'working directory설정을 하여 경로 문제를 해결하자'
writer: 박정태
---

이번에는 파이썬에서 가끔 삽질을 유도하는 path문제에 대해서 알아보고자 한다.

모듈을 나눠서 개발을 하다보면 서로의 개발환경을 맞추지 않고 작업을 할 수 밖에 없는 경우가 있다.
툴, 파이썬 버전, 등 개인적인 이유에서 셋팅을 맞추지 않고 개발을 시작을 하는 경우가 있습니다.

또한 갑자기 두개의 프로젝트가 갑자기 합쳐져야 하는 경우도 있습니다.
(필자는 대부분 이러한 경우때 문제가 발생하였습니다.)

이럴떄 가장 많은 문제가 되는것이 바로 path문제입니다.

path도 여러가지 문제가 있을 수 있는데 file을 접근할 떄 생가는 문제에 대해서 다뤄보고자 합니다.

```python
import os
os.chdir(os.getcwd()+'/WORKING DIRECTORY')
```

외부 모듈을 가져다가 쓰는 파일에서 해당 코드만 추가시켜주면 간단히 path문제를 해결 할 수 있습니다.

해당 코드는 working directory를 설정하여 절대경로를 고정시켜주는 역할을 합니다. 즉 해당 모듈을 어디에서 호출을 하든 절대경로를 해당 경로로 고정이 됩니다.