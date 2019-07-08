---
layout: post
title:  "Centos7 에서 mysql8.x 설치하기"
author: x2soft
categories: [ tutorial, 리눅스, mysql ]
tags: [centos7, mysql8]
---

개발환경에서 mysql8은 도커기반에서의 설치가 가장 간편합니다. 이번 시간에는 Centos7에서 mysql8.x 를 설치하는 방법을 알아보도록 하겠습니다.

기회가 된다면 도커기반으로 mysql8.x 를 설치하는 방법도 알아보도록 하겠습니다.

## 1. yum Repository 추가 및 설치

```bash
# rpm -Uvh https://repo.mysql.com/mysql57-community-release-el7-11.noarch.rpm
# yum --enablerepo=mysql80-community install -y mysql-community-server
```

## 2. mysql 설정

### 2-1. data 디렉토리 생성

```bash
# mkdir -p /data/mysql
# chown mysql:mysql /data/mysql
```

### 2-2. my.cnf 수정

```bash
# vi /etc/my.cnf
```

```text
[client]
socket=/data/mysql/mysql.sock

## data 디렉토리 변경
datadir=/data/mysql
socket=/data/mysql/mysql.sock
## [mysqld] sectionc에 아래 내용 추가
user = mysql
port = 3306
innodb_dedicated_server=ON
```

## 3. mysql 구동

### 3-1. mysql 실행/중지/재시작

```bash
# systemctl start[stop|restart] mysql
```

### 3-2. 시스템 부팅시 자동 실행

```bash
# systemctl enable mysqld
```

### 3-3. 초기 임시 비밀번호 확인

```bash
# cat /var/log/mysqld.log | grep -i 'temporary password'
2019-07-02T04:23:44.741210Z 5 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: :XKcwd*pv8se
```

### 3-4. 비밀번호 변경

```bash
# mysql_secure_installation
Securing the MySQL server deployment.

Enter password for user root: :XKcwd*pv8se (위에서 확인한 임시 비번)

New password: 새 비밀번호 (root)

Re-enter new password: 비밀번호 확인

Estimated strength of the password: 100 

Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y    ## 변경 된 비밀번호를 계속 사용하시겠습니까?

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y  ## 익명사용자를 삭제 할까요?

Success.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y  ##원격 root 로그인 차단?

Success.

Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y  ## 테스트 데이터베이스 삭제?

 - Dropping test database...

Success.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y  ##관리자 권한 테이블 다시 로드할까요?

Success.
```

### 3-5. mysql 접속

```bash
# mysql -uroot -p
```

### 3-6. mysql 패스워드 정책 설정

```bash
mysql> show variables like 'validate_password%';
+--------------------------------------+-------+
| Variable_name                        | Value |
+--------------------------------------+-------+
| validate_password.check_user_name    | ON    |
| validate_password.dictionary_file    |       |
| validate_password.length             | 8     |
| validate_password.mixed_case_count   | 1     |
| validate_password.number_count       | 1     |
| validate_password.policy             | LOW   |
| validate_password.special_char_count | 1     |
+--------------------------------------+-------+
7 rows in set (0.00 sec)

## 패스워드 policy를 변경하고자 하면 아래와 같이 설정 함. 
mysql> SET GLOBAL validate_password.policy=LOW;

## 위와 같이 설정하면 mysql 재시작 될 때 다시 기본 정책으로 변경 됨으로 my.cnf 에 아래와 같이 추가 하면 재시작시에도 설정값으로 시작됨. 
[root@x2 ~]# vi /etc/my.cnf
validate_password_policy=LOW            ## 추가

## mysql DB, 계정 추가
mysql> ALTER USER "root"@"localhost" IDENTIFIED BY "examples!@#$", "root"@"localhost" PASSWORD EXPIRE NEVER;
mysql> CREATE DATABASE project DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
mysql> CREATE USER "test_project"@"%" IDENTIFIED WITH mysql_native_password BY "examples!@#$";
mysql> GRANT ALL ON project.* TO "test_project"@"%";
mysql> FLUSH PRIVILEGES;
```