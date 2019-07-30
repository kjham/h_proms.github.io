---
layout: post
current: post
navigation: True
title: (Linux:Centos7) Booting Mode 변경
date: 2018-03-30 11:14:00
tags: [sw-dev-lab]
class: post-template
subclass: 'post tag-sw-dev-lab'
author: proms
---

**Linux Centos7 -Booting Mode 변경**

[Centos 7] 부팅모드 변경 방법​​

이전 버전과 다르게 CentOS 7 에서는
/etc/inittab 에 들어가도 부팅 순서 변경이 보이질 않는다.

여기서는 systemctl을 이용한 변경을 해야 한다.

현재의 런레벨을 확인하는 방법

# runlevel
# who -r


런레벨 3(text mode)으로 변경 

# systemctl set-default multi-user.target
# systemctl get-default
# reboot 

런레벨 5(graphic mode)으로 변경

# systemctl set-default graphical.target
# systemctl get-default
# reboot

바꿀 수 있는 런레벨 출력

# systemctl list-units --type=target

출처 : https://m.blog.naver.com/PostView.nhn?blogId=nels1004&logNo=220252554151&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F