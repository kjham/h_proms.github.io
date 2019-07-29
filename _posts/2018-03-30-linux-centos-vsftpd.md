---
layout: post
current: post
navigation: True
title: (Linux:Centos7) FTP 접속 오류
date: 2018-03-30 11:13:00
tags: [sw-dev-lab]
class: post-template
subclass: 'post tag-sw-dev-lab'
author: proms
---

**Linux Centos7 - FTP 접속**

Centos7 를 설치할 때, FTP 를 옵션으로 선택하였음에도.. 접속이 안되는 경우가 있습니다.

1. systemctl 명령어로 vsftpd를 시작/중지/상태확인합니다.
# systemctl status vsftpd.service <– vsftpd 상태확인
# systemctl start vsftpd.service <– vsftpd 시작
# systemctl stop vsftpd.service <– vsftpd 중지
# systemctl restart vsftpd.service <– vsftpd 재시작

2. 방화벽으로 인해 접속이 안되는 경우가 있습니다.
— firewalld 실행 상태 확인
# systemctl status firewalld.service
— firewalld 중지
# systemctl stop firewalld.service
— firewalld 자동중지(시스템시작시 자동시작 안함)
# systemctl disable firewalld.service

3. root 계정으로 접속이 안될 떄
# vi /etc/vsftpd/ftpusers
# vi /etc/vsftpd/user_list

위 두 곳에서 root 제거

vsftpd 재실행
# systemctl stop vsftpd.service
# systemctl start vsftpd.service

출처 : http://ellehu.com/linux/4333