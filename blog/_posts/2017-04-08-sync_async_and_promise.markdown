---
layout: post
title:  "Javascript Asynchronous, synchronous and Promise"
date:   2017-04-08 10:08:05 +0900
categories: programming
tags: [programming, javascript, synchronous, Asynchronous]
published: true
writer: 박정태
count: 125
---


# 동기, 비동기 그리고 promise


Asynchronous(비동기) Code란 무엇일까? node를 접하지 않았다면 대부분의 개발자들은 동기방식의 코드에 익숙할 것이다. 다음 코드를 보면 이해하기 쉬울 것이다.

```c
void FTest(){
    for(int i = 0 ; i < 10 ; i++){
        printf("%d", i);
    }
    return ;
}

void main(){
    FTest();
    printf("success");
    return 0;
}
```

위 코드를 실행한다면,
FTest()가 실행이 되면서 0부터 9까지 출력이 될것이다. 해당 함수가 종료가 된 후 success가 출력이 될것이다.
프로그램을 실행시키는 프로세스는 해당 함수가 종료가 될 때까지 점유를 하고있다. 즉 다른 처리를 하지 못하고 끝날때까지 기다리는 것을 의미한다.(만약 해당 함수가 끝나지 않았는데 처리를 하고 싶다면 쓰레드를 만들면 된다.)

java spring기반의 서버를 예를 들어보자
spring은 클라이언트로부터 request가 발생하면 즉시 쓰레드를 만들어낸다. 이후 요청에 대한 처리가 완료된 후 해당 쓰레드를 없앤다. spring같은 경우는 약 1000개의 쓰레드를 생성하여 처리를 할 수 있다고 한다. 물론 서버 PC의 사양에 따라 넘을수도 아닐수도 있을것이다.

만약 쓰레드가 꽉 찼다면?
쓰레드가 꽉 차면 클라이언트는 더이상 요청을 할 수가 없다.

비동기 방식을 사용하면 이러한 문제를 해결할 수 있다.
비동기 방식은 위 코드와 반대로 코드가 끝날 때까지 기다리지 않는다.
즉 요청을 언제든지 받을 수 있다는 것을 의미한다.


node.js내부에 event loop라는 놈이 존재한다. event loop는 외부에서 요청이 들어와 비동기 함수를 호출 할 경우 해당 처리 부분을 그냥 다른놈에게 연산을 하라고 넘겨주기만 한다.
연산이 완료가 되면 callback이라는 특수 함수를 인자로 받는다. callback 함수는 연산이 끝나면 실행을 하라는 무명 함수이다.
아래의 예시 코드를 보자.

```js
MongoClient.connect(url, function(err, db) {

    console.log("Connected succesfully to server");

    db.collection('inserts').insert({_id: 123, a:2}, function(err, r) {
        console.log('insert success');
        db.close();
    });

    console.log('test point');
});
```

위코드는 db에 data를 insert시킨후 insert success를 출력을 하게된다.

동기방식으로 생각을 하면
Connected succesfully to server
insert success
test point
순으로 출력을 해야하지만

test point가 먼저출력이 된다.
(비동기 방식에서는 콜백 인자를 실행을 시키는 개념이기 때문에 모든 함수의 실행 시간을 정확히 측정하기 어렵다.)

그렇다 event loop는 코드를 전부 떠넘긴다. 그리고 다음 요청을 받을 준비를 한다.

이 설명만 읽어본다면 비동기 방식은 처리를 떠넘기고 사용자의 요청을 받을 준비를 하기 때문에 싱글 쓰레드로도 충분히 서버를 돌릴 수 있는 매우 훌륭한 방법이다.
하지만 좋은 방법임에도 불구하고 코드가 매우 더러워지는 콜백지옥(**callback hell**이라도고 한다 )을 경험을 하게 된다. 또한 callback을 루프로 돌리는 건 쉬운일이 아니다.
> 필자도 node를 처음 접하고 콜백 루프를 제대로 돌리기 까지 상당한 시간이 걸렸던 것으로 기억한다. 반년 가까이 편법으로 억지로 끼워 맞추었고 promise, async의 존재를 알고나서 부터 해당 패턴 및 라이브러리를 사용하기 시작했다

