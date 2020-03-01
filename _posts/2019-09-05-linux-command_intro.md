---
layout: post
title: 'Linux Command 정리'
author: kjham.ham
date: 2019-09-19 13:41
createdate: 2019-09-05 09:47
tags: [swtech,linux]
comments: true
---

+ 도메인 편집
`vi /etc/hosts`  

+ 방화벽 관련
`service iptables stop`  
`service firewalld stop`  

+ 이더넷 드라이버 관련  
`/etc/sysconfig/network-scripts/`

+ 특정 확장자 파일 제거  
`find /work/dir -name "*.tmp" -exec rm -rf {} \;`  

+ 특정 확장자 파일 이동  
`find /work/dir -name "*.tmp" -exec cp {} /des/dir \;`

+ 특정 확장자 파일 복사  
`find /work/dir -name "*.tmp" -exec mv {} /des/dir \;`
