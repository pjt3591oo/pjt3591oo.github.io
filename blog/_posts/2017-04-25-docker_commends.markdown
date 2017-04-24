---
layout: post
title:  "docker commends"
date:   2017-04-25 08:19:05 +0900
categories: server
tags: [server, docker]
published: true
count: 125
descript: '도커의 명령어들을 간단하게 알아보기'
writer: 박정태
---

docker는 가상머신처럼 기존의 운영체제에서 다른 운영체제를 돌릴 수 있도록 해주는 기술입니다

이번 포스팅에서는 도커의 명령어를 간단하게 정리를 해보도록 하겠습니다.

- 이미지 확인

```bash
$ docker images
```

- 이미지 파일 내려받기

```bash
$ docker pull [image]
```

- 이미지 파일검색

```bash
$ docker search [image]
```

- 컨테이너 생성 후 실행

```bash
$ docker run [image]
```

run명령어는 이미지 파일을 컨테이너를 생성해주어 명령어를 실행을 해주는 명령어 입니다.

run명령어를 실행시 해당 이미지 파일이 없다면 pull을 진행을 하게 됩니다 그리고 컨테이너를 만들어 주고 해당 컨테이너를 실행을 시킵니다.

컨테이너는 `$ docker ps -a`로 확인이 가능 합니다.

ex) 일반 os를 생성하여 bash실행
```bash
$ docker run -it ubuntu /bin/bash
```

ex) db같은 서버를 실행 할 경우

```bash
$ docker run -d --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=[password] [이미지 이름]
```

좀더 다양한 옵션을 확인할 수 있습니다.

1. e 옵션: 환경변수 설정.
2. p 옵션: 포트포워딩 설정.
3. d 옵션: 데몬모드라고 불리우며, 백그라운드로 실행을 시킵니다. 디비간은 서버를 실행 시킬떈 -d옵션을 주어 백그라운드로 실행을 시켜주어야 해당 세션에서 나와도 정상적으로 작동을 합니다.
4. i, t옵션 : it로 쓰며 bin/bash로 실행을 할 땐 적어주어야 합니다.
5. v 옵션: 호스트와 공유할 디렉토리 설정

---

- 컨테이너 확인

```bash
$ docker ps -a
```

- 실행중인 컨테이너 확인

```bash
$ docker ps
```

---

- 컨테이너 실행

```bash
$ docker start [CONTAINER ID]
```

- 컨테이너 중지

```bash
$ docker stop [CONTAINER ID]
```

- 컨테이너 재시작

```bash
$ docker restart [CONTAINER ID]
```

- 실행중인 컨테이너 들어가기

```bash
$ docker attach [CONTAINER ID}
```

> CONTAINER ID대신 CONTAINER NAME을 사용해도 됩니다.

---

- 실행중인 컨테이너 명령어 사용

```bash
$ docker exec [CONTAINER ID] commend
```

ex) docker exec -it [CONTAINER ID] /bin/bash

---

- 기존 컨테이너를 새로운 이미지로 생성

```bash
$ docker commit [기존 컨테이너 ID] [새로운 이름]
```

> 이미지로 만들어야 docker hub를 통해 공유가 가능