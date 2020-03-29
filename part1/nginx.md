---
description: Cent OS 7.7 에서 Nginx 설치 및 설정 내용을 정리 합니다.
---

<figure class="align-center">
  <img src="{{site.baseurl}}/assets/images/server/nginx_gunicorn.jpg">
  <figcaption></figcaption>
</figure>


# **NGINX**

블로그에 저장한 내용을 참고하여 정리했습니다. **[Nginx](https://yongbeomkim.github.io/django/dj-guni-cent/)** 

## nginx 실행하기

앞에서 설치된 내용을 실행 및 확인하는 방법은 다음과 같습니다.

```r
[root@localhost ~]$ nginx -v
nginx version: nginx/1.17.6

[root@localhost ~]$ systemctl enable nginx
[root@localhost ~]$ systemctl start nginx
[root@localhost ~]$ systemctl status nginx
● nginx.service - nginx - high performance web server
  Process: 30220 ExecStart=/usr/sbin/nginx 
  CGroup: /system.slice/nginx.service
          └─30222 nginx: worker process

[root@localhost ~]$ netstat -ntlp | grep :80
tcp   0   0 0.0.0.0:80   0.0.0.0:*   LISTEN  30221/nginx: master 

[root@localhost ~]$ curl -i http://localhost
```

## Django & Gunicorn 연결