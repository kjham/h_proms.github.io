---
layout: post
title: 'MongoDB : Sharding System'
author: kjham.ham
date: 2018-08-30 13:30
tags: [swtech,nosql,mongodb]
image: /files/covers/blog.jpg
comments: true
---

**New NoSQL & mongoDB - Sharding System**

### Sharding System 이란?  
1. Sharding 의 가장 큰 목적은 파티셔닝을 통한 데이터 분산 처리와 성능 향상을 위한 Load Balancing 입니다.  
2. 또한, 빅 데이터의 효율적 관리와 백업 및 복구 전략 수립을 위한 솔루션이기도 합니다.

### Sharding의 목적  
1. 데이터의 분산 저장  
하나의 서버에 빅 데이터를 저장, 관리하는 것은 성능 저하 문제를 유발시킵니다.  
따라서, 여러대의 서버를 통하 분산 처리했을 때 가장 이상적으로 작동할 수 있습니다.  

2. 빠른 성능  
분산 처리는 여러 개의 프로세스가 여러 개의 CPU를 통해 동시 작업을 수행했을 때 가장 이상적이라 볼 수 있습니다.

3. 데이터 백업과 복구 전략의 역할  
하나의 서버에 빅 데이터를 저장, 관리했을 때 서버 장애가 발생한다면 어마어마한 데이터 유실이 발생합니다.  
따라서, 샤딩 시스템을 이용하여 보다 안정적이고 효과적인 시스템 운영이 가능하도록 합니다.  

### 시스템 환경  
- 적절한 분산 처리를 위해 3대 이상의 샤드 서버로 구축을 권장합니다.  
- 싱글 노드로 운영할 때 보다 20~30% 이상의 추가 메모리 영역이 요구됩니다.  
(Mongos, OpLog, Balancer 프로세스 등 사용)  
- 샤스 시스템 구축할 때 요구되는 Config 서버는 최소 3대 이상 활성화할 것을 권장합니다.  

### Config 서버  
- 예기치 못한 시스템 장애를 대비하여 3대 이상의 Config 서버를 별도 시스템 혹은 적절한 샤드 서버쪽 배치 작업이 필요합니다.  
- Config-1 에 저장된 Meta Data는 Config-2, Config-3 에 동일하게 저장 관리됩니다.  
- 2대 이상의 Config 서버 운영 중, 하나의 Config 에서 장애 발생 시 나머지 Config 서버는 읽기 전용으로 되고, 최소한의 샤딩시스템 운영이 가능하도록 합니다.  
- Config 서버는 각 샤드 서버에 어떤 데이터들이 어떻게 분산 저장 되어 있는지에 대한 Meta Data가 저장되어 있으며 MongoS가 데이터를 쓰고/읽기 작업을 수행할 때 COnfig 서버를 통해 수집합니다.  

### MongoS 프로세스  
- 빅 데이터를 샤드 서버로 분배해 주는 프로세스 입니다.  
- 하나 이상의 프로세스가 활성화 됩니다.  
- Application Server에서 실행 가능합니다.  
- Config 서버로부터 Meta Data를 캐시합니다.  

Config 서버가 설치된 Node에서 각 Node 가 성호 연결될 수 있도록 MongoS에 등록해야 합니다.

### Chunk  
초당 발생하는 빅 데이터를 여러 대의 서버로 구성된 샤딩 시스템에 저장할 때 하나의 서버에만 데이터가 저장된다면 쓰기 지연 현상이 집중적으로 발생하여 성능 저하 현상이 발생합니다.  
이런 경우, 하나의 서버에 저장되는 데이터들이 여러 개의 논리족 구조로 분할 저장해 두었다가 일정한 데이터 양에 도달했을 때, 두 번째, 세 번째 서버로 분할 데이터의 일부를 이동 저장하게 되는데.. 이 분할 단위를 청크라고 합니다.

