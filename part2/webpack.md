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

### **Install & Coding the Django**

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

### **Install Webpack by Yarn**

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

### **Package.Json**

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

### **Webpack Build Scripts**

**build** 파일을 만들기 위한 실행 내용은 아래의 내용과 같이 상세하게 서술할 수도 있습니다. 실행을 위해서는 1개만 입력되어 있으면 됩니다. 

```r
"build": "webpack --config webpack.dev.js",
"build": "webpack --config webpack.config.js"
```

### **Webpack.config.js**

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

# **Hot Reloaded Webpack Dev Server**

앞에서 작업한 내용을 정리하면, Webpack 의 번들 파일로 압축한 뒤에야만 배포 단계에서 작동 내용을 확인 할 수 있습니다. 빈번한 수정 작업시에는 매우 귀찮은 과정을 중간에 계속 필요로 하는데, 이러한 문제에서 고안된 기능이 **Hot Reloaded Mode** 에서 작동하는 **Dev Server** 입니다.

## **1 Webpack Dev Server**

```r
# dev-server 설치 후 package.json 에 실행 스크립트 추가
~/mysite/static $ yarn add -D webpack-dev-server
~/mysite/static $ vi package.json
  "scripts": {
    "start": "webpack-dev-server"  # dev server 실행 스크립트 추가
  },
```

설치 및 실행 스크립트를 작성한 뒤, `$ yarn start` 를 실행하면, `http://localhost:8080` 에서 다음과 같이 webpack dev server 가 8080 번 포트에서 실행되는 모습을 볼 수 있습니다.

<html>
<body class="directory">
  <input id="search" type="text" placeholder="Search" autocomplete="off" />
  <div id="wrapper">
  <h1> / </h1>
  <ul>
    <li>
      <a href="/css" class="" title="css">
        <span class="name">css</span>
        <span class="size"></span>
        <span class="date">2020-4-10 3:35:22 PM</span>
      </a>
    </li>
    <li>
      <a href="/dist" class="" title="dist">
        <span class="name">dist</span>
        <span class="size"></span>
        <span class="date">2020-4-11 4:47:53 PM</span>
      </a>
    </li>
    <li>
      <a href="/js" class="" title="js">
        <span class="name">js</span>
        <span class="size"></span>
        <span class="date">2020-4-10 3:42:36 PM</span>
      </a>
    </li>
</div>
</body>
</html>


# package.json 에 Node.js 스크립트 추가



```r
~/mysite/static $ yarn add -D react react-dom prop-types
~/mysite/static $ yarn add -D babel-plugin-transform-class-properties
~/mysite/static $ yarn add -D @babel/core babel-loader @babel/preset-env @babel/preset-react

~/mysite/static $ yarn add -D nodemon
~/mysite/static $ yarn add -D webpack-dev-server
~/mysite/static $ yarn add -D style-loader babel-loader react-hot-loader
~/mysite/static $ yarn add -D @hot-loader/react-dom
```

### **Webpack.config.js**

필요한 모듈을 설치하였으면 이를 실행하는 설정값들을 추가 합니다.

```r
module.exports = {
  mode: 'development',
  entry: {
    app: './js/index.js'
  },
  output: {
    filename: '[name].bundle.js',
    publicPath: 'http://localhost:8080/',
  },
  module: {
    rules: [
      {  
       	test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
      {
        test: /\.(js|jsx|tsx|ts)?$/,
        include: /node_modules/,
        use: ['react-hot-loader/webpack'],
      },
      {
        test: /\.js$/,
        exclude: /(node_modules|bower_components)/,
        use: {
	        loader: 'babel-loader',
	        options: { 
	          presets: [
	            '@babel/preset-env',
	            "@babel/preset-react"
              ]
	        }
	    }
	  }
	]
  },
  devServer: {
	headers: {"Access-Control-Allow-Origin":"*"},
}
```


<br/>

## **React.js**

### **Static with Django & Webpack**

### **Package.Json**
