---
description: Django 작업에서 설정에 기본적인 webpack 내용을 정리 합니다.
---

<figure class="align-center">
  <img src="{{site.baseurl}}/assets/images/dj_setting/webpack.png">
  <figcaption></figcaption>
</figure>

## **Django & webpack**

### **Introduction**

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

## **Setting & Installation**

### **Install Django & Webpack by Yarn**

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

**Django** 를 설치한 뒤 **Yarn** 을 사용하여 환경설정 및 **[Webpack](https://webpack.js.org/guides/installation/)** 을 설치 합니다.

```r
~/mysite $ cd static
# package.json 생성
~/mysite/static $ yarn init
~/mysite/static $ yarn init -y  # 빠르게 환경설정 할 때
# webpack 모듈의 설치
~/mysite/static $ yarn add webpack webpack-cli
```

## **Building & Configuration**

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

### **Webpack.Setting.Js**

**build** 파일을 만들기 위한 실행 내용은 아래의 내용과 같이 상세하게 서술할 수도 있습니다. 실행을 위해서는 1개만 입력되어 있으면 됩니다. 

```r
"build": "webpack --config webpack.dev.js",
"build": "webpack --config webpack.config.js"
```

빌드하는 파일에 대해서는 **webpack.config.js** 에서 내용을 정의하면 됩니다. 이 파일은 **Webpack** 의 설정 파일이고, 내용 작성에 있어서 주의할 점 몇가지를 유념해야 합니다. 구체적으로는 **주석을 포함하면 안되고**, 경로는 `./js/index.js` 와 같이 **상대경로를 사용** 해야 한다는 점 등이 있습니다.

```r
~/mysite/static $ yarn build
yarn run v1.21.1
Built at: 2020-04-10 7:29:34 PM
  Asset      Size  Chunks             Chunk Names
main.js  3.98 KiB    main  [emitted]  main
Done in 0.38s.

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
