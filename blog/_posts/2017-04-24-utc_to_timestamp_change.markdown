---
layout: post
title:  "python utc를 timestamp로 바꾸는 방법"
date:   2017-04-24 22:19:05 +0900
categories: programming
tags: [programming, python, time]
published: true
count: 125
descript: 'python에서 utc를 timestamp로 바꾸는 방법'
writer: 박정태
---

```python
from datetime import datetime
import time


utc = time.time()
time = datetime.datetime.utcfromtimestamp(utc).strftime(‘%Y-%m-%d’)
print(time)
```

```
실행결과
2017-04-24
```