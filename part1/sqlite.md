---
description: Cent OS 7.7 에서 Sqlite Setting 을 정리 합니다.
---

<figure class="align-center">
  <img src="{{site.baseurl}}/assets/images/os/sqlite_banner.jpg">
  <figcaption></figcaption>
</figure>

# SQLite3

[Django2.2 & SQlite3](http://www.djaodjin.com/blog/django-2-2-with-sqlite-3-on-centos-7.blog.html)

[Sqlite Upgrade](https://stackoverflow.com/questions/55674176/django-cant-find-new-sqlite-version-sqlite-3-8-3-or-later-is-required-found)

Cent OS 에서 기본 설치된 버젼은 **3.7.17** 입니다. **Django 2.2** 를 설치 후 실행을 하면 sqlite 의 버젼을 확인하는 다음의 코드가 추가되어 있습니다. `"ModuleNotFoundError: No module named '_sqlite3'"`

```python
raise ImproperlyConfigured('SQLite 3.8.3 or later is required (found %s).' % Database.sqlite_version)
```

sqlite3 를 업데이트 하는 과정이 필요 합니다. [SQLite Download, Installation and Getting started](https://www.w3resource.com/sqlite/sqlite-download-installation-getting-started.php)

You may end up receiving a **"SQLite header and source version mismatch"** error message after you finished installation if you run `./configure` command. To avoid this you may run the following `./configure` command

[myFile.js test from github](https://raw.githubusercontent.com/YongBeomKim/gitbook/gh-pages/assets/downloads/sh/test.sh)

[myFile.js test.sh]({{site.baseurl}}/assets/downloads/sh/test.sh)

## Sqlite3 Upgrade

Sqlite 3 를 설치하는 방법은 다음과 같습니다. [https://www.sqlite.org](https://www.sqlite.org) 에서 최신버전을 찾아서 알에 적용하면 설치 됩니다.
 
```r
# install depedancey
yum -y install gcc

# sqlite update
wget https://www.sqlite.org/2020/sqlite-autoconf-3310100.tar.gz
tar -xzvf sqlite-autoconf-3310100.tar.gz
cd sqlite-autoconf-3310100
./configure --prefix=/usr/local

# ./configure --prefix=$HOME/opt/sqlite
# ./configure --disable-dynamic-extensions --enable-static --disable-shared
make && make install
```

이러한 설치방법은 Python 에서 연결 모듈을 확인하면 제대로 실행되지 않는 경우가 있습니다.

```r
Python 3.7.7 (default) 
>>> import _sqlite3
>>> _sqlite3.sqlite_version
'3.7.17'
```

최신버젼을 설치했음에도 위처럼 오류가 발생하는 이유는 다음과 같습니다.

```r
[root@localhost ~]$ /usr/bin/sqlite3
SQLite header and source version mismatch
2013-05-20 00:56:22 118a3b35693b134d56ebd780123b7fd6f1497668
2015-10-14 12:29:53 a721fc0d89495518fe5612e2e3bbc60befd2e90d
```

설치된 버젼과 실행되는 버젼이 달라서 생기는 문제로 현재 설치방법으로 해결하는 방법은 찾지 못했습니다. 따라서 `.bashrc` 등에서 환경설정으로 새로 설치한 버젼을 연결 및 실행하는 방식으로 위 문제를 해결 가능합니다.

```r
$ nvim ~/.bashrc
export LD_LIBRARY_PATH="/usr/local/lib"
```

참고로 sqlite3 를 삭제하는 방법은 찾은결과는 다음과 같습니다.

```r
# 먼저 설치된 sqlite3 를 삭제 합니다.
[root@localhost ~]$ yum erase 'sqlite*'

[root@localhost ~]$ whereis sqlite3
sqlite3: /usr/local/bin/sqlite3
[root@localhost ~]$ rm /usr/local/bin/sqlite3 

[root@localhost ~]$ whereis libsqlite3 
libsqlite3: /usr/local/lib/libsqlite3.la /usr/local/lib/libsqlite3.a /usr/local/lib/libsqlite3.so

[root@localhost ~]$ rm /usr/local/lib/libsqlite3.* 
[root@localhost ~]$ whereis libsqlite3             
libsqlite3:#
```