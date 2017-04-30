---
layout: post
title:  "파일 리더기 만들기 - pdf를 html로 변환, docx를 pdf로 변환"
date:   2017-05-01 18:19:05 +0900
categories: node.js
tags: [programming, node.js]
published: true
count: 125
descript: 'pdf파일 html로 변경하기'
writer: 박정태
---

파일 리더기를 만들고 있다.

우선 rtf와 docx 직접적으로 처리를 하려고 했으나 매우 거지 같아서 pdf로 변환을 거친 후 처리를 하기로 했다.

이번에는 pdf 파일을 html 파일로 바꿔주는 방법, docx파일을 pdf파일로 바꿔주는 방법에 대해서 다뤄보고자 합니다.

우선 pdftohtmljs라는 모듈을 사용할 것인데.
해당 모듈을 사용하기 전에 pdf2htmlex라는 모듈이 설치가 되어야 한다.

```bash
$ sudo apt-get install pdf2htmlex
```

ubuntu의 경우 위와같이 설치를 해줍니다.

```bash
$ brew install pdf2htmlex
```

mac의 경우는 brew를 이용하여 설치를 해줍니다.

간혹 맥에서 위처럼 설치중에 아래와 같은 문구가 나타나면서 설치가 진행이 되지 않을 수있습니다.

> Error: You must `brew link python` before pdf2htmlex can be installed

이럴경우는 아래와 같이 해결이 가능합니다.

```bash
$ brew link --overwrite python
```

위 명령어를 통해 python을 링크를 걸어줍니다. 이게 해당 모듈을 설치를 할 때 python을 사용해서 무엇인가를 하기 위해 link를 걸어주는 것 같습니다. 이제 다시 pdf2htmlex를 설치 해줍니다.


```bash
$ brew install pdf2htmlex
```

이제 pdf를 html로 변환해주는 pdftohtmljs를 설치를 해보겠습니다.

```bash
$ npm install pdftohtmljs
```

```javascript
var pdftohtml = require('pdftohtmljs');
var converter = new pdftohtml('test.pdf', "sample.html");

converter.convert('ipad').then(function() {
    console.log("Success");
}).catch(function(err) {
   console.error("Conversion error: " + err);
});
```

pdftohtmljs를 불러온 후 위 처럼 작성을 하면 test.pdf를 sample.html로 컨버팅이 가능해집니다.

이제 docx파일을 pdf로 바꿔보도록 하겠습니다. docx-pdf라는 모듈을 사용을 해줄 겁니다.


```bash

$ npm install docx-pdf
```

모듈을 설치를 해줍니다.

```javascript
docxConverter('test.docx', './output.pdf',function(err,result){
    if(err){
        console.log(err);
    }
    console.log('result'+result);
});
```

test.docx를 output.pdf로 바꾸어 줍니다.