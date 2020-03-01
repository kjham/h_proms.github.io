---
layout: post
title: 'MongoDB : ReplicaSet'
author: kjham.ham
date: 2019-08-30 13:36
tags: [swtech,nosql,mongodb]
image: /files/covers/blog.jpg
comments: true
---

**New NoSQL & mongoDB - Replica & ReplicaSets**

### Master & Slave 서버  
빅 데이터의 안정한 저장과 관리 그리고 복구가 수행되기 위해서는 적절한 백업 솔루션이 필요합니다.  
리프리카와 리프리카 셋 기능은 빅데이터의 백업을 통해 안정성을 보장하기 위한 솔루션 중 하나 입니다.

리프리카 기능을 위해서는 마스터 노트와 슬레이브 노드가 필요하며, 슬레이브 노드는 최소 3대 이상을 권장합니다.

### ReplicaSets  
마스터 서버와 슬레이브 서버의 관계는 원본 데이터베이스에 대한 복제 본 데이터베이스에 동일한 데이터를 하나 더 저장해 두는 관계를 의미합니다.  
그런데, 실시간으로 마스터 서버에 대한 복구 작업을 수행하는 거나 슬레이브 서버를 즉시 사용할 수 있는 것은 아닙니다.  
이를 위한 기능이 리프리카셋 입니다.

리프리카셋에서 실시간으로 사용되는 메인 서버를 Primary Server 라고 합니다.  
Primary Server 을 통해 데이터를 입력, 수정, 삭제, 조회 합니다.

리프리카셋에서 두 번째 서버를 Secondary Server 라고 하는데, 이 서버는 Primary Server 장애 발생 시, 리프리카셋에 의해 즉시 Primary Server의 마지막 수행 작업부터 연속적으로 작업 수행을 가능하도록 합니다.

>-주요특징  
1) 리프리카셋에서 프라이머리 서버는 맴 2초마다 세컨더리 서버의 상태를 케츠하며 동기화를 위한 HeartBeat 작업을 수행합니다.  
2) Secondary Server가 Shutdown 된더라도 복제 작업만 중지될 뿐 Primary Server에 대한 데이터 읽기 작업은 정상적으로 수행됩니다.  
3) Primary Server가 중지되면 Secondary Server 가 Primary Server가 됩니다.  
4) OpLog 는 Primary Server 가 Secondary Server로 복제 작업을 수행하다 장애로 인해 작업을 수행할 수 없는 경우 동기화하지 못한 데이터를 추후 Secondary Server로 반영해 주기 위해 데이터를 백업해줍니다.

### 실습  

장비 2 대를 준비합니다.  
192.168.0.1 과 192.168.0.2 라고 하겠습니다.  

192.168.0.1 노드 구성  
- mongos : 50000  
- rs1_pri : 27017  
- rs1_arb : 27019  
- rs2_sec : 28018  
- rs2_arb : 28019  
- config1 : 40000  
- config2 : 40001  

192.168.0.2 노드 구성  
- rs1_sec : 27018  
- rs2_pri : 28017  
- config3 : 40002  