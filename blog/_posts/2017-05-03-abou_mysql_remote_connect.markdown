---
layout: post
title:  "mysql 원격접속하는 방법"
date:   2017-05-03 20:43:05 +0900
categories: database
tags: [database]
published: true
count: 125
descript: 'mysql 디비설정, 유저설정을 통해 원격접속'
writer: 박정태
---

database도 하나의 서버이기 때문에 외부에서 접속을 해야하는 경우가 있다.

하지만 2가지의 이유로 접속이 불가능할 수 있다.

1번째. db의 접속 ip를 제한했을 경우 이는 디비 자체에서 특정 ip대역만 접속을 허용하도록 설정하는 경우이다.

2번째. 경우 유저의 접속 ip를 제한하는 경우이다.

필자는 오늘 개발된 서버의 정보를 받아다가 개발을 이어하던 상황을 맞이했다. 디비와 서버가 같은 로컬환경에서 이루어 졌고. 그렇기 때문에 디비의 접속 ip가 localhost로 제한이 걸려있었다. 또한 계정의 접속 ip도 localhsot로 제한이 걸려있어서 개인 로컬 환경에서 정상적인 디비접속이 이루어지지 않았다.

디비 접속 ip가 제한당하면 디비 커넥자체가 되지 않지만 유저접속 아이피가 제한을 당해도 정상적인 디비접속은 가능하다 다만 디비 목록을 제대로 다 읽어오지 못하는 현상이 발생한다.

(필자는 이 부분을 처음 경험했기 때문에 유저 제한이 걸려있는지 적당한 삽질끝에 찾아냈다. phpmyadmin에서는 타 계정이 정상접속이 됬는데 디비 클라이언트에서 접속을 할땐 정상접속이 되지 않았고 이를 통해 유저가 디비 접속을 하는 위치에 따라 디비 목록을 받아오지 못하는 현상이 있다는것을 알게되었다.)

추가 적으로 ip접속 제한이 걸려있다면


```bash
$ mysql -h ip주소 -u 계정 -p
```

여기서 ip주소는 localhost나 127.0.0.1이 아닌 실제 서버 주소를 쳐야한다.

실 디비서버 에서 위와같은 방식으로 접속을 해도 로컬 개발환경에서 뜨는 현상과 똑같이 떴음을 확인을 했고 종합결과 유저에 대한 제한이라고 판단을 했다
.(머 사실 이경우밖에 없어서 판단이고 머고 없음... ㅋㅋ 단지 확실할뿐.... ㅋㅋㅋㅋ)

# 디비 접속제한 설정하는 방법

```bash
root@ip-172-31-2-167:/etc/mysql# netstat -ntl
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
tcp6       0      0 :::3306                 :::*                    LISTEN
tcp6       0      0 :::80                   :::*                    LISTEN
tcp6       0      0 :::22                   :::*                    LISTEN
```

netstat라는 명령어를 통해 대기중인 포트 확인과 아이피 허용 범위를 알 수 있다. ::::또는 0.0.0.0같은 경우는 모든 ip대역을 허용하겠다는 의미이다.

```bash
root@ip-172-31-2-167:/etc/mysql# netstat -ntl
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
tcp6       0      0 127.0.0.1:3306          :::*                    LISTEN
tcp6       0      0 :::80                   :::*                    LISTEN
tcp6       0      0 :::22                   :::*                    LISTEN
```

만약 위처럼 127.0.0.1과 같이 로컬 호스트 주소만 허용되어 있다면 아래의 파일을 수정을 해준다.

```bash
vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

해당 경로를 가서 mysqld.cnf를 열어보면 bind-address가 보일것이다.

```bash
bind-address=127.0.0.1
```

위 처럼 되어있다면 #을 붙여서 주석처리를 해준다.
주석 처리를 해주고 바뀐 설정을 적용시키기 위해 서버를 다시시작 해준다.

```bash
$ service mysql restart
```

이렇게 해도 외부에서 접속이 되지 않을경우가 있다. 이럴때는 접속하는 계정의 권한문제일수 있다.

# 유저 접속제한 설정하는 방법

mysql에서는 mysql.user에서 유저에 대한 정보를 저장하고 있다.

```sql
mysql> USE mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> SELECT host, user FROM user;
+-----------+------------------+
| host      | user             |
+-----------+------------------+
| localhost | root             |
+-----------+------------------+
9 rows in set (0.00 sec)
```

디비 로컬환경에서 root으로 로그인을 한 후 mysql디비 접속후 user테이블 조회를 해보았더니 위와같은 결과가 나타났다.

host는 허용 접속 ip이다. root계정은 로컬 호스트에서만 접속이 가능하다는 의미이다.

위 처럼 user테이블을 검색하면 모든 유저의 대한 정보를 볼 수 있고 현재 접속한 계정에 대한 정보를 보고싶다면 show grants for current_user;를 이용하면 된다.

```sql
mysql> SHOW GRANTS FOR CURRENT_USER;
+---------------------------------------------------------------------+
| Grants for root@localhost                                           |
+---------------------------------------------------------------------+
| GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' WITH GRANT OPTION |
| GRANT PROXY ON ''@'' TO 'root'@'localhost' WITH GRANT OPTION        |
+---------------------------------------------------------------------+
2 rows in set (0.00 sec)
```

현재 root으로 로그인이 된 상태이기 때문에 root에 대한 정보가 나타난다.

current_user;에 특정 계정명을 치면 해당 유저의 좀더 정확한 정보들이 나타난다.

만약 root@ip를 쳤는데 아래와 같이 나타난다면 해당 유저는 해당 ip에서 접속이 불가능하다.

```sql
mysql> SHOW GRANTS FOR root@1.1.1.1;
ERROR 1141 (42000): There is no such grant defined for user 'root' on host '1.1.1.1'
```

```sql
mysql> INSERT INTO mysql.user (host,user,password) VALUES ('%','계정',password('패스워드'));
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%';
mysql> FLUSH PRIVILEGES;
```

INSERT INTO의 경우 계정을 추가하고 싶을 때 사용하면 되고,

만약 이미 존재하는 계정에서 접속 ip만 추가하고 싶다면 2~3 줄만 실행을 하면된다.
