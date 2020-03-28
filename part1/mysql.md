---
description: Cent OS 7.7 에서 MySQL 7.7 설치 및 설정 내용을 정리 합니다.
---

<figure class="align-center">
  <img src="{{site.baseurl}}/assets/images/os/mysql_logo.jpg">
  <figcaption>MySQL Logo</figcaption>
</figure>


# MySQL

가장 보편적인 **MySQL** 을 설치하고 작업합니다.

## MyCLI

MySQL 의 작업을 돕는 IDE 편집기를 설치하고 설정값을 추가 합니다.

```r
[root@localhost ~]$ pip3.7 install mycli

[root@localhost ~]$ nvim ~/.myclirc
# Screenshots at http://mycli.net/syntax
syntax_style = monokai
# disabled pager on startup
enable_pager = False
```

## Install MySQL

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

## Reset Password

**설치시 정의된 초기 암호를** 사용하여 로그인을 합니다.

```r
[root@localhost ~]$ grep 'password' /var/log/mysqld.log 
2018-02-13T14:49:45.720116Z 1 [Note] A temporary password is generated for root@localhost: tur++-dvf7tI
```

Log 값이 기록된 `/var/log/mysqld.log` 파일에서 `$ grap` 명령을 사용하여 **임시 password** 내용을 확인 합니다. 위 명령 내용을 보면 초기 비밀번호는 `tur++-dvf7tI` 입니다.

```r
[root@localhost ~]$ mysql -uroot -p
Enter password: 
Your MySQL connection id is 30
Server version: 5.7.29 MySQL Community Server (GPL)
```

초기 password 를 사용자가 원하는 내용으로 변경 합니다.

```r
mysql> show databases;
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.

mysql> SET PASSWORD = PASSWORD('Python=2020@@');
mysql> flush privileges;
```

비밀번호 변경시 [정책](https://kamang-it.tistory.com/entry/MySQL%ED%8C%A8%EC%8A%A4%EC%9B%8C%EB%93%9C-%EC%A0%95%EC%B1%85-%ED%99%95%EC%9D%B8-%EB%B3%80%EA%B2%BD%ED%95%98%EA%B8%B0) 의 내용을 준수해야 합니다. 규정 내용을 살펴보는 방법은 다음과 같습니다.

<figure class="align-center">
  <img src="{{site.baseurl}}/assets/images/os/mysql_valid.png">
  <figcaption></figcaption>
</figure>

```r
mysql> SET GLOBAL validate_password_length = <원하는 길이>;
select password('<테스트할 패스워드>');
```

## Add User

초기값은 localhost 에서만 접속이 가능합니다. 외부에서도 연결 되기 위해서는 사용자를 다음과 같이 추가해야 합니다.

```r
# 외부에서 접속 가능한 '%' 계정을 추가 합니다
mysql > grant all privileges on *.* to '계정'@'%' identified by '비밀번호'; 
mysql > flush privileges;

# '%' 가 등록되어 있는지를 확인 합니다
mysql > SELECT host, user, plugin, authentication_string FROM user;
+-----------+------+-----------------------+-------------------------+
| Host      | User | plugin                | authentication_string   |
+-----------+------+-----------------------+-------------------------+
| localhost | root | mysql_native_password | *8024A6913C57B924E6803A |
| %         | root | mysql_native_password | *8024A6913C57B924E6803A |
+-----------+------+-----------------------+-------------------------+
mysql > exit;

[root@localhost ~]$ systemctl restart mysqld
[root@localhost ~]$ mycli -h python.org --ssh-port 3306 -u root
```

## Port Setting
추후 추가예정 입니다.