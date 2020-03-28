---
description: Cent OS 7.7 에서 Terminal Setting 을 정리 합니다.
---

# [Cent OS](https://yongbeomkim.github.io/linux/centos-setting/) Setting

1. Python, Nginx, Nvim 등 도구설치
2. MySQL, MariaDB 설치


## Install Python3

<figure class="align-center">
  <img src="{{site.baseurl}}/assets/images/restapi.png">
  <figcaption>웹 브라우저에서 구현되는 REST API</figcaption>
</figure>


https://javafa.gitbooks.io/python-django/content/assets/chapter1_browser.png

가장 활용도가 높은 Python 3 을 Cent OS 에서 정의된 기본 버젼을 설치합니다.

```r
# Install Python 3.6
yum -y install centos-release-scl
yum -y install rh-python36
. /opt/rh/rh-python36/enable # or scl enable rh-python36 bash [if interactive]
# yum install -y python36u
# yum install -y python36u-pip
```

위 방법은 사용자가 원하는 버젼이 설치되지 않는다는 단점이 있습니다. 바로 다음에서  [특정버젼](https://computingforgeeks.com/how-to-install-python-on-3-on-centos/) 을 설치하는 방법은 아래의 내용으로 설치하면 됩니다. 아래의 내용 중 `https://www.python.org/ftp/python/3.7.7/Python-3.7.7.tgz` 부분은 [Python 사이트](https://www.python.org/downloads/) 에서 사용자가 원하는 버젼의 내용으로 바꿔서 실행 합니다.

```r
# 설치 과정에 필요한 의존성 문제를 해결하는 프로그램들 설치 
yum groupinstall -y "Developent Tools"
yum -y install openssl-devel bzip2-devel libffi-devel

wget https://www.python.org/ftp/python/3.7.7/Python-3.7.7.tgz
tar xzf Python-3.7.7.tar.xz
cd Python-3.7.7
./configure --enable-optimizations
make altinstall
```

## [ZSH](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes), Git, Nginx, Node.js, [NeoVim](https://github.com/neovim/neovim/wiki/Installing-Neovim) (v10.17.1)

다음의 내용을 스크립트에 포함을 한 뒤, 실행 하면 시스템에서 활용되는 기본적 도구들이 자동으로 설치 됩니다.  

```r
# Install Git
yum install git

# Install Nginx
yum install -y libxml2-devel libxml2-static libxslt libxslt-devel gd gd-devel
wget http://nginx.org/packages/mainline/centos/7/x86_64/RPMS/nginx-1.17.6-1.el7.ngx.x86_64.rpm
yum localinstall nginx-1.17.6-1.el7.ngx.x86_64.rpm

# Install Node.js
curl -sL https://rpm.nodesource.com/setup_10.x | sudo bash -
yum install nodejs

# Install SQlite 3.8
wget http://www6.atomicorp.com/channels/atomic/centos/7/x86_64/RPMS/atomic-sqlite-sqlite-3.8.5-3.el7.art.x86_64.rpm
yum localinstall atomic-sqlite-sqlite-3.8.5-3.el7.art.x86_64.rpm
mv /lib64/libsqlite3.so.0.8.6{,-3.17}
cp /opt/atomic/atomic-sqlite/root/usr/lib64/libsqlite3.so.0.8.6 /lib64

# Install ZSH
yum -y install zsh
cd ~
chsh -s /bin/zsh root
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"

cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
source ~/.zshrc
git clone https://github.com/zsh-users/zsh-autosuggestions

# Neovim (CentOS 7 / RHEL 7)
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum install -y neovim python3-neovim
```

위 내용으로 설치를 한 뒤, bash shell 에 다음의 내용들을 추가 합니다.

```r
$ vi /root/.bashrc
  alias python3="/usr/bin/python3.6"

$ nvim ~/.zshrc
  ZSH_THEME="agnoster"      # 테마를 정의한다
  export LANG="ko_KR.UTF-8" # 한글 인코딩을 해결
  plugins=(
    git
    zsh-autosuggestions
    #zsh-syntax-highlighting
    history-substring-search
  )

$ souce .zshrc            # 변경된 설정을 적용
```

## MySQL

올해는 가장 보편적인 **MySQL** 을 설치 합니다.

```r
rpm -ivh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
yum search mysql-community
yum install mysql-community-server
systemctl start mysqld              # 서버 활성화
systemctl enable mysqld             # 시스템 부팅시 시작 활성화
systemctl restart mysqld
echo 'validate_password_policy=LOW' >> /etc/my.cnf # 보안정책 완화
echo 'default_password_lifetime=0' >> /etc/my.cnf
```

설치 과정중 출력된 Log 값들은 `/var/log/mysqld.log` 파일에 저장 됩니다  

```r
[root@localhost ~]$ grep 'password' /var/log/mysqld.log 
2018-02-13T14:49:45.720116Z 1 [Note] A temporary password is generated for root@localhost: tur++-dvf7tI

[root@localhost ~]$ mysql -uroot -p
Enter password: 
```




**참고사이트**

1. 
2. 



**GenericView** 를 활용하면, 간결한 코딩과 더불어 안적적인 구조를 갖는 웹서비스를 구축할 수 있습니다.

이번에는 상황별 다양한 메세지를 처리하는 Message를 객체에 대해서 정리를 하겠습니다,

### settings.py

**settings.py** 에 다음의 설정내용을 추가합니다.
```python
# messages setting
from django.contrib.messages import constants as message_constants
from django.contrib.messages import constants as messages

MESSAGE_LEVEL = message_constants.DEBUG
# redirect 이후에도 메시지가 남음
MESSAGE_STORAGE = 'django.contrib.messages.storage.cookie.CookieStorage'
# MESSAGE_STORAGE = 'django.contrib.messages.storage.session.SessionStorage'

# 상황 메세지별 적용할 class : BootStrap 등을 참고
MESSAGE_TAGS = {
    messages.DEBUG: 'alert-info',
    messages.INFO: 'alert-info',
    messages.SUCCESS: 'alert-success',
    messages.WARNING: 'alert-warning',
    messages.ERROR: 'alert-danger',
}
```

### views.py
**_messages_** 는 개별 메세지 객체를 다루고, **_SuccessMessageMixin_** 는 **GenericView** 객체에서 메세지를 다룹니다. request 함수에서는 **messages** 객체를 활용하고, GenericView 에서는 **success_message** 변수에 메세지를 정의 합니다.

```python
from django.contrib import messages
from django.contrib.messages.views import SuccessMessageMixin

def create(request):
    data = request.POST.get('data', None)
    data = Books(date = request.POST['date'])    
    data.save()

    except DatabaseError as e:
        messages.warning(request, 'Account expired')
        messages.error(request, '데이터 오류')
        return redirect(reverse("list"), {'error':e})
    
    messages.info(request, 'remain in account.')
    messages.success(request, '데이터 저장')
    return redirect('list')


class Updateview(SuccessMessageMixin, UpdateView):
    model = Cafeteria
    success_message = '내용이 변경 되었습니다'

class Deleteview(DeleteView):
    model = Cafeteria
    success_url = reverse_lazy("menus:list")

    # 아래를 추가하면 confirm 템플릿 없이 진행
    def get(self, request, *args, **kwargs):
        return self.post(request, *args, **kwargs)
```

### urls.py
```python
urlpatterns = [
    path('', Listview.as_view(), name="list"),
    path('create', create, name="create"),
    path('update/<int:pk>', Updateview.as_view(), name='edit'),
    path('delete/<int:pk>', Deleteview.as_view(), name='delete'),
]
```

### Templates
템플릿에게 **messages** 객체를 사용하여 메세지를 전달합니다. 해당 객체에 내용이 저장되면 템플릿에서는 이를 활용하여 메세지 출력 형식을 정의합니다.

{% raw %}
```html
{% if messages %}
    {% for m in messages %}
    <div {% if m.tags %} class="alert {{m.tags}} fade show" {% endif %}>
        <strong><li>{{m}}</li></strong>
    </div> 
    {% endfor %}
{% endif %}
```
{% endraw %}


## 섹션 1

마크다운은 당신의 책을 가장 잘 표현할수 있습니다.  **book's structure**

## 섹션 2
