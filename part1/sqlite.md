---
description: Cent OS 7.7 에서 Sqlite Setting 을 정리 합니다.
---

<figure class="align-center">
  <img src="{{site.baseurl}}/assets/images/os/sqlite_banner.jpg">
  <figcaption></figcaption>
</figure>

# SQLite3

Cent OS 에서 기본 설치된 버젼은 **3.7.17** 입니다. **Django 2.2** 를 설치 후 실행을 하면 sqlite 의 버젼을 확인하는 다음의 코드가 추가되어 있습니다.

```python
raise ImproperlyConfigured('SQLite 3.8.3 or later is required (found %s).' % Database.sqlite_version)
```

sqlite3 를 업데이트 하는 과정이 필요 합니다. [SQLite Download, Installation and Getting started](https://www.w3resource.com/sqlite/sqlite-download-installation-getting-started.php)

You may end up receiving a **"SQLite header and source version mismatch"** error message after you finished installation if you run `./configure` command.
 To avoid this you may run the following `./configure` command

[myFile.js]({{file name="{{site.baseurl}}/assets/downloads/sh/test.sh"}})

[myFile.js]({{file name='myFile.js'}})



```r
[root@localhost ~]$ wget https://www.sqlite.org/2020/sqlite-autoconf-3310100.tar.gz

[root@localhost ~]$ tar -xzvf sqlite-autoconf-3310100.tar.gz

[root@localhost ~/sqlite-autoconf-3310100]$ ./configure --disable-dynamic-extensions --enable-static --disable-shared

[root@localhost ~/sqlite-autoconf-3310100]$ make
[root@localhost ~/sqlite-autoconf-3310100]$ make install
```



이러한 문제를 해결하는 작업순서는 아래와 같습니다.

1. sqlite3 를 먼저 설치를 하고 확인 합니다.
2. Python 을 원하는 버젼으로 설치 합니다.
3. 작업이 완료된 뒤 Python 에서 sqlite 호출 내용을 확인 합니다.

우선 Python 에서 설치 및 호출 내용을 확인 합니다.

```r
Python 3.7.7 (default) 
>>> import _sqlite3
>>> _sqlite3.sqlite_version
'3.7.17'
```

새로 설치하기 전에 기존에 설치된 내용을 제대로 삭제작업을 진행 합니다. 완전히 지우지 않으면 호출시 내용의 충돌이 생겨서 문제가 계속 발생 합니다.

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



```r
wget https://kojipkgs.fedoraproject.org/packages/sqlite/3.9.0/1.fc21/x86_64/sqlite-devel-3.9.0-1.fc21.x86_64.rpm

wget https://kojipkgs.fedoraproject.org/packages/sqlite/3.9.0/1.fc21/x86_64/sqlite-3.9.0-1.fc21.x86_64.rpm

yum install sqlite-3.9.0-1.fc21.x86_64.rpm sqlite-devel-3.9.0-1.fc21.x86_64.rpm

[root@localhost ~]$ /usr/bin/sqlite3
SQLite header and source version mismatch
2013-05-20 00:56:22 118a3b35693b134d56ebd780123b7fd6f1497668
2015-10-14 12:29:53 a721fc0d89495518fe5612e2e3bbc60befd2e90d
```


```r
https://www.sqlite.org/2020/sqlite-autoconf-3310100.tar.gz
```





## Install Python3

가장 활용도가 높은 **Python 3** 을 **Cent OS** 에서 정의된 기본 버젼을 설치합니다.

```r
# Install Python 3.6
yum -y install centos-release-scl
yum -y install rh-python36
. /opt/rh/rh-python36/enable # or scl enable rh-python36 bash [if interactive]
# yum install -y python36u
# yum install -y python36u-pip
```

**[특정 버젼](https://computingforgeeks.com/how-to-install-python-on-3-on-centos/)** 을 설치하려면 다음의 내용을 실행 합니다. 버젼 내용을 다르게 변경하려면 `https://www.python.org/ftp/python/3.7.7/Python-3.7.7.tgz` 의 내용을 **[Python 사이트](https://www.python.org/downloads/)** 에서 검색한 뒤 변경한 내용으로 실행 합니다.

```r
# 설치 과정에 필요한 의존성 문제를 해결하는 프로그램들 설치 
yum groupinstall -y "Developent Tools"
yum -y install openssl-devel bzip2-devel libffi-devel

wget https://www.python.org/ftp/python/3.7.7/Python-3.7.7.tgz
tar xzf Python-3.7.7.tgz
cd Python-3.7.7
./configure --enable-optimizations
make altinstall
```

## "ModuleNotFoundError: No module named '_sqlite3'"




## Node Version Manager (nvm)

[NVM](https://github.com/nvm-sh/nvm#installing-nvm-on-alpine-linux) 의 공식문서로 작업 중 문제가 발생시 참고를 하면 됩니다.

**node.js** 는 자바스크립트 모듈을 실행하는데 활용하는 프로그램으로 버젼을 다르게 테스트할 필요가 을때 활용 합니다. 아래의 예제는 설치 후 10.17.0 버젼의 **node.js** 를 설치하는 과정 입니다.

```r
# bash terminal 에서 설치시
[root@localhost ~]$ curl -sL https://rpm.nodesource.com/setup_10.x | sudo bash -

# zsh terminal 에서 설치시
[root@localhost ~]$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.1/install.sh | zsh

[root@localhost ~]$ nvm --version
0.35.1

[root@localhost ~]$ nvm ls-remote | grep "v10.*LTS"
v10.18.0   (LTS: Dubnium)
v10.18.1   (LTS: Dubnium)
v10.19.0   (Latest LTS: Dubnium)

[root@localhost ~]$ nvm install 10.17.0
[root@localhost ~]$ node -v
v10.17.0
```