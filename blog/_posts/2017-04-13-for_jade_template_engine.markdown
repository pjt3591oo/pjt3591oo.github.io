---
layout: post
title:  "Jade Template Engine을 활용한 반복되는 코드 줄이기"
date:   2017-04-15 10:39:05 +0900
categories: node.js
tags: [programming, node.js, jade]
published: true
writer: pjt3591oo
count: 125
---

node.js에서는 jade와 ejs라는 템플릿 엔진을 이용하여 html파일로 렌더링을 할 수 있습니다.
블로그 주인은 `ejs`보다 `jade`를 애용합니다. 이유는 `{/% /%}`, `<>`와 같은 특수 문자를 조금 덜 사용하는 jade를 선호합니다.
하지만 jade는 `tab`과 `space`로 구문을 해석하는 단점이 있습니다.

이번 시간에는 어떻게 하면 효율적인 html파일 관리를 할 수 있을지 설명을 해보도록 하겠습니다.
(아래 내용은 100% 정답이 아니라 다양한 방식으로 사용해오면서 발전된 방법입니다. 앞으로도 계속 변할 것이고, 추후에 추가되거나 바뀌는 부분에 대해서는 추가적으로 포스팅을 하겠습니다.)

jade의 문법적인 내용을 다루지 않습니다.

express를 활용하여 프로젝트를 생성을 해보도록 하겠습니다.

- project create

```bash
$ express helloWorld  # CREATED helloWorld Directory
$ cd helloWorld       # MOVED helloWorld
```

- project structure

```
+-- app.js
+-- bin
|   +-- www
+-- package.json
+-- public
|   +-- javascript
|   +-- css
|   +-- images
+-- routes
|   +-- user.js
|   +-- indexs.js
+-- views
|   +-- error.jade
|   +-- index.jade
|   +-- layout.jade
```

views를 집중적으로 탐구를 해 볼 것이다.

* error.jade

```jade
extends layout

block content
  h1= message
  h2= error.status
  pre #{error.stack}
```

* index.jade

```jade
extends layout

block content
  h1= title
  p Welcome to #{title}
```

error.jade, index.jade파일에서 layout을 extends하고 있다.
또한 block content가 보입니다.
다른 프로그래밍 언어에서 extends가 대충 어떤 느낌으로 작동하는지 감이 오셨을 겁니다.

아래의 layout.jade코드를 살펴 보도록 하겠습니다.

* layout.jade

```jade
doctype html
html
  head
    title= title
    link(rel='stylesheet', href='/stylesheets/style.css')
  body
    block content

```

기본적인 html구조가 보입니다.
그런데 user.jade와 error.jade에서 보았던 block content가 보이는 군요
이 부분이 바로 해당 소스 코드를 붙여서 만들어 주는 부분입니다.

index.jade파일을 랜더링을 시도를 하게 되면 우선 extent layout을 했기 때문에 layout.jade파일을 읽어 드립니다.

그리고 block content를 index.jade에 있는 block content 이하 부분으로 채우게 됩니다. 최종적으로는 아래와 같은 코드가 만들어 질겁니다.

* `index.jade` 렌더링시 만들어지는 중간 결과물

```jade
doctype html
html
  head
    title= title
    link(rel='stylesheet', href='/stylesheets/style.css')
  body
    h1= title
    p Welcome to #{title}
```

content부분을 그대로 옮기게 됩니다.

여기까지 extends의 사용 방법에 대해서 알아 보았습니다.

여기까지 보고 첫번째로 왜 굳이 이렇게 layout을 나누는 것일지 의문을 가질 수 있습니다.

이유는 반복되는 코드(`dry`한 코드 작성)를 줄이기 위해서 입니다. 이는 매우 중요한 이론입니다.

하나가 수정이 이루어져야 하면 반드시 하나만 수정이 일어나야 합니다.

우리가 보는 웹 사이트들을 잘 살펴보면 상단에 위차한 header, 하단에 위치한 footer와 같은 것들은 고정이 되어있는것을 확인을 할 수 있을겁니다.

