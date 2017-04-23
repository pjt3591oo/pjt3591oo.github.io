---
layout: post
title:  "ubuntu에 php 개발환경 셋팅"
date:   2017-04-23 18:19:05 +0900
categories: programming
tags: [programming, php, server]
published: true
count: 125
descript: '우분투에 php를 설치해보자'
writer: 박정태
---

시작하기에 앞서서 필자는 docker에 ubuntu 16버전에서 셋팅을 하였습니다.
ubuntu 설치 직후와 같은환경.

# APM설치

ubuntu에 php환경을 셋팅을 해보도록 하곘습니다.

도커에 우분투를 올린것은 우분투를 설치 직후와 같기때문에 패키지, apt-get을 업데이트를 시켜줍니다.

apache, php, mysql을 APM이라고 부른다

```bash
$ apt-get update
$ apt-get upgrade
```

* apache2 설치

```bash
$ apt-get install apache2
```

* mysql 설치

```bash
$ apt-get install mysql-server mysql-client
```

mysql은 설치를 진행중에 관리자 계정의 패스워드를 입력하라는 창이 뜬다.

* php 설치

```bash
$ apt-get install php libapache2-mod-php php-xml php-gd php-mysql
```

* apache, mysql 실행하기

```
$ service apache2 restart
 * Restarting Apache httpd web server apache2
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message
                                                                                                                                            [ OK ]
$ service mysql restart
 * Stopping MySQL database server mysqld                                                                                                    [ OK ]
 * Starting MySQL database server mysqld
No directory, logging in with HOME=/
                                                                                                                                            [ OK ]
```

---
---

# apache2 셋팅

지금까지는 설치하는 방법이었습니다. 이제는 apache2를 설정하는 방법에 대해서 알아보도록 하겠습니다.

* 포트변경

```bash
$ vim /etc/apache2/ports.conf
```

```bash
# If you just change the port or add more ports here, you will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default.conf

Listen 10000

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```

Listen을 원하는 포트로 바꿔주시면 됩니다.

```bash
$ service apache2 restart
```
포트를 바꿔주셨으면 이제 서버를 재시작 해줍니다.

# php파일 추가하기

apache는 `/var/www`가 루트 디렉토리로 동작을 하게된다.

```bash
$ cd /var/www
$ ls
html
```

/var/www로 이동을 해보면 html디레토리가 있을 겁니다.
해당 디렉토리 내부에 php파일을 만들어 주도록 합니다.

```bash
$ vim /etc/www/html/helloworld.php

<html><body>
<?php

echo 'Hello world';

?>
</body>
</html>
```

자 이제 해당 서버로 접속을 해줍니다.

localhost:포트번호/helloworld.php로 접속을 해보겠습니다.

```bash
$ curl http://localhost:10000/phpinfo.php
<html><body>
Hello world</body>
</html>
```

정상적으로 접속이 됩니다.

흠... apache랑 php는 처음 다뤄보는데 먼가 신선하네 ㅋㅋㅋㅋㅋ

---

# phpmyadmin을 이용하여 디비관리

```bash
$ apt-get install phpmyadmin
```

phpmyadmin을 설치를 해줍니다.

```
Deconfigure database for phpmyadmin with dbconfig-common? [yes/no]
```
yes를 입력해줍니다.


```Please provide a password for phpmyadmin to register with the database server. If left blank, a random password will be generated.

MySQL application password for phpmyadmin:

Password confirmation:
```

database의 관리자 계정을 설정해 줍니다.

```bash
Determining localhost credentials from /etc/mysql/debian.cnf: succeeded.
Please choose the web server that should be automatically configured to run phpMyAdmin.

  1. apache2  2. lighttpd

(Enter the items you want to select, separated by spaces.)

Web server to reconfigure automatically:
```

어떤 웹서버를 사용할지 선택을 해줍니다.

apache2를 사용할 것이기 때문에 1번을 입력해주고 엔터를 쳐줍니다

```bash
$ service apache2 restart
```

쾌적한 진행을 위해 apache2를 재시작을 해줍니다

이제 http://localhost:포트/phpmyadmin을 접속을 해줍니다.

그럼 로그인창이 뜨게 되는데

ID : root
PASSWORD : 관리자 비밀번호

여기까지 apache를 이용해서 php를 제공을 해주고 phpmyadmin을 이용하여 디비를 관리할 수 있도록 셋팅을 해보았습니다.

마지막으로 한가지만 더 해결을 해보도록 하겠습니다.

apache를 재시작하기 위해 `service apache2 restart`를 입력할 때마다 이상한 문구가 계속 떴을겁니다.

```bash
$ service apache2 restart
 * Restarting Apache httpd web server apache2

 AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message
                                                                                                                                            [ OK ]
```

AH00558: apache2 ~라는 문구가 보기가 싫습니다.

```bash
$ vim /etc/apache2/apache2.conf
```

/etc/apache2/apache2.conf을 열어서 `Servername localhost`를 추가를 해주고 저장을 해줍니다.

```bash
# service apache2 restart
 * Restarting Apache httpd web server apache2                                                                                               [ OK ]
```

이제야 보기가 좋네요

