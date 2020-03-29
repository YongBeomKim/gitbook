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

## ZSH, Git, Nginx, [NeoVim](https://github.com/neovim/neovim/wiki/Installing-Neovim) (v10.17.1)

다음의 내용을 스크립트에 포함을 한 뒤, 실행 하면 시스템에서 활용되는 기본적 도구들이 자동으로 설치 됩니다.  

zsh 는 설치 후 **[zsh-theme](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes)** 의 변경 및 **Plug In 기능** 을 추가 후 재가동 합니다. 

```r
# Install Git
yum install git

# Install Nginx
yum install -y libxml2-devel libxml2-static libxslt libxslt-devel gd gd-devel
wget http://nginx.org/packages/mainline/centos/7/x86_64/RPMS/nginx-1.17.6-1.el7.ngx.x86_64.rpm
yum localinstall nginx-1.17.6-1.el7.ngx.x86_64.rpm

# Install SQlite 3.8
wget http://www6.atomicorp.com/channels/atomic/centos/7/x86_64/RPMS/atomic-sqlite-sqlite-3.8.5-3.el7.art.x86_64.rpm
yum localinstall atomic-sqlite-sqlite-3.8.5-3.el7.art.x86_64.rpm
mv /lib64/libsqlite3.so.0.8.6{,-3.17}
cp /opt/atomic/atomic-sqlite/root/usr/lib64/libsqlite3.so.0.8.6 /lib64

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
alias python3="/usr/bin/python3.6"

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

sqlite3 설치된 내용을 확인하면 기본 설치된 버젼이 **3.7.17** 입니다. **Django 3** 를 실행하려먼 **sqlite 3.8** 이상의 버젼을 필요로 하는 오류를 출력합니다.

```python
raise ImproperlyConfigured('SQLite 3.8.3 or later is required (found %s).' % Database.sqlite_version)
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

sqlite3 --version
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