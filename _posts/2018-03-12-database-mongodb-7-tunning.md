---
layout: post
title: 'MongoDB : Performance Tuning'
author: kjham.ham
date: 2018-03-13 12:30
tags: [swtech,nosql,mongodb]
image: /files/covers/blog.jpg
---

**New NoSQL & mongoDB - Performance Tuning**

운영 단계에서 사용자가 예기치 못하는 다양한 장애와 성능 저하 현상들이 발생하고 있는데 그 원인은 다음과 같이 매우 다양합니다.

1. Design Tuning  
데이터베이스의 성능을 저하시키는 가장 큰 원인은 데이터를 저장하는 논리적 구조인 컬렉션에 대한 적절한 분석과 설계 작업이 수행되지 못했기 때문입니다.  
이러한 컬렉션의 설계 문제로 인한 성능저하 원인과 문제점을 분석하고 대처하는 방법을 디자인 튜닝이라고 합니다.

1) 대용량 데이터의 INSERT가 발생하는 Collection의 경우 최초 Extent의 크기를 충분히 설계하여 Extent 증가 시 불필요한 성능 지연이 발생하지 않도록 생성해야 하며, 대용량 데이터의 Full Scan이 자주 발생하는 Collection의 경우에는 Extent가 큰 것이 유라혹 Index Scan이 자주 발생하는 경우에는 Extent의 크기가 작거 설계되는 것이 유리합니다.

2) Collection 간에 업무적 관계가 밀접한 경우에는 Embedded 또는 Extend Document를 설계하고, 밀접하지 않은 경우에는 Linking(DBRef) 방법을 통해 설계해야합니다.

3) Compound Index를 설계할 때 자주 검색되거나 검색 조건이 유리한 Field를 선행 Field로 선택해야 성능에 유리합니다.

2. Statement Tuning  
MongoDB의 두 번쨰 성능 원인은 개발자가 작성한 Query가 적절하게 작성되지 못한 경우입니다.

1) MongoDB는 서버와 사용자에 의해 실행된 문장의 실행 결과에 대해 Profiling System을 제공하며 분석 결과는 db.system.profile Collection에 저장합니다.

2) Explain() 함수를 통해 Full Collection Scan 되는 문장들이 튜닝 대상입니다.
인덱스가 존재하지 않거나 인덱스는 존재하지만 인덱스를 통한 검색이 수행되지 못하는 경우 성능이 저하됩니다.

3) Key에 대한 Update는 Index에 대한 Update가 발생하기 때문에 성능이 저하될 수 있습니다. Key는 되도록 Update, Insert, Delete가 빈번하게 발생하지 않는 Field로 생성하는 것이 좋습니다.

4) 빅 데이터에 대한 빠른 Update를 위해서는 충분한 익스텐트 공간을 확보해야 합니다.

5) 인덱스의 빠른 생성을 위해 Background Index의 생성을 고려하십시오.

6) Graph Data는 응답 시간이 초과할 수 밖에 없기 때문에 성능이 저하될 수 밖에 없습니다.

3. Architecture Tuning  
MongoDB는 빅 데이터의 빠른 쓰기와 읽기 작업을 위해 다양한 솔루션을 제공하는데, 이런 아키텍처를 분석하여 해결하는 방안을 Architecture Tuning 라고 합니다.

1) Replica Set 영역과 사용자의 비지니스 Area 영역은 반드시 분리하십시오. 불필요하게 하나의 서버에서 다양한 기능 설정을 하면 성능이 저하될 수 밖에 없습니다.

2) 하나의 instance 보다 Shard Clusters를 적절히 활용하십시오. 여러개의 서버에 Shard 설정을 통해 분산과 Load Balancing 할 수 있습니다.

3) Slave 데이터베이스에는 Backup과 Reports 활동을 중지하십시오. 되도록 Master 데이터베이스를 통해 작업 수행하는 것이 유리합니다. 백업 및 통계 분석 작업은 슬래이브 서버에 성능 부하를 일으킵니다. 마스터 슬래이브의 복제 작업이 있기 때문입니다.

4) 하나의 Machine에는 하나의 MongoDB Instance 만 활성화 하십시오. MongoDB는 충분한 Map Memory 영역을 요구하기 때문에 불필요하게 여러 개의 mongod를 수행하게 되면 메모리 부족으로 성능 지연이 예상됩니다.

4. Instance Tuning  
충분한 메모리 영역이 할당되지 않았을 때 성능 저하 현상이 발생할 수 있습니다.  

1) Explain Plan과 Profiler를 적용하여 문장 튜닝을 먼저 수행하십십오.

2) 최적화된 Read/Write가 가능하도록 문장을 작성하십시오.
>db.test.update({_id:99}, {$inc:{imageCount:1}})  
db.test.find({folderID:99}).count().explain()

3) Data Locality(인접성)이 높아질 수 있도록 설계하고 저장하십시오.  
디스크 상 하나의 블록에 충분한 여유공간을 확보하고 보다 많은 데이터가 저장될 수 있도록 하십시오.

5. Hardware Tuning  
운영체계에서 발생하는 문제로 시스템 성능이 저하될 때, MongoDB도 함께 성능이 저하될 수 밖에 없습니다.  
따라서 운영체계에 대항 성능 튜닝이 먼저 요구됩니다.  

1) 운영체계 환경에 대한 고려

2) 하드디스크 환경에 대한 고려

3) File System 환경에 대한 고려

4) 저장엔진(Storage Engine)에 대한 고려

5) 복제(Replication) 서버 환경에 대한 고려

6) Sharding 서버 환경에 대한 고려