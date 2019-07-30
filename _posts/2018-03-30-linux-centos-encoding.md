---
layout: post
current: post
navigation: True
title: (Linux:Centos7) 한글깨짐현상 수정
date: 2018-03-30 10:03:00
tags: [sw-dev-lab]
class: post-template
subclass: 'post tag-sw-dev-lab'
author: proms
---

**Linux Centos7 - 한글깨짐**

1. 현재 설정된 인코딩 설정 확인
[root@localhost etc]$ echo $LANG
ko_KR.UTF-8


2. 인코딩 설정 해제
[root@localhost sysconfig]# unset LANG
[root@localhost sysconfig]# LANG=C

3. 사용 가능한 인코딩 설정 확인
[root@localhost etc]$ locale -a | grep ko
ko_KR
ko_KR.euckr
ko_KR.utf8
korean
korean.euc
ru_RU.koi8r
ru_UA.koi8u
tg_TJ.koi8t
uk_UA.koi8u

4. /etc/sysconfig/i18n 파일 수정
1) centos 7 이전
[root@localhost sysconfig]# vi i18n 
#LANG="ko_KR.UTF-8"
LANG="ko_KR.euckr"

2) centos 7 이후
/etc/locale.config 를 수정

출처 : http://egloos.zum.com/abctank/v/9215895