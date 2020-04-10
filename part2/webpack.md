---
description: Django 작업에서 설정에 기본적인 webpack 내용을 정리 합니다.
---

<figure class="align-center">
  <img src="{{site.baseurl}}/assets/images/dj_setting/webpack.png">
  <figcaption></figcaption>
</figure>


# **Django & webpack**

블로그에 저장한 내용을 참고하여 정리했습니다. **[webpack](https://yongbeomkim.github.io/webpack/django-webpack-static-install/)** 보다 상세한 내용은 아래의 YouTube 를 참고하면 보다 정확한 내용을 아실 수 있습니다.

<iframe width="560" height="315" src="https://www.youtube.com/embed/A2vEazcfJ7U" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


## **Content**

Django 를 중심으로 프로젝트를 진행하는 가장 적합한 방식 입니다.
동영상에서 진행되는 목차를 살펴보면 다음과 같습니다.

1. python venv & install django
1. django homepage
1. webpack 
1. webpack-dev-server
1. hot module replacement with js
1. hot module replacement with css
1. babel
1. react
1. react-hot-loader

## **webpack**

backend 로 작동할 django 프로젝트를 설치합니다.

```r
~ $ django-admin startproject mysite
~ $ cd mysite 
~/mysite $ ./manage.py startapp app      
~/mysite $ ./manage.py makemigrations && python manage.py migrate
~/mysite $ mkdir -p app/templates static/{js,css}
├── app
│   ├── __init__.py
│   └── templates
└── static
    ├── css
    └── js
```

node package 를 내부에 설치 합니다.

```r
~/mysite $ cd static
# package.json 생성
~/mysite/static $ npm init -y
# webpack 모듈의 설치
~/mysite/static $ npm i webpack webpack-cli --save
```
이렇게 기본적인 설치 작업이 종료된 뒤, 원활한 연결이 이루어 지도록 추가 설정이 필요 합니다.