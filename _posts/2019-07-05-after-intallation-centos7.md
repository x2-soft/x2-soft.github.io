---
layout: post
title:  "Centos 7 설치후 기본설정"
author: jane
categories: [ tutorial, 리눅스 ]
image: assets/images/1.jpg
tags: [centos7, yum, selinux, firewalld]
---

지난 시간에는 CentOS 7을 설치하는 과정을 알아 보았습니다. 이번 시간에는 앞서 설치된 CentOS7를 개발환경에서 사용하기 위해 필요한 설정 작업을 알아보도록 하겠습니다.

보안과 관련된 selinux와 firewalld 는 참고 정도로만 봐주셨으면 합니다.

참고로 아래의 과정은 모두 root 로 진행합니다.

## yum 업데이트

업데이트가 필요한 패키지들을 업데이트하기 위해 필요합니다.

```bash
# yum update -y
```

## selinux 해제하기

```bash
# vi /etc/selinux/config
```
SELIUNX=enforcing을 SELINUX=disable 로 수정
![]({{ site.baseurl }}/assets/post_images/20190704/26.png)

## firewalld 해제 및 명령어 history logging

방화벽 해제

```bash
# systemctl stop firewalld
```

명령어 history logging

```bash
# vi /etc/profile.d/cmd.sh
```

cmd.sh 파일에 아래의 내용 추가

```bash
function history_to_syslog
{
  declare cmd
  who=$(whoami)
  cmd=$(history 1)
  TTY=`tty`
  HISNAME="`basename $TTY`" 
  ip=`who |grep pts/${HISNAME} |cut -f 2 -d \(|cut -f 1 -d \)`
logger -p local7.notice -- IP=$ip USER=$who, PID=$$, PWD=$PWD, CMD=$cmd
}
trap history_to_syslog DEBUG || EXIT
HISTSIZE=10000
HISTFILESIZE=1000000
HISTTIMEFORMAT="%F %T " 
export PATH USER LOGNAME MAIL HOSTNAME HISTSIZE HISTFILESIZE HISTTIMEFORMAT INPUTRC
declare -r HISTFILE
```

환경변수 등록 및 갱신

```bash
# source /etc/profile.d/cmd.sh
```

syslog 파일 설정

```bash
# vi /etc/rsyslog.conf
```

local7.notice    /var/log/cmdlog # 추가

![]({{ site.baseurl }}/assets/post_images/20190704/27.png)

syslog 재시작

```bash
# systemctl stop rsyslog.service
# systemctl start rsyslog.service
```

확인

![]({{ site.baseurl }}/assets/post_images/20190704/28.png)

## 네트워크 설정 확인

```bash
# vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

![]({{ site.baseurl }}/assets/post_images/20190704/29.png)

## 기타 필요 패키지 설치

```bash
## ifconfig, netstat, arp, route 등의 명령어를 사용하기 위한 패키지
# yum install -y net-tool                            

## 네트워크 tui 환경으로 설정할 수 있게 하는 패키지
# yum -y install NetworkManager-tui       

## 서비스 데몬 관리 tui  환경으로 설정할 수 있게 하는 패키지
# yum install -y ntsysv    

## 그 외 base setting
# yum install -y epel-release \
   && rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm \
   && yum install -y sudo cmake wget ncurses-devel gcc* openssl openssl-devel nss-tools rsync           
```

## 서버 부팅시 필요한 서비스만 구동되도록 설정

```bash
# ntsysv
```

![]({{ site.baseurl }}/assets/post_images/20190704/30.png)