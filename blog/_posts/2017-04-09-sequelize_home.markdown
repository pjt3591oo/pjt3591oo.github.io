---
layout: post
title:  "sequelize - Home[번역]"
date:   2017-04-09 16:00:05 +0900
categories: node.js
tags: [programming, javascript, sequelize, orm, node.js]
published: true
writer: 박정태
count: 125
---

> 시작 하기 앞서 해당 글은 sequelize document를 직접 번역함을 알립니다. 영어를 많이 잘하지 못하여 오역이 있을 수 있습니다.


Sequelize는 node.js와 io.js를 위한 promise기반의 ORM입니다. Sequelize는 PostgeSQL, MySQL, MariaDB, SQLite ,MSSQL의 디비를 지원하며 트랜젝션, 관계, 백업을 지원합니다.

## 예시 - 사용법

```.js
var Sequelize = require('sequelize');
var sequelize = new Sequelize('database', 'username', 'password');

var User = sequelize.define('user', {
  username: Sequelize.STRING,
  birthday: Sequelize.DATE
});

sequelize.sync().then(function() {
  return User.create({
    username: 'janedoe',
    birthday: new Date(1980, 6, 20)
  });
}).then(function(jane) {
  console.log(jane.get({
    plain: true
  }));
});
```
