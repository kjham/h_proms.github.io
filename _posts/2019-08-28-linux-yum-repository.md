---
layout: post
title: 'Centos Yum Repository 추가'
author: kjham.ham
date: 2019-08-28 11:00
tags: [swtech,linux,yum]
comments: true
---

폐쇄 개발망을 이용하다보면 yml이나 apt-get을 정상적으로 이용할 수 없습니다.  
개인적으로 구성한 yml repository 접속하여 원하는 패키지를 설치하는 절차를 살펴봅니다.  

### yum repository 목록 확인  
`yml repolist`  

### yml repository 파일 위치  
`cd /etc/yml.repos.d`  

### repo 추가 또는 수정  
수정 : `vi CentOS-Base.repo`  
추가 : `vi 이름정의.repo`  

### 내용 수정 또는 추가
~~~sh
[저장소 이름]
name=저장소 표시 이름
baseurl=저장소 주소
enabled=활성화 여부 (0 or 1)
gpgcheck=gpg 서명키 사용 여부 (0 or 1)
gpgcheck=서명키를 사용한다면 서명키 주소 입력
~~~

### 내용 확인
`cat CentOS-Base.repo`  
~~~sh
name=KJHAM Yum Repository
mirrorlist=http://192.168.100.1/centos/7.5.1804/os/x86_64/
baseurl=http://192.168.100.1/centos/7.5.1804/os/x86_64/
gpgcheck=0
enabled=1
~~~


### 참고 사항  
- OS 버전 확인  
`cat /etc/redhat-release`  