---
description: Cent OS 7.7 에서 Terminal Setting 을 정리 합니다.
---

<figure class="align-center">
  <img src="{{site.baseurl}}/assets/images/os/centos_logo.png">
  <figcaption></figcaption>
</figure>

# [Cent OS](https://yongbeomkim.github.io/linux/centos-setting/) Setting

1. Python, Nginx, Nvim 등 도구설치
2. MySQL, MariaDB 설치

## User Setting

```r
# 사용자 기본암호 변경
[root@localhost ~]$ passwd root
새 암호:

[root@localhost ~]$ cat /etc/*release*
CentOS Linux release 7.7.1908 (Core)
Derived from Red Hat Enterprise Linux 7.7 (Source)
NAME="CentOS Linux"

[root@localhost ~]$ getconf LONG_BIT
64
```

## User Add & Delete

추가 사용자정보 추가 및 삭제 방법은 다음과 같습니다. 보다 [리눅스](https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4) 에 대한 자세한 내용을 참고하시면 됩니다.

```r
# 새로운 사용자 계정 추가
[root@localhost ~]$ useradd pythonserver
[root@localhost ~]$ passwd pythonserver
pythonserver 사용자의 비밀 번호 변경 중
새  암호: 

# 사용자의 권한범위 설정
[root@localhost ~]$ visudo
root    ALL=(ALL)   ALL 
pythonserver  ALL=(ALL)   ALL 

# 사용자 자료 제거
[root@localhost ~]$ userdel pythonserver
[root@localhost ~]$ rm -rf /etc/pythonserver
```

## Python3 Install 

가장 활용도가 높은 **Python 3** 을 **Cent OS** 에서 정의된 기본 버젼을 설치하는 방법은 다음과 같습니다.

```r
# Install Python 3.6
yum -y install centos-release-scl
yum -y install rh-python36
. /opt/rh/rh-python36/enable # or scl enable rh-python36 bash [if interactive]
# yum install -y python36u
# yum install -y python36u-pip
```

서버 운영과 목적에 따라 **[특정 버젼](https://computingforgeeks.com/how-to-install-python-on-3-on-centos/)** 을 설치하는 경우에는 다음의 내용을 따라서 실행 합니다. 버젼 `https://www.python.org/ftp/python/3.7.7/Python-3.7.7.tgz` 내용은 **[Python 사이트](https://www.python.org/downloads/)** 에서 검색을 하면서 버젼을 찾아서 적용하면 됩니다.

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

## SQLite3 Upgrade

```r
# Install Depandency Program
yum -y install gcc

# Sqlite update
wget https://www.sqlite.org/2020/sqlite-autoconf-3310100.tar.gz
tar -xzvf sqlite-autoconf-3310100.tar.gz
cd sqlite-autoconf-3310100
./configure --prefix=/usr/local
# ./configure --prefix=$HOME/opt/sqlite
# ./configure --prefix=/usr/local

make && make install
```

설치 후, 환결설정값에 `export LD_LIBRARY_PATH="/usr/local/lib"` 를 추가하면 제대로 작동이 됩니다. 대신 `/usr/local/lib` 를 실행하면 발생하는 오류는 추후에 보완하도록 합니다.

```r
# /root/.bashrc 에 등록하면 Booting 시 자동 실행 됩니다.
# vi /etc/rc.d/rc.local 에서는 미실행
[root@webserver01 ~]$ vi /root/.bashrc 
export LD_LIBRARY_PATH="/usr/local/lib"

[root@webserver01 ~]$ /usr/bin/sqlite3 
SQLite header and source version mismatch
2020-01-27 19:55:54 3bfa9cc97da10598521b342961df8f5f68c7388fa117345eeb516eaa837bb4d6
2013-05-20 00:56:22 118a3b35693b134d56ebd780123b7fd6f1497668
```


## ZSH, Git, Nginx, [NeoVim](https://github.com/neovim/neovim/wiki/Installing-Neovim) (v10.17.1)

다음의 내용을 스크립트에 포함을 한 뒤, 실행 하면 시스템에서 활용되는 기본적 도구들이 자동으로 설치 됩니다.  

zsh 는 설치 후 **[zsh-theme](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes)** 의 변경 및 **Plug In 기능** 을 추가 후 재가동 합니다. 

```r
# Install Git
yum install git

# Install Nginx : 링크서 최신버젼 확인하기
# http://nginx.org/packages/mainline/centos/7/x86_64/RPMS/
yum install -y libxml2-devel libxml2-static libxslt libxslt-devel gd gd-devel
wget http://nginx.org/packages/mainline/centos/7/x86_64/RPMS/nginx-1.17.9-1.el7.ngx.x86_64.rpm 
yum localinstall nginx-1.17.9-1.el7.ngx.x86_64.rpm 

# Neovim (CentOS 7 / RHEL 7)
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum install -y neovim python3-neovim

# Install ZSH
yum -y install zsh
cd ~
chsh -s /bin/zsh root
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"

cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
source ~/.zshrc
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

위 내용으로 설치를 한 뒤, bash shell 에 다음의 내용들을 추가 합니다.

```r
[root@localhost ~]$ vi /root/.bashrc
alias python3="/usr/bin/python3.7"

[root@localhost ~]$ nvim ~/.zshrc
ZSH_THEME="agnoster"      # 테마를 정의한다
export LANG="ko_KR.UTF-8" # 한글 인코딩을 해결
plugins=(
 git
 zsh-autosuggestions
 #zsh-syntax-highlighting
 history-substring-search
)

[root@localhost ~]$ souce .zshrc            # 변경된 설정을 적용
```

## SQLite3

```python
raise ImproperlyConfigured('SQLite 3.8.3 or later is required (found %s).' % Database.sqlite_version)
```

**Django 2.2.9** 이후 부터는 **sqlite 3.8** 이상인지를 확인하는 코드가 포함되어 있습니다. 때문에 기본 버젼인 **sqlite 3.7.17** 에서는 오류를 출력합니다.

```r
Python 3.7.7 (default) 
>>> import _sqlite3
>>> _sqlite3.sqlite_version
'3.7.17'
```

이러한 문제를 해결하는 작업순서는 아래와 같습니다.

1. sqlite3 를 설치를 하고 확인 합니다.
2. .bashrc , .zshrc 등의 실행환경에 설치된 내용을 `export` 로 연결합니다.
3. 파이썬을 실행하여 연결된 내용의 실행을 확인 합니다.

자세한 내용은 sqlite 별도 페이지를 참고 합니다.








우선 Python 에서 설치 및 호출 내용을 확인 합니다.


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








```r
z
export PATH=$HOME/opt/sqlite/bin:$PATH

export PATH=$HOME/opt/sqlite/bin:$PATH
export LD_LIBRARY_PATH=$HOME/opt/sqlite/lib
export LD_RUN_PATH=$HOME/opt/sqlite/lib



export LD_LIBRARY_PATH="/usr/local/lib"
export PATH=$HOME/opt/sqlite/bin:$PATH

export PATH=$HOME/opt/sqlite/bin:$PATH
export LD_LIBRARY_PATH=$HOME/opt/sqlite/lib
export LD_RUN_PATH=$HOME/opt/sqlite/lib

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