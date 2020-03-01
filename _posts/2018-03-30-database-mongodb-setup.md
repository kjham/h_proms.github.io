---
layout: post
title: 'MongoDB : Linux 설치 가이드'
author: kjham.ham
date: 2018-03-30 14:42
tags: [swtech,nosql,mongodb]
image: /files/covers/blog.jpg
comments: true
---

**MongoDB 설치 가이드**

1. tar 다운로드
https://www.mongodb.com/download-center#atlas
위 주소로 접속하여, 최신버전 다운로드

2. linux 서버로 파일 복사

3. 압축 풀기
tar xzf mongodb-linux-x86_64-rhel70-3.4.2.gz

4. 데이터 저장용 폴더 생성
mkdir -p /data/db

5. 서버 시작  
/[압축푼 경로]/mongodb-linux-x86_64-rhel70-3.4.2/bin/mongod --port 27017 --dbpath /data/db  
인증을 주고자 할 경우는  
/[압축푼 경로]/mongodb-linux-x86_64-rhel70-3.4.2/bin/mongod --port 27017 --dbpath /data/db -- auth
백그라운드로 실행할 경우는
/[압축푼 경로]/mongodb-linux-x86_64-rhel70-3.4.2/bin/mongod --port 27017 --dbpath /data/db & 

6. 서버로 접속
또 다른 linux 세션을 접속 후..
/[압축푼 경로]/mongodb-linux-x86_64-rhel70-3.4.2/bin/mongo --port 27017