---
layout: post
title:  "id와 패스워드 없이 GIT 이용하기(feat bitbucket)"
author: x2soft
categories: [ tip, git ]
tags: [ssh, bitbucket]
---

git으로 부터 clone 이나 fetch 등의 작업을 할 경우 id와 패스워드의 credential 정보가 필요합니다. 물론, 한번의 인증 후 인증 정보를 캐시하여
다음 이용할때 부터는 해당 인증 정보를 입력하지 않아도 가능하도록 할 수 있습니다.

여기서는 공개키와 개인키를 이용하여 git 인증을 대신할 수 있는 방법을 알아보도록 하겠습니다.

몇 가지의 파일만 관리를 하면 배포과정에서 발생하는 인증정보 입력을 없애 자동화 과정에 응용할 수 있습니다.

## 1. 예상되는 몇 가지 상황

### 1-1. id와 비밀번호를 묻는 프로프트가 나타난다.

```bash
$ git clone https://example.com/scm/project/webapp.git webapp_test
Cloning into 'webapp_test'...
Username for 'https://example.com': john.doe
Password for 'https://john.doe@example.com':
```

### 1-2. git protocol 은 ssh로 사용하고 싶다.

```text
ssh://git@example.com:7999/project/webapp.git
```

### 1-3. ssh 접속시 verification prompt 가 나타난다(1회).

```bash
$ git clone ssh://git@example.com:7999/project/webapp.git
Cloning into 'webapp'...
The authenticity of host '[example.com]:7999 ([xxx.xxx.xxx.xxx]:7999)' can't be established.
RSA key fingerprint is xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
RSA key fingerprint is xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
Are you sure you want to continue connecting (yes/no)?
```

## 2. SSH 개인키와 공개키 준비

### 2-1. 타겟 서버에서 키 생성하기

```bash
$ ssh-keygen                                                                                                    
Generating public/private rsa key pair.
Enter file in which to save the key (/home/johndoe/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/johndoe/.ssh/id_rsa.
Your public key has been saved in /home/johndoe/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxx johndoe@localhost.localdomain
The key's randomart image is:
+---[RSA 2048]----+
|      +*O*.o..o. |
|       *=o=.+oo  |
|    . o..+o=.+ o |
|     +  .o* o o  |
|      . SB..     |
|      E Oo+      |
|       ..B .     |
|     .o o        |
|    .o.o         |
+----[SHA256]-----+

$ ls -alh .ssh/
total 24K
drwxr--r--  2 influence influence 4.0K Jul  3 14:31 .
drwx--x--- 11 influence influence 4.0K Jul  3 08:27 ..
-rw-------  1 influence influence 1.7K Jul  3 08:23 id_rsa
-rw-r--r--  1 influence influence  413 Jul  3 08:23 id_rsa.pub
```

### 2-2. Public 키를 GIT(Bitbucket) Access Keys에 추가

BitBucket > Repository > Repository settings > Access Keys 에 위에서 생성한 id_rsa.pub 파일(Public Key)의 내용을 추가 한다.

## 3. 타겟 서버 ssh 설정

### 3-1. /home/username/.ssh/config 파일 수정(만들기)

```text
Host webapp                                                                                                                      
    Hostname example.com                                                                                                              
    IdentityFile ~/.ssh/id_rsa                                                                                                            
    IdentitiesOnly yes                                                                                                                    
    StrictHostKeyChecking no
```

 - webapp : 실제 git 도메인의 alias이다.
- IdentityFile : Private Key 파일의 위치다(위에서 만든 Private Key).
- StrictHostKeyChecking : host verifycation을 하지 않는다.

config 파일의 권한을 수정한다.

```bash
$ chmod 600 config
```

## 4. git clone 하기

git 리파지터리의 프로토콜을 ssh 바꾼후 해당하는 URL 정보를 확인한다.

필요하다면, 해당 포트(7999)도 방화벽에서 오픈하도록 한다.

```bash
$ git clone ssh://git@webapp:7999/project/webapp.git
Cloning into 'webapp'...
Warning: Permanently added '[example.com]:7999,[xxx.xxx.xxx.xxx]:7999' (RSA) to the list of known hosts.
remote: Enumerating objects: 6920, done.                                                                                                    
remote: Counting objects: 100% (6920/6920), done.                                                                                           
remote: Compressing objects: 100% (3996/3996), done.                                                                                        
remote: Total 6920 (delta 1977), reused 6629 (delta 1865)                                                                                   
Receiving objects: 100% (6920/6920), 7.67 MiB | 5.42 MiB/s, done.
Resolving deltas: 100% (1977/1977), done.
```

