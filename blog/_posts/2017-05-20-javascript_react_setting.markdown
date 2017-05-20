---
layout: post
title:  "[react] react 작업환경 설"
date:   2017-05-20 15:29:05 +0900
categories: programming
tags: [programming javascript]
published: true
count: 125
descript: 'react를 시작하기 전에 환경셋팅을 해보자'
writer: 박정태
---

# react-boilerplate 사용방법

해당 프로젝트는 일반적인 react를 사용하기 전에 이런저런 환경설정을 미리 셋팅을 해둔 프로젝트 입니다. 해당 프로젝트는 아래의 설정들을 거쳐 해당 명령어만 실행을 해주면 일반적인 react 프로젝트를 실행 시킬 수 있습니다.

```bash
1. git clone https://github.com/pjt3591oo/react-boilerplate.git - 프로젝트 클론하기
2. npm install – Dependency 설치하기
3. npm start – 개발 서버 실행하기 (포트: 7777)
```

위 명령어를 실행 하였으면 http://localhost:7777로 접속해보기


> 해당 프로젝트의 자세한 설명은 아래에 작성하였습니다.

---

* 시작하기 앞서서

> npm 3.10.6이상 버전 설치 권장 : npm의 버전마다 설치 방식이 달라 2.x버전에서 아래에 나와있는 모듈을 설치 할 때 상당히 오래 걸릴 수 있습니다.

# 1. Global Package 설치하기

* ```babel``` : 아직 ECMAScript6를 지원하지 않는 환경에서 ECMAScript6 Syntax를 사용할 수 있게 해줌.
* ```webpack``` : 모듈 번들러로서, Browserify처럼 브라우저 위에서 import(require)을 할 수 있게 해주고 자바스크립트 파일들을 하나로 합쳐준다.
* ```webpack-dev-server``` : webpack에서 지원하는 간단한 개발서버로서 별도의 서버를 구축하지 않고도 웹서버를 열 수 있으며 hot-=loader를 통하여 코드가 수정될때마다 자동으로 리로드 되게 할 수 있다.


```bash
$ npm install -g babel webpack webpack-dev-server
```

# 2. project생성

```bash
$ mkdir react-tutorial && cd react-tutorial
$ npm init
```

# 3. Dependency 및 Plugin 설치

* react사용을 위한 패키지 설치

```bash
npm install --save react react-dom
```

* babel에서 사용 될 플러그인 설치, 해당 모듈은 개발환경에서만 사용되므로 **```-save-dev```** 옵션을 설정

```bash
$ npm install --save-dev babel-core babel-loader babel-preset-react babel-preset-es2015 webpack webpack-dev-server
```

> `webpack` 과 `webpack-dev-server` 가 글로벌로 이미 설치가 되어있는데, 로컬 모듈로 설치된 이유는 webpack 의 livereload와 비슷한 기능인 ***`–hot`*** 옵션을 사용하기 위함 입니다. 사실 상, webpack 모듈을 글로벌 패키지로서 꼭 설치 할 필요는 없습니다. 이를 설치 한 이유는 커맨드라인에서 webpack-dev-server을 바로 실행하기 위함인데, 로컬에만 설치하고 나중에 webpack 을 실행할 때는 ./node_modules/bin/webpack-dev-server –hot 이런식으로 실행 할 수 있습니다.

# 4. 디렉토리 구조 이해 및 파일 생성

```bash
react-tutorial
├── package.json
├── public            # 서버 public path
│   └── index.html    # 메인 페이지
├── src               # React.js 프로젝트 루트
│   ├── components    # 컴포넌트 폴더
│   │   └── App.js    # App 컴포넌트
│   └── index.js      # Webpack Entry point
└── webpack.config.js # Webpack 설정파일
```

**Webpack entry point**는, Webpack 모듈에서 가장 처음으로 읽어들일 파일입니다.

Webpack entry point부터 시작해서, 해당 파일에서 import한 다른 모듈들을 불러옵니다.

```bash
mkdir src src/components public && touch public/index.html src/components/App.js src/index.js webpack.config.js
```

위와 같이 디렉토리를 만들어 줍니다.

# 5. 컴파일러, 서버 및 로더 설정

### webpack 설정하기 [webpack.config.js]

ECMAScript6를 컴파일해주고 개발서버를 열어주는 webpack의 설정파일 **webpack.config.js**를 수정하세요

```js
module.exports = {
    entry: './src/index.js',

    output: {
        path: __dirname + '/public/',
        filename: 'bundle.js'
    },

    devServer: {
        inline: true,
        port: 7777,
        contentBase: __dirname + '/public/'
    },

    module: {
            loaders: [
                {
                    test: /\.js$/,
                    loader: 'babel-loader',
                    exclude: /node_modules/,
                    query: {
                        cacheDirectory: true,
                        presets: ['es2015', 'react']
                    }
                }
            ]
        }
};
```

webpack의 역할은, entry 부터 시작하여 필요한 모듈들을 다 불러온 후, 한 파일로 합쳐 bundle.js에 저장합니다. 추가적으로, 모듈을 통하여 ES6 문법으로 작성된 코드를 ES5 형태로 변환도 해줍니다.

위 파일은 개발 서버 포트를 7777로 설정합니다. 개발 서버는 파일이 변동 될 때마다 다시 컴파일하고, 연결되어있는 브라우저를 새로고침해주는 기능을 가지고 있습니다.

### package.json

npm start 명령어를 콘솔에 입력 했을 때, webpackdev-server가 실행 될 수 있게 **package.json**의 "script" 블록을 수정하세요

```json
"scripts": {
    "start": "webpack-dev-server --hot --host 0.0.0.0"
},
```

> 만약에 localhost 가 아닌 외부 서버에서 dev-server 실행 시, –host 옵션을 추가하지 않으면 접근이 안됩니다.
localhost 인경우에는 생략하시면 됩니다.

# 6. HTML 및 JS 수정

### index.html

이 파일은 평범한 HTML 파일 입니다. div id = "app"을 React 프로젝트의 root element로 지정하고 index.js 스크립트를 로드해주세요. 이 파일은 webpack에서 bundle된 파일로서, react 라이브러리 및 기타 자바스크립트 파일들이 하나로 합쳐진 파일입니다.

```html
<!DOCTYPE html>
<html>

   <head>
      <meta charset="UTF-8">
      <title>React App</title>
   </head>

   <body>
      <div id="root"></div>
      <script src="bundle.js"></script>
   </body>

</html>
```

### src/components/App.js

우리가 만들 첫 React 컴포넌트입니다.

```js
import React from 'react';

class App extends React.Component {
    render(){

        return (
                <h1>Hello React Skeleton</h1>
        );
    }
}

export default App;
```

1번에서 사용된 **`import`** JavaScript ES6 에 새로 도입된 키워드로서, **`require('..')`** 의 역할을 합니다.

12번에서 사용된 **`export`**는 JavaScript ES6 에 새로 도입된 키워드로서, **`module.export = App`** 와 같습니다.

컴포넌트에 대한 설명은 다음 강좌에서 자세히 알아보도록 하겠습니다.

> 파일 및 컴포넌트의 첫 문자를 대문자로 하는건 React의 naming convention 입니다.

### src/index.js

```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App';

const rootElement = document.getElementById('root');
ReactDOM.render(<App />, rootElement);
```

App 컴포넌트를 불러와서 root element에 렌더링하는 부분입니다.

# 7. webpack-dev-server 구동하기

```bash
$ npm start
```

브라우저에서 http://localhost:7777로 접속을 한 후 App.js를 수정을 하면 자동으로 브라우저가 새로고침을 합니다.