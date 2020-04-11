---
description: Django 작업에서 설정에 기본적인 webpack 내용을 정리 합니다.
---

<figure class="align-center">
  <img src="{{site.baseurl}}/assets/images/dj_setting/webpack.png">
  <figcaption></figcaption>
</figure>

<br/> 

# **Introduction**

**React.js 를 포함하는 Django Project 환경구성** 이 이번의 목표 입니다. Django 와 React.js 활용하는 방법으로는 가장 대표적인 방법이 **Django Restful API backend** 와 **React.js frontend** 의 철저 분리 및 연결방식 활용 합니다.

이러한 방법은 Django 문법을 익힌 입장에서는 **Django 의 Template 문법** 및 **Router 기능을** 전혀 활용하지 못한채 오로지 backend DB 관리자로써 머문다는 한계가 있습니다. 이러한 고민 속에서 **[webpack in django](https://www.youtube.com/watch?v=A2vEazcfJ7U&feature=emb_title)** 동영상 내용을 참고하여 **Django Template 에서 Webpack 을 활용한 JavaScript 모듈의 활용** 을 내용으로 정리를 하게 되었습니다.

<iframe width="560" height="315" src="https://www.youtube.com/embed/A2vEazcfJ7U" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

보다 자세한 내용은 **[webpack 정리 블로그](https://yongbeomkim.github.io/webpack/django-webpack-static-install/)** 와 **[YouTube 동영상](https://www.youtube.com/watch?v=A2vEazcfJ7U&feature=emb_title)** 을 참고 하세요, YouTube 동영상의 목차는 다음과 같습니다.

1. python venv & install djangozx
1. django homepage
1. webpack 
1. webpack-dev-server
1. hot module replacement with js
1. hot module replacement with css
1. babel
1. react
1. react-hot-loader

<br/>

# **Django & Webpack bundle JavaScript**

Django 의 Static 폴더에서 사용하는 Js, CSS 작업 내용의 연결을, Webpack 을 사용하여 **bundle** 파일을 활용하면 **Node Package Manager** 모듈을 활용 할 수 있습니다. 첫번째로는 **Django 에서 Webpack** 의 연결과, **bundle 로 압축된 파일을 Django Template 에서 활용** 가능한 Tutorial 로써 배포 단계에서는 **Django 의 서버** 만으로도 모든 기능이 구현 가능합니다.

## **1 Setting & Installation**

### **01 Install & Coding the Django**

2020년 4월에 작성하는 문서로써, React.js 가 16 이후부터 안정화 단계에 들어온 만큼 **yarn** 을 사용하여 모듈을 설치하는 내용으로 정리 하였습니다. Vue.js 등은 **[stackoverflow](https://stackoverflow.com/questions/33628558/vue-js-change-tags)** 내용을 참고하면 좋습니다.

**[Yarn 의 설치](https://classic.yarnpkg.com/en/docs/install#debian-stable)** 및 **[기본 명령어 Yarn init](https://classic.yarnpkg.com/en/docs/cli/init/)** 는 해당 링크의 내용을 참고 합니다.

```r
# Django 설치
~ $ django-admin startproject mysite
~ $ cd mysite 
~/mysite $ ./manage.py startapp app      
~/mysite $ ./manage.py makemigrations && python manage.py migrate
~/mysite $ mkdir -p app/templates static/{js,css}
~/mysite $ tree -d -L 3 
├── app
│   ├── __init__.py
│   └── templates
└── static
    ├── css
    └── js
```

`./manage.py runserver` 에서 실행 가능한 **static 설정** 을 추가 합니다.

```python
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static'),
]
```

### **02 Install Webpack by Yarn**

**Django** 를 설치한 뒤 **Yarn** 을 사용하여 환경설정 및 **[Webpack](https://webpack.js.org/guides/installation/)** 을 설치 합니다.

```r
~/mysite $ cd static
# package.json 생성
~/mysite/static $ yarn init
~/mysite/static $ yarn init -y  # 빠르게 환경설정 할 때
# webpack 모듈의 설치
~/mysite/static $ yarn add webpack webpack-cli
~/mysite/static $ tree -d -L 3           
.
├── app
│   └── templates
├── server
└── static
    ├── css
    ├── js
    └── node_modules
        ├── @webassemblyjs
```

<br/>

## **2 Building & Configuration**

### **01 Package.Json**

`$ yarn init` 를 실행하면 **Node.js** 모듈이 설치 됩니다. 설정 내용은 **package.json** 에 기록되어 있습니다.

webpack 을 사용한 build 파일을 만들기 위한 **$ yarn build** 의 실행을 위해서는 **package.json** 에 **[실행 Scripts](https://stackoverflow.com/questions/54693223/what-does-yarn-build-command-do-are-npm-build-and-yarn-build-similar)** 를 추가 한 뒤에 실행을 해야 합니다.

```r
~/mysite/static $ vi package.json

{
    "name": "mypackage",
    "version": "0.0.1",
    "scripts": {
       "build": "webpack",
    }
}
```

### **02 Webpack Build Scripts**

**build** 파일을 만들기 위한 실행 내용은 아래의 내용과 같이 상세하게 서술할 수도 있습니다. 실행을 위해서는 1개만 입력되어 있으면 됩니다. 

```r
"build": "webpack --config webpack.dev.js",
"build": "webpack --config webpack.config.js"
```

### **03 Webpack.config.js**

Webpack 으로 빌드하는 파일 정보는 **webpack.config.js** 에서 정의 합니다. 이 파일은 **Webpack** 의 설정 파일로 주석은 `{* *}` 을 사용하고, 경로는 `./js/index.js` 와 같이 **상대경로를 사용** 해야 한다는 점 등에 유의 합니다.

이제 가장 간단한 내용에 대한 설정값 내용들을 살펴 보겠습니다

```r
module.exports = {
    mode: 'development',
    entry: {
        app: './static/js/index.js'
    },
    output: {
        filename: '[name].bundle.js',
    },
}
```

구체적인 build 대상과, 생성자를 정의한 뒤, `$ yarn build` 작업을 실행하면 `./dist/app.bundle.js` 파일이 생성됨을 알 수 있습니다.

```r
~/mysite/static $ yarn build
yarn run v1.21.1
Asset           Size  Chunks  Chunk  Names
app.bundle.js  3.98   KiB     app    [emitted]  app
Entrypoint app = app.bundle.js
Done in 0.40s.
~/mysite/static $ tree -d -L 3           
.
├── app
│   └── templates
├── server
└── static
    ├── css
    ├── dist
    ├── js
    └── node_modules
        ├── @webassemblyjs
        ├── @xtuc
```

build 로 생성된 bundle 파일은 Django 의 `settings.py` 에서 Static 설정값에 './dist' 경로를 추가 합니다.

```python
# settings.py
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    # os.path.join(BASE_DIR, 'static'),
    os.path.join(BASE_DIR, 'static/dist'),
]
```

그리고 이에 맞춰서 Django Template 의 내용을 변경하면 됩니다. 그리고 실행하면 빌드 파일이 자연스럽게 Django 의 Template 문법과 어울리면서 작동 되는 모습을 보실 수 있습니다.

```html
{% raw %}
<script src="{% static 'app.bundle.js' %}"></script>
{% endraw %}
```

<br/>

# **Webpack Dev Server & NodeMon**

앞에서 작업한 내용을 정리하면, Webpack 의 번들 파일로 압축한 뒤에야만 배포 단계에서 작동 내용을 확인 할 수 있습니다. 빈번한 수정 작업시에는 매우 귀찮은 과정을 중간에 계속 필요로 하는데, 이러한 문제에서 고안된 기능이 **Hot Reloaded Mode** 에서 작동하는 **Dev Server** 입니다.

## **1 Webpack Dev Server**

### **01 Package Install & running the Script**

웹팩 dev server 를 설치 한 뒤 실행하는 과정 입니다.

```r
# dev-server 설치 후 package.json 에 실행 스크립트 추가
~/mysite/static $ yarn add -D webpack-dev-server

# dev server 실행 스크립트 추가
~/mysite/static $ vi package.json
  "scripts": {
    "start": "webpack-dev-server"  
  },
```

설치 및 실행 스크립트를 작성한 뒤, `$ yarn start` 를 실행하면, `http://localhost:8080` 에서 다음과 같이 webpack dev server 가 8080 번 포트에서 실행되는 모습을 볼 수 있습니다.

<div>
  <input id="search" type="text" placeholder="Search" autocomplete="off" />
  <div id="wrapper">
  <h1> / </h1>
  <ul>
    <li><a href="/css" class="" title="css">css</a></li>
    <li><a href="/dist" class="" title="dist">dist</a></li>
    <li><a href="/js" class="" title="js">js</a></li>
</ul>
</div>
</div>


### **02 Django Setting**

위에서 살펴본 대로 static 연결경로가 `http://localhost:8080` 에 1개 더 추가가 되었습니다. 여기서 실행내용을 Test 해 보려면 `settings.py` 에서 다음과 같이 내용을 변경 합니다.

```python
STATIC_URL = 'http://localhost:8080/'
STATICFILES_DIRS = [
    'dist',
]
```

<br/>

## **2 Nodemon**

**Node.js Monitoring** 도구로, 내용의 수정이 있을때 마다 자동으로 재실행 하는 모듈 입니다. 

### **01 Install & running the Script**

> nodemon -w webpack.config.js -x webpack-dev-server

실행 스크립트를 설명하면, `-w: watch`, `-x: excutive` 설정 입니다. 자세한 내용은 다음의 **[npm nodemon post](https://www.npmjs.com/package/nodemon)** 를 참고 합니다.

```r
~/mysite/static $ yarn add -D nodemon
~/mysite/static $ vi package.json

  "scripts": {
    "start": "nodemon -w webpack.config.js -x webpack-dev-server"
  },

~/mysite/static $ yarn start
```

Django 서버와 webpack bundle 파일을 연결하여 실행 하면서, 해당 내용이 변경시 제대로 동작 하는지를 확인 합니다.

<br/>

## **3 Hot Module Replacement**

webpack 에서 지원하는 기능으로 **[정식문서](https://webpack.js.org/concepts/hot-module-replacement/)** 내용을 살펴보면 javascript 모듈이 여러개로 나누어 져 있을때, Full reloading 을 하지 않고, 부분 부분으로 실행을 하는 기능 입니다.

이러한 기능은 **[생산력 향상](https://ibrahimovic.tistory.com/47)** 에 도움을 줍니다. 따라서 우리가 해야할 일은 webpack-dev-server 구성에 추가로 웹팩에 내장된 HMR 플러그인을 사용하는 것 뿐입니다.

## **01 Running the Script with HMR**

실행은 간단합니다. 실행 스크립트 맨 뒤에 `--hot` 를 추가하면 됩니다.

```r
~/mysite/static $ vi package.json

  "scripts": {
    "start": "nodemon -w webpack.config.js -x webpack-dev-server --hot"
  },
```

## **02 JavaScript**

Hot Module 이 적용될 떄, console 에서는 Warning 메세지들이 출력 됩니다. 실행에는 문제가 있지 않지만 다른 오류를 찾는데 방해가 될 수도 있는만큼 아래의 내용을 개별 script 에 추가를 하면 됩니다. 모든 파일에 다 추가할 필요는 없고, HRM 작업시 빈번하게 수정 작업이 이루어 지는 파일에 추가를 하면 됩니다. <strike>내용은 뜨거우면 뜨거운 대로 동작하면 된다는 내용 입니다.</strike>

```javascript
if (module.hot) {
    module.hot.accept();
}
```

timer 와 같이 계속적인 변화가 이루어지는 명령같은 경우에는, status 가 여러개가 겹쳐서 실행되는 모습을 볼 수 있습니다. 이는 React 등에서 사용되는 **생명주기별 초기화** 작업이 필요한 경우로써 다음과 같이 상태가 변화시 초기값 지정하는 내용을 추가하면 해결 가능합니다.

```javascript
const timer = setInterval( () => counter.innerText = ++count, 1000);

if (module.hot) {
  module.hot.dispose(()=>{
    clearInterval(timer)
  });
  module.hot.accept();
}
``` 

## **02 Webpack.Config.Js**

새로고침을 할때마다 Console 에서 오류를 출력합니다. Django 실행 Port 와 Webpack-dev-Server 실행 Port 가 서로 달라서 이를 찾는데 발생하는 오류 입니다. 이러한 문제를 해결하기 위해, Webpack 의 출력포트를 설정 파일에 추가 합니다.

```r
~/mysite/static $ vi webpack.config.js

module.exports = {
  output: {
    ...
    publicPath: 'http://localhost:8080/'
  },
}
```

작업한 뒤 실행하면 **Access-Control-Allow-Origin** 오류가 당연하게도 발생 합니다. 다른 내용에서는 `pip install django-cors-headers` 를 사용하여 해결 하였지만, 아래의 내용을 Webpack 설정 파일에 추가만 하면 해당 오류는 더이상 발생하지 않습니다. <strike>구분하여 실행하는 방식이 모든 내용을 작업자가 몰라도 가능한 만큼 장단점이 존재 합니다.</strike>

```r
~/mysite/static $ vi webpack.config.js

module.exports = {
  ...
  devServer: { headers: { 'Access-Control-Allow-Origin': '*' } }
}
```

### **03 HRM in CSS ,Sytle Loader**

bundle 로 압축되는 JavaScript 에 Css 를 추가 하고 실행하면 Webpack 에서 오류가 발생합니다. 

```r
$ nvim main.js
import '../css/hello.css';

$ yarn start
ERROR in ./css/hello.css 1:0
Module parse failed: Unexpected character '#' (1:0)
｢wdm｣: Failed to compile.
```

Node.js 유틸리티인 **loader** 를 설치 및 연결해야 합니다. **loader** 는 `.css, .scss, .ts` 타입의 **non-javascript** 파일을 컴파일 하는 유틸리티 입니다. **[css-loader](https://www.npmjs.com/package/css-loader)** 는 **css** 파일을 찾아 문자열로 정리해주며, **[style-loader](https://www.npmjs.com/package/style-loader)** 는 정리된 문자열을 index.html 파일 내부의 `<style>`태그에 넣어줍니다.

```r
module.exports = {
  ...
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  },
```

<br/>

# **Webpack & React.Js**

지금까지 작업한 내용을 살펴보면 다음과 같습니다.

1. Django App Start
1. Webpack bundle Start
1. Webpack Hot Reload Module Start
1. Webpact with CSS

마지막으로 react.js 를 포함하는 방법을 살펴보겠습니다.

<br/>

## **1 babel**

### **01 Install Babel Loader**

JSX 문법을 활용 가능하도록 **[babel loader](https://www.npmjs.com/package/babel-loader)** 유틸리티를 설치 합니다. 아래의 내용은 2020년 4월을 기준으로 작성한 내용으로, 작업때 마다 npm 페이지에서 해당 내용을 확인 합니다.

```r
~/mysite/static $ yarn add -D babel-loader @babel/core @babel/preset-env
```

### **02 Install @babel/preset-react**

지금까지 작업한 Webpack 과 babel 위에서 React 를 사용하기 위한 추가 유틸리티 설치 및 설정 작업이 필요 합니다. 이 부분은 유사한 종류가 많다보니 문서 작성년도에 따라 활용하는 유틸리티가 다른 경우가 있는만큼 이점에 주의해서 작업을 진행하도록 합니다.

예전 문서에는 **react-loader** 를 사용하는 경우도 있었지만, 지금은 추천 및 사용자가 압도적으로 많은 **[@babel/preset-react](https://www.npmjs.com/package/@babel/preset-react)** 를 사용 합니다.

```r
~/mysite/static $ yarn add -D @babel/preset-react
```

### **03 Babel React Preset Setting**

**[Babel 문서](https://babeljs.io/docs/en/babel-preset-react)** 를 참고하면 내부 메서드 등에 대해 상세한 설명을 볼 수 있습니다. 바로 앞에서 설치한 **babel-loader** 와 연결성이 좋은만큼 위에서 설치한 내용과 동일한 설정값에서, options 의 presets 부분에 `"@babel/preset-react"` 을 추가 하면 됩니다.

```r
~/mysite/static $ vi webpack.config.js

module: {
  rules: [
    {
      test: /\.js?$/,
      exclude: /(node_modules|bower_components)/,
      use: {
        loader: 'babel-loader',
        options: { 
          presets: ['@babel/preset-env', ] }
      }
    }
  ]
}
```

작업을 완료한 뒤 build 가 제대로 동작 하는지를 확인 합니다.

<br/>

## **2 React**

### **01 Install React**

작업을 위한 Django, WebPack, Babel 의 작업이 완료 되었습니다. 이제는 이 위에서 **[React](https://www.npmjs.com/package/react)** 와 **[React-dom](https://www.npmjs.com/package/react-dom)** 을 설치하고 작업을 시작 합니다.

```r
~/mysite/static $ yarn add -D react react-dom
```

작업을 한 뒤 react 코드를 추가하면 제대로 작동하는 모습을 볼 수 있습니다.

### **02 React hot Loader**

Hot Reload Module 이 React 에는 포함되어 있지 않아서 `[HMR] Cannot apply update. Need to do a full reload!` 경고 메세지가 출력 됩니다. 이에 대응 가능한 [@hot-loader/react-dom](https://www.npmjs.com/package/@hot-loader/react-dom) 대신에 **[react-hot-loader](https://www.npmjs.com/package/react-hot-loader)** 를 추가 합니다.  

```r
~/mysite/static $ yarn add -D react-hot-loader
```

그리고 React 코드상에서 export 부분에 hot() 함수로 객체를 감싸면  됩니다.

```php
import { hot } from 'react-hot-loader/root';
const App = () => <div>Hello World!</div>;

export default hot(App);
```

지금까지 완벽하게 Django 와 React.js 를 연결하는 방법을 학습 하였습니다. 여러번 반복하여 보다 안정적이면서 강력한 서비스를 만드는데 기초가 되는 내용이었으면 좋겠습니다.