```js
MongoClient.connect(url, function(err, db) {

    console.log("Connected succesfully to server");

    db.collection('inserts').insert({_id: 123, a:2}, function(err, r) {
        console.log('insert success');
        db.collection('inserts').insert({_id: 123, a:2}, function(err, r) {
            console.log('insert success');
            db.collection('inserts').insert({_id: 123, a:2}, function(err, r) {
                console.log('insert success');
                db.collection('inserts').insert({_id: 123, a:2}, function(err, r) {
                    console.log('insert success');
                    db.close();
                });
            });
        });
    });

    console.log('test point');
});
```

이런 현상이 될 것이다.(코드만 봐도 한숨이 나오지 아니한가!!!)
이러한 callback hell을 탈출하는 방법 중 하나가 `async`모듈, `promise`패턴을 사용하는 것인데,
async 모듈보다는 promise 사용법에 더욱 익숙해져 있기 때문에 promise에 대해서 설명 하겠다.

Promise patternn
---

```js
MongoClient.connect(url, function(err, db) {

    console.log("Connected succesfully to server");

    pr(db).then(function(r){
        console.log('test point');
    });
});

var pr = function(db){
    return new Promise(function(resolve, reject){
        console.log('test');
        db.collection('inserts').insert({_id: 123, a:2}, function(err, r) {
            console.log('insert success');
            db.close();
            resolve('success');
        });
    })
}
```

먼가 코드가 복잡해 보인다.
Promise 객체를 생성하여 코드를 분리 하였기 때문에 코드가 좀더 길어질 수 있다.

```bash
----- result -----
$ node some.js
Connected successfully to server
test point
insert success
```

pr이라는 놈은 Promise라는 객체를 생성하여 반환을 해준다. Promise의 콜백 함수는 두개의 인자를 받는다 첫번째 인자인 resolve는 해당 객체가 성공적으로 실행 됬을 경우 반활 될 값. reject는 해당 함수가 중간에 에러가 났을 경우 error로 넘겨줄 값.

위 코드에서 pr() 좀더 다듬어 보자.

```js
var pr = function(db){
    return new Promise(function(resolve, reject){
        console.log('test');
        db.collection('inserts').insert({_id: 123, a:2}, function(err, r) {
            if(err) reject(err);
            console.log('insert success');
            db.close();
            resolve('success');
        });
    })
}
```

`if(err) reject(err)`를 추가함으로써 에러 부분을 처리를 해 줄 수 있다.

pr()을 호출한 후 then()이 붙어있다. 요넘은 이전의 함수의 return값을 callback으로 받는다고 생각하면 된다. 그렇기 때문에 만약 function(r)에서 r을 출력을 한다면 `resolve('success')`의 success를 출력을 할 것이다.

***then은 두번째 인자로 이전의 호출에서 error 발생 및 reject됬을 때 호출 되는 콜백을 받기도 한다.***

```js
MongoClient.connect(url, function(err, db) {
    console.log("Connected succesfully to server");

    pr(db).then(function(r){
        console.log('test point');
        return 123;
    },function(err){
        //pr(db)가 error이 났을 경우 호출
        console.log(err);
    }).then(function(data){
        console.log(data);
        return 2;

    },function(err){
        //return 123;이 찍힌 부분이 error 났을 경우 호출
        console.log(err);
    }).then(function(r){
        console.log(r);
        return 0;
    })

});
```

```bash
----- result -----
$ node some.js
Connected successfully to server
test point
insert success
123
2
```

순처적으로 처리가 됨을 볼 수 있다. 또한 각각의 시점에서의 에러 처리를 할 수 있다.