만약 html로 페이지를 만들게 되면 모든 페이지의 header, footer가 같은 코드로 반복적으로 작성이 이루어지게 될 것입니다.

하지만 jade와 같은 템플릿 엔진을 활용하면 이러한 반복된 코드 작성을 줄여줍니다.

* layout.jade

```jade
doctype html
html
  head
    title= title
    link(rel='stylesheet', href='/stylesheets/style.css')
  body
    header
        // header 관련 코드

    block content

    footer
        // footer 관련 코드
```

* business_logic1.jade

```
extends layout

block content
    // logic code1
```
* business_logic2.jade

```
extends layout

block content
    // logic code2
```

이러한 형태로 header와 footer를 layout에 넣어 줌으로써 서로 다른 부분의 영역만 집중적으로 관리가 가능해집니다.

하지만 이러한 형태도 아직은 불안정 합니다. header와 footer또한 각각의 기능이 뚜렷하고 기능이 추가되어 무거워 질 수 있습니다. 즉 독립적인 파일로 관리를 하는 것이 추후 유지 보수에 좀더 좋을 것 같습니다.

header와 footer도 layout파일과 분리를 해보도록 하겠습니다.

그전에 views의 내부를 좀 수정을 해보도록 하겠습니다.


* views directory

```
+-- layouts
+-- business
+-- components
```

layouts, business, components로 디렉토리를 재 구성 해줍니다.

- components : 위에서 언급한 header, footer같이 부품처럼 쓰이는 존재
- layouts : components를 활용하여 반복되는 코드를 미리 만들어 둠
- business : 실제 로직이 들어가는 부분

디렉토리를 재 구성했으니 각각의 필요한 파일을 재 구성을 해보도록 하겠습니다.

```
+-- layouts
|   +-- business_logic1_layout.jade
|   +-- business_logic2_layout.jade
+-- business
|   +-- business_logic1
|       +-- index.jade
|       +-- error.jade
|   +-- business_logic2
|       +-- index.jade
|       +-- error.jade
+-- components
|   +-- header.jade
|   +-- footer.jade
```

우선 위에서 언급한 header, footer를 compoenets에 추가를 해주었습니다.

include를 활용하여 layout에서 해당 파일을 불러와 사용을 해보도록 하겠습니다.

* ./components/header.jade

```
header
    // header 관련 코드
```
* ./components/footer.jade

```
footer
    // footer 관련 코드
```

layout에서 작성했던 header와 footer부분의 코드를 그대로 빼줍니다.

그리고 layouts/business_logic1_layout에서 해당 파일을 include를 해주면 됩니다.

* ./layouts/business_logic1_layout.jade

```jade
doctype html
html
  head
    title= title
    link(rel='stylesheet', href='/stylesheets/style.css')
  body
    include '../components/header'

    block content

    include '../components/footer'
```

**business/business_logic1/error.jade**와 **business/business_logic1/index.jade**에서 layout 호출 부분을 수정을 해보도록 하겠습니다.
(디렉토리 생성을 하였기 때문에 경로를 바꾸어 주어야 합니다.)

* `error.jade`

```jade
extends ../../layouts/business_logic1_layout

block content
  h1= message
  h2= error.status
  pre #{error.stack}
```

* `index.jade`

```jade
extends ../../layouts/business_logic1_layout

block content
  h1= title
  p Welcome to #{title}
```

components, layouts, business의 형태로 좀더 효율적인 구조를 만들었습니다.

각 비지니스 로직이 다른 레이아웃을 사용 할 수 있기 때문에 레이아웃 사용여부에 따라 business내부를 디렉토리를 나누었습니다.
물론 이 부분은 해당 프로젝트에 맞추어 더 세분화 시켜도 되고 굳이 세분화 할 필요는 없습니다.
간단한 대규모가 아닌 이상 레이아웃 하나 만으로도 충분할거거든요 ㅎㅎㅎ

만약 관리자 페이지가 있다면 관리자에 대한 레이아웃을 추가시켜주면 됩니다.
해당 로직 부분을 business에 추가를 시켜주어 만들어 주면 됩니다.