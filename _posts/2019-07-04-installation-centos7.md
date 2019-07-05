---
layout: post
title:  "[STEP BY STEP]Centos 7 설치하기"
categories: [ tutorial, 리눅스 ]
image: assets/post_images/20190704/install-centos-7-logo.png
tags: [centos7, sticky, featured]
---

사실 리눅스를 설치할 일은 그다지 많지 않은것이 현실입니다. 물론 시스템 관리자 입장에서는 자주 있을 수 있는 일지만, 개발자들은 그렇지 않은 경우가 많죠. 하지만, 막상 필요에 의해 설치하는 경우에는 생각이 나지 않아 그때 그때 구글링에 의존해야 하는 경우도 존재 합니다. 그래서, CentOS7 을 설치하는 전 과정을 Step By Step 형식으로 정리해 보았습니다. 

## 설치전 준비물

1. Centos7 이미지 파일

    [CentOS7 다운로드](http://isoredirect.centos.org/centos/7/isos/x86_64/CentOS-7-x86_64-DVD-1810.iso)

2. iso 파일을 USB 메모리에 저장하기 위한 UTIL 설치

    [win32diskimager 다운로드](http://sourceforge.net/projects/win32diskimager/)

    ![설정]({{ site.baseurl }}/assets/post_images/20190704/win32diskimager.png)

## 설치

### Install Centos 7 선택

![Install CentOS 7 선택]({{ site.baseurl }}/assets/post_images/20190704/2.png)

### 설치 과정에서 사용할 언어 선택

![언어 선택]({{ site.baseurl }}/assets/post_images/20190704/3.png)

### 소프트웨어 선택(S)

![소프트웨어 선택]({{ site.baseurl }}/assets/post_images/20190704/4.png)

### 기본환경 선택

필요한 패키지들을 별도로 설치하기 위해 최소 설치를 선택합니다.

![ 선택]({{ site.baseurl }}/assets/post_images/20190704/5.png)

### 설치대상 선택

자동 파티션 설정을 선택합니다.

![설치대상선택]({{ site.baseurl }}/assets/post_images/20190704/6.png)

### 디스크 선택 및 파티션 설정

![디스크 선택 및 파티션 설정]({{ site.baseurl }}/assets/post_images/20190704/7.png)

파티션별 설정은 아래와 같이 진행하도록 하겠습니다.

- /boot - 500M ~ 1G
- swap - 메모리크기의 2배 정도
- / - 20G
- /data - 남은 용량 모두 사용

### /boot 파티션 생성

![]({{ site.baseurl }}/assets/post_images/20190704/8.png)
![]({{ site.baseurl }}/assets/post_images/20190704/9.png)
![]({{ site.baseurl }}/assets/post_images/20190704/10.png)

### swap 파티션 생성

![]({{ site.baseurl }}/assets/post_images/20190704/11.png)
![]({{ site.baseurl }}/assets/post_images/20190704/12.png)

### / 파티션 생성

파티션을 생성하기에 앞서 장치 유형과 파일 시스템에 대해서 파악후 적절한 선택을 할 수 있도록 합니다.

#### LVM

- 논리적 스토리지를 생성하여 물리적 디스크 추가, 조정 등의 유연한 관리 및 확장에 용이

#### xfs

> - 900만 TB 까지 지원이 가능(32비트는 16TB)
> - 마운트된 상태에서도 파일시스템 확장이 가능
> - 포맷시간이 매우 빠름
> - 파일시스템 체크시 매우 빠른 속도를 낼수 있음


> - 대용량 위주의 파일시스템에 최적화되어 작은 사이즈의 파일에선 되려 속도 저하가 발생
> - 파일 시스템 문제 발생시 커널 메시지가 나오지 않을 가능성이 존재
> - 디버그 옵션이 없어 삭제된 파일은 복구 불가능
> - 파일시스템 복구시 2TB 당 1GB 의 물리 메모리 필요
> - 별도의 데몬에서 파일 시스템을 관리

![]({{ site.baseurl }}/assets/post_images/20190704/13.png)

### /data 파티션 생성

별도의 home 파티션은 생성하지 않습니다. 상황에 따라 적절하게 생성해주시면 됩니다.

![]({{ site.baseurl }}/assets/post_images/20190704/14.png)
![]({{ site.baseurl }}/assets/post_images/20190704/15.png)

### 변경 사항 저장

![]({{ site.baseurl }}/assets/post_images/20190704/16.png)

### 네트웍 관련 설정

![]({{ site.baseurl }}/assets/post_images/20190704/17.png)
![]({{ site.baseurl }}/assets/post_images/20190704/18.png)
![]({{ site.baseurl }}/assets/post_images/20190704/19.png)
![]({{ site.baseurl }}/assets/post_images/20190704/20.png)

## 설치 시작

![]({{ site.baseurl }}/assets/post_images/20190704/21.png)
![]({{ site.baseurl }}/assets/post_images/20190704/22.png)
![]({{ site.baseurl }}/assets/post_images/20190704/23.png)
![]({{ site.baseurl }}/assets/post_images/20190704/25.png)