---
layout: post
title: '(Linux:Centos7) IP 고정방법'
author: kjham.ham
date: 2018-03-30 11:26
tags: [swtech,linux]
image: /files/covers/blog.jpg
---

**Linux Centos7 - IP 고정방법**

네트워크 설정 파일은 /etc/sysconfig/network-scripts/ifcfg-enp0s3입니다.(파일 이름은 다를 수도 있습니다.) 설정 파일을 텍스트 에디터로 엽니다.

>TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="enp0s3"
UUID="91af51db-7cf0-4069-9433-77d356b31bca"
DEVICE="enp0s3"
ONBOOT="yes"

BOOTPROTO="static"
IPADDR="192.168.0.123"
GATEWAY="192.168.0.1"
DNS1="168.126.63.1"
DNS2="168.126.63.2"

네트워크를 재시작합니다.
# systemctl restart network

출처 : https://www.manualfactory.net/10004