###Sharding System의 문제점  
1. 하나의 Shard 서버에 데이터가 집중되고 Load Balancing이 되지 않는 경우  
Shard Key로 설정된 Cardinality가 낮은 경우에 Chunk Size는 반드시 64MB 단위로 분할되는 것은 아닙니다.  
이런 경우, 64MB보다 훨씬 큰 Chunk 크기가 생성되는데.. 다른 서버로 데이터 분산이 이뤄지지 않아 하나의 샤드 서버에만 데이터가 집중되는 현상이 발생합니다.  
따라서 적절한 Shard Key 설정과 Chunk Size 조절이 필요합니다.  

2. 특정 Shard 서버에 IO 트래픽이 증가하는 경우  
너무 낮은 Cardinality를 가진 필드를 Shard Key로 설정하면, 특정 Shard Server에만 데이터가 저장될 수 있습니다.  
이는 Shard Server는 동일한 Shard Key를 가진 데이터들을 같은 서버에 저장하기 때문입니다.  

3. 샤드 클러스터의 밸런스가 균등하지 않은 경우  
Shard 서버에 분산 저장되는 속도가 하나의 컬렉션에 데이터가 입력되는 속도보다 느릴 때, 밸런스가 균등하지 않게 됩니다.  
Chunk Migration 이 빈약한 네트워크 대여푝의  경우 빅 데이터의 빠른 저장을 따라가지 못합니다.  
하루 일과 중 Peak Hour에 Chunk Migration이 발생하면 시스템 자원의 효율성이 떨어지게 되어, Peak Hour 에는 Chunk Migration을 중지하도록 Balance Window 를 설정하는데.. 너무 긴 Balance Window 는 균형을 무너뜨리게 됩니다.  

4. 과도한 Chunk Migration이 클러스터 작동을 멈추는 경우  
불필요하게 큰 Chunk Size는 과부하를 발생시킵니다.  
빈번한 Chunk Migration은 일시적으로 클러스트 서버 전체의 성능 지연 문제를 유발하므로 Shard 서버를 늘리는 것도 방법이 됩니다.  

5. 쓰기 겅능이 지연되고 빠른 검색이 안 되는 경우  
하나의 컬렉션은 여러 개의 익스텐트 구조로 생성되는데 이 익스텐트의 크기가 너무 작으면 빅 데이터가 저장되면서 새로운 익스텐트가 할당하며 불필요한 대기 시간이 발생하게 되고 쓰기 성능을 지연시키는 원인이 됩니다.

### ZONE Sharding  
3.4버전부터 제공되는 Zone Sharding 기능은 Auto Sharding을 통해 분산 저장되는 데이터를 사용자가 직접 원하는 Shard Server에 저장되도록 설계하는 방법입니다.  
이는 데이터의 분산 정도를 제어할 수 있는 장점을 가졌다고 볼 수 있습니다.

### 제약 조건
MongoDB는 기본적으로 Auto Sharding을 지원하고 있습니다.  
일반적으로 자동으로 생성하는 ObjectID를 ShardKey로 인식하게 됩니다.  
이 경우 비슷한 시간대에 발생한 데이터가 한 곳의 Shard에 모이게 됩니다.  

따라서, ShardKey를 직접 설정하는 경우가 발생하는데..  
ShardKey를 직접 구성할 때 다음 제약 조건을 참고해야 합니다.  

1. Shard Key는 512 byte를 넘길 수 없습니다.  
512 byte가 넘어가는 Index를 만드는 것 우스운 일이죠..  
2. Collection에 정의한 Shard Key는 변경할 수 없습니다.  
꼭 바꿔야 한다면, 새로운 Collection을 생성 하여 Shard Key를 적용하고..  
데이터를 복사해야 합니다.  
3. Shard Key로 지정한 Field는 데이터를 변경할 수 없습니다.  
Update 수행 시, 다른 Shard로 옮기는 일이 없도록 해야하기 때문입니다.  