---
layout: post
title:  "ssh-keygen을 이용한 ssh 키 파일 생성"
date:   2017-04-21 13:14:05 +0900
categories: tools
tags: [tools ssh]
published: true
count: 125
descript: '여러가지 인증 방법 중 ssh를 이용하기 위한 키 파일 생성방법'
writer: 박정태
---

우리는 타 서버에 접속을 하기 위해서 다양한 방법을 사용하는데 그중 하나가 **ssh** 키를 이용하는 방법이다.

ssh 키를 이용하면 당연히 ssh 키 파일이 필요하다. 이번 글에서는 이러한 키 파일을 생성하는 방법에 대해서 알아보고자 한다.

ssh 키 파일을 만들기 위해서는 `ssh-keygen`을 이용하면 매우 쉽게 키 파일을 만들어 낼 수 있다.

linux나 mac은 기본적을 설치가 되어있지만, window의 경우에는 git bash가 설치가 되어있다면 git bash를 통해서 실행이 가능하다.

git을 설치하여 **git bash**를 이용하여 사용하기를 권장한다.
(git bash를 이용하면 윈도에서도 linux와 같은 명령어를 통해서 명령어를 사용할 수 있다.)

ssh를 접속하기 위해서는 키 파일이 필요하다 우리는 2개의 키 파일을 만들어야 하는데 서버에 두는 키와 접속자가 들고 있는 키가 필요하다

```bash
$ ssh-keygen -t rsa -b 2048
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/bagjeongtae/.ssh/id_rsa): 키 파일 이름
Enter passphrase (empty for no passphrase): 생성 되는 키의 암호
Enter same passphrase again: 생성 되는 키의 암호 확인
Your identification has been saved in /Users/bagjeongtae/.ssh/id_rsa.
Your public key has been saved in /Users/bagjeongtae/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:ouLIFlSvyAam6DUcPU19mU0Ej88np8709JRYwoBMf24 bagjeongtae@bagjeongtaeui-MacBook-Pro.local
The key`s randomart image is:
+---[RSA 2048]----+
|        .. .Bo   |
|   .   .o.o+o.   |
|  . o o  o.+ o   |
|.o . + .    B    |
|B o o o S    E + |
|o= = . .    . O .|
|o + o        + o.|
|.= .        + o..|
|o..          o ..|
+----[SHA256]-----+
```

위 명령어를 통해 키 파일을 생성이 되었다.

```bash
$ ls -al ~/.ssh
total 24
drwx------   5 bagjeongtae  staff   170  4 21 12:59 .
drwxr-xr-x+ 59 bagjeongtae  staff  2006  4 21 11:19 ..
-rw-------   1 bagjeongtae  staff  1679  4 21 12:59 id_rsa
-rw-r--r--   1 bagjeongtae  staff   425  4 21 12:59 id_rsa.pub
-rw-r--r--   1 bagjeongtae  staff   866  4 21 11:35 known_hosts
```

키를 생성할 때 아무것도 입력을 하지 않아 id_rsa라는 파일이 생성이 되었다.

*.pub는 접속 대상이 되는 서버에 넣어둔다.

접속할 때는 id_rsa 파일을 이용하여 접속을 하면 된다.