---
layout: post
title:  "mysqldump를 이용하여 데이터 백업, 복원하기"
date:   2017-05-04 14:33:05 +0900
categories: database
tags: [database]
published: true
count: 125
descript: 'mysqldump를 이용하여 디비 백업과 source를 이용하여 데이터 복원을 해보자'
writer: 박정태
---

로컬 서버에 있는 디비를 분리하기 위해 기존의 디비에 있는 데이터를 백업해야 하는 일이 생겼다. 방법은 많지만 mysqldump를 이용하여 해결을 하였다.

mysqldump는 해당 내부의 데이터를 sql 문으로 전부 바꿔주는 역할을 하는 툴이다.

mysqldump의 경우 mysql이 설치가 되어있다면 같이 설치가 되므로 따로 설치를 할 필요는 없다

# dumpfile 생성하는 방법

dumpfile이란 sql이라는 확장자를 가지는 파일을 말한다.


```bash
$ mysqldump -u [user] -p --all-databases > dump.sql # 디비 서버에 있는 모든 데이터 베이스 백업
$ mysqldump -u [user] -p --databases [db name] > dump.sql # 디비 서버에 있는 [db name]에 한에서만 백업
$ mysqldump -u [user] -p [db name].[table name] > dqmp.sql # 특정 디비의 특정 테이블 백업
```

위 에서 --all-databases, --databases와 같은 옵션이 붙어 있는데, --all-databases는 모든 데이터 베이스를 의미한다.

--databases는 특정 데이터 베이스를 선택할 때 사용한다

그외에
--opt : 버퍼링 비활성화
--allow-keywords, --quote-names: 예약어를 사용하는 테이블 덤프, 복원할 수 있게 함
-d, --no-data: 데이터 제외 스키마만 덤프
-t, --no-create-info: 스키마 제외

등이 있다.

# dump파일 복원하는 방법

```bash
mysql > source dump.sql
```

매우 간단하다 ㅋㅋ

단, 주의할점이 있다면 .sql파일이 있는 경로에서 mysql을 접속을 해야한다. 그렇지 않으면 sql파일을 찾을 수 없다.

```bash
mysql> source dump.sql
ERROR:
Failed to open file 'francosy.sql', error: 2
```

mysqldump와 source를 이용하여 손 쉽게 백업 및 복원을 할 수 있다.