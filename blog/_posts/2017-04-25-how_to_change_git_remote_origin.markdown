---
layout: post
title:  "git 원격 저장소 바꾸기"
date:   2017-04-25 22:09:05 +0900
categories: programming
tags: [programming, python]
published: true
count: 125
descript: 'remote set-url을 이용하여 원격 저장소를 바꾸자'
writer: 박정태
---

- remote url 확인하기

```bash
$ git remote -v
```

로컬 브랜치에서 push, fetch를 할 경우 어느 원격 저장소로부터 작업을 할지에 대한 정보를 보여준다.

- remote url 변경

```bash
$ git remote set-url origin [new-url]
```

매우 간단.