---
layout: post
title: 'MongoDB Backup & Restore'
author: kjham.ham
date: 2019-12-17 16:10
tags: [swtech,java,database,mongodb,nosql]
##image: /files/covers/effectivejava.jpg
---

업무 중 MongoDB의 Database를 복제해야 하는 일이 생겼습니다.  
그래서 다음과 절차를 갖고 진행했습니다.  

MongoDB 에서는 databaseCopy 라는 명령어를 제공해줍니다.  

복제해야 할 Database 로 접속합니다.  
`use DB명`  

그리고 다음 명령어로 복제를 수행합니다.  
`db.copyDatabase('원본DB명' , '복제DB명')`  

그러나, Sharding 으로 구성하였을 경우 위 명령어로는 복제를 할 수 없습니다.  
따라서 `mongodump` 와 `mongorestore`를 이용해야 합니다.  

우선, dump 파일을 생성하겠습니다.  
mongodump 실행 포맷  
`mongodump --out /파일경로/파일명 --port 포트번호 -u관리자계정 -p관리자암호 --db 기존DB명 --authenticationDatabase admin`  
mongodump 실행 예시  
`mongodump --out /home/backup/mongodb/test_20191216.dump --port 50000 -uadmin -pPassword1! --db olddb --authenticationDatabase admin`  

그리고 복원을 수행합니다.  
mongorestore 실행 포맷  
`mongorestore --port 포트번호 -u관리자계정 -p관리자암호 --db 신규DB명 --authenticationDatabase admin /home/backup/summary`  
mongorestore 실행 예시  
`mongorestore --port 50000 -uadmin -pPassword1! --db newdb  --authenticationDatabase admin /home/backup/mongodb`

정상적으로 성공했음을 확인합니다.