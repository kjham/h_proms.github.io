---
layout: post
current: post
navigation: True
title: (New NoSQL & mongoDB) Tutorial
date: 2018-03-12 16:00:00
tags: [sw-dev-lab]
class: post-template
subclass: 'post tag-sw-dev-lab'
author: proms
---

**New NoSQL & mongoDB - Tutorial**

**- 특징**  
1. JSON 타입으로 데이터 저장 관리
JSON 이란 ? Java Script Object Notation  

2. Replication 및 Shard 기능으로 복제 및 분산처리 가능  
또한, MapReduce 를 통해 빅데이터에 대한 빠른 추출 가능

3. 관계형 데이터베이스의 주요기능인 CRUD 위주의 다중 트랜잭션 처리도 가능

4. Memory Mapping 기술을 통해 빅 데이터에 효율적 처리에 최적화된 메모리 인 데이터베이스 구조 제공  
이로 인해 RDBMS에 비하여 적게는 3배 많게는 10배까지 성능 보장

**- 용어**
1. Table -> Collection  
2. Row -> Document  
3. Column -> Field  
4. Primary Key -> Object_ID Field  
5. Relationship -> Embedded & Link

**- 버전 주요 이력**
1. MongoDB 1.x ~ 2.x
>- MMAP Storage-Engine  
Memory 매핑 기능을 핵심으로 하는 빅 데이터의 빠른 쓰기와 읽기 중심의 MongoDB 저장 엔진 제공  
- MQL Aggregation Framework  
데이터 조작을 위한 문법 그리고 데이터 집계 및 분석을 위해 제공되는 연산자 제공
- Index Support  
데이터의 빠른 검색을 위한 기능 제공  
- Text-Search Engine  
저장된 빅 데이터로부터 빠른 검색을 위한 검색엔진  
- Collection-Level Lock  
트랜잭션 데이터 처리를 위한 Lock 매커니즘  
- Sharding & Replication  
빅 데이터의 분산 및 복제 기술

2. MongoDB 3.0
>- wiredTiger Storage Engine  
데이터 압축 기능을 핵심으로 하는 트랜잭션 데이터의 효율적인 처리 중심의 MongoDB 저장 엔진
- Compression & Encryption  
빅 데이터의 압축을 통한 저장 공간의 효율성 향상과 암호화를 통한 보안 기능  
- Validator & Auditing  
데이터의 무결성 보장을 위한 기능  
- Authentication  
수십~수백대의 서버 구축 시 발생하는 보안 문제를 강화하기 위한 인증 기술  
- Document-Level Lock  
데이터 처리를 위한 Lock 메커니즘

3. MongoDB 3.2 ~ 최근
- In-Memory Storage Engine  
In-Memory 처리기술을 통한 데이터 처리 성능을 향상시킬 수 있는 MongoDB 저장 엔진  
- OPS Manager  
MongoDB를 효율적으로 관리할 수 있는 그래픽 모드 관리 툴  
- Cloud Manager  
Cloud 서버를 이용한 MongoDB의 백업 및 복구, 관리 기능

**- Collection**  
1. Capped Collection
디스크 공간이 허용하는 범위 내에 지속적으로 저장하는 타입  
2. None Capped Collection
최초 제한된 크기가 모두 차면, 다시 처음 공간을 재사용하는 타입

**- JSON 과 BSON**
JSON (Java Script Object Notation)  
BSON (Binaray Serial Object Notation)
JSON 형태의 데이터는 실제 저장될 때, BSON 형태로 변환되어 자장 됩니다.

**- Data Type 종류**
Object_ID 타입과 JSON 타입을 제공하며,  
JSON은 18가지의 Data Type을 제공합니다.  
Double, String, Object, Array, Binaray Datra, Object id, Boolean, Date, Null, REgular expression, JavaScript code, Symbol, JavaScript code with scope, 32-bit integer, Timestamp, 64-bit integer, Min key, Max key

**- 연산자 종류**
1. 비교 연산자  
2. Boolean 연산자  
3. 산술 연산자  
4. 문자 연산자  

**- Aggregation Framework**  
빅데이터 추출을 위해 제공하는 함수입니다.  
$project, $match, $group, $sort, $limit, $skip 등 6개의 연결 연산자로 구성

**- Map Reduce**  
대용량 데이터의 효율적인 처리 및 데이터의 집합을 위해 만들어졌으며 공유되지 않은 여러 개의 노드에서 동시에 병렬처리 방식으로 빅 데이터에 대한 처리가 가능한 기능

또한, 기존 RDBMS 에서 지원하던 함수들을 우회하여 극복할 수 있는 것이 Map Reduce

**- JavaScript 함수**
사용자가 정의하여 호출하는 함수라 볼 수 있습니다.  
비 저장형 함수 (Non-Storing JavaScript Function) 과 저장형 함수 (Storing JavaScript Function) 로 나누어 집니다.  
호출할 때는, db.eval 함수로 수행합니다.   

**- Lock 정책**
1.x 의 Global Lock 에 이어 2.x 에는 Database Lock 이 제공되었고..  
3.2 버전에 이르러 Document Lock 을 제공하고 있습니다.

RDBMS 의 Row Level Lock 과 동일한 수준으로 볼 수 있습니다.  