promise.all을 이용하연 여러개의 처리를 한 번에 할 수 있다.
단 하나라도 에러가 날 결우 error 부분을 호출을 한다.
```js
MongoClient.connect(url, function(err, db) {
    console.log("Connected succesfully to server");

    Promise.all([pr(db), pr(db)]).then(function(r){
        //모두 성공했을 경우
        console.log('test point');
        console.log(r);
        return 123;
    },function(err){
        //하나라도 실패했을 경우
        console.log(err);
    });

});
```

```bash
$ node some.js
Connected successfully to server
test
test
insert success
insert success
test point
[ 'success', 'success' ]
```

new Promise가 두번이 실행되고 resolve의 값이 배열로 넘어갔음을 볼 수 있다.

추가적으로 mongodb가 에서 many라는 것이 추가되면서 loop문이 많이 해소 될 것으로 보인다.
(mongodb로 바꿔서 짜본 결과 코드라인수도 많이 줄어들고 가독성도 많이 향상이 되었다.)

```js
db.collection('inserts').insertMany([{a:2}, {b:3}], function(err, r) {
        console.log('insert success');
        db.close();
});
```

```js
db.collection('inserts').insertMany(dbTest, function(err, r) {
        console.log('insert success');
        db.close();
});
```

insertMany를 사용하면 객체 하나가 아닌 array형태로 넘기면 각각을 루프를 돌면서 insert를 해주는것 같다.


javascript 특성만을 이용하여 가독성 향상
---
마지막으로 promise나 async를 사용하지 않고 순수 javascript의 특성을 이용하여 가독성을 향상시켜 보겠다. javascript는 함수를 `1급객체(first class object)` 취급을 한다.

아래와 같은 조건을 가진 것을 `1급객체(first class object)`라 한다.
1. 변수(variable)에 담을 수 있다.
2. 인자(parameter)로 전달할 수 있다.
3. 반환값(return value)으로 전달할 수 있다.

추가적으로 아래와 같은 조건을 가진 것을 `1급함수(first class function)`라 한다.
1. 런타임(runtime) 생성이 가능하다.
2. 익명(anonymous)으로 생성이 가능하다.

위조건을 따지면 c언어에서의 함수는 1급함수로 취급할 수 없다. 반면에 javascript에서는 함수를 1급함수 또는 1급객체로 취급을 한다. 통상 1급객체라 부른다.


다시 본론으로 돌아와 위와 같은 특징을 이용하여 가독성을 높여주는 코드를 작성을 해보겠다.
```js
var fs = require('fs');

fs.readFile('db1.txt','utf-8', function(err, data1){
    fs.readFile('db2.txt','utf-8', function(err, data2){
        console.log(data1, data2);
    });
});
```
요런 코드가 있다. 위 코드를 async나 promise를 이용하여 바꿀경우 외부에 promise 객체를 생성하여 해당 객체를 불러와 then으로 체인연결을 하여 짤 것이다.

```js
var fs = require('fs');


var content_check2 = function(err, data){
    console.log(data);
}

var content_check1 = function(err, data){
    console.log(data);
    fs.readFile('db1.txt','utf-8', content_check2);
}

fs.readFile('db2.txt','utf-8', content_check1);
```
이렇게 코드를 바꿔주면 아무런 모듈을 쓰지 않고도 충분히 가독성을 높여주는 코드를 작성할 수 있다.

```js
var fs = require('fs');
var content = {
	check:function() {
		fs.readFile('db2.txt','utf-8', this.check1);
	},
	_check1:function(err, data) {
		console.log(data);
		fs.readFile('db1.txt','utf-8', this._check2);
	},
	_check2:function(err, data) {
		console.log(data);
	}
};
content.check();
module.exports = {
    content : content
}
```
위와 같이 모듈로 만들어서 쓰기도 간단하다.
