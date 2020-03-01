---
layout: post
title: 'MongoDB : CRUD 연산'
author: kjham.ham
date: 2018-03-14 16:00
tags: [swtech,nosql,mongodb]
image: /files/covers/blog.jpg
comments: true
---

**penSource MongoDB - CRUD 연산**

**- CRUD 란?**  
Create / Read / Update / Delete 를 의미합니다.  
Document 를 JSON 형태로 전달하면서 2진 JSON인 BSON 코드로 저장합니다.  

**- MongoDB의 주요기능**  
1. Index  
MongoDB는 일반적인 쿼리 밑 업데이트 성능을 향상시키기 위해 인덱스를 완벽하게 지원합니다.  
인덱스는 애플리케이션에서 데이터 구조를 효율적으로 관리하면서 컬렉션의 일정 부분을 view로 저장하도록 지원합니다.  

1) Index 를 사용하는 이유
- Index는 DB 테이블에 대한 처리 속도를 높여주는 자료 구조로서, 저장딘 자료에 대한 빠른 조회가 가능해집니다.  
- Index는 Key-Field 형태를 가지며, 스캔을 거치지 않고 원하는 문서의 주소로 바로 이동하게 합니다.  
- Collection이 커지면 읽기 동작에서 정보 전체 읽기가 발생하여 처리 속도가 느려지므로, 서버는 정보 전체 읽기가 발생하지 않도록 해야합니다.

2) Index 의 특징  
- MongoDB는 자동적으로 _id 필드에 unique Index를 생성합니다.  
- 각 Index 는 8KB의 데이터 공간이 필요합니다.  
- Index 생성 시 쓰기 및 변경 동작의 성능이 떨어집니다.  
- Index는 system.indexes 컬렉션에 저장되며, db.system.indexes.fnid() 로 색인된 키를 확인할 수 있습니다.  

3) Index 사용 방법  
db.users.find( {"username" : "david" }) 에 대한 Index 생성은
db.users.ensureIndex( {"username":1 })
db.users.ensureIndex({"date":1, "username":1 }) -> "1"은 오름차순 방향

4) Index 사용시 주의 사항  
- 모든 키에 대하여 인덱스하지 않는다.  
- 컬렉션의 절반 이상을 반환해야 하는 경우에는 인덱스를 사용하지 않습니다.

2. 복제 셋 읽기 우선 기능  
복제 셋 구성 요소를 갖는 복제 셋과 샤드 클러스터에서의 애플리케이션은 읽기 우선으로 설정됩니다.  
읽기 우선은 클라이언트가 셋의 직접 읽기 동작을 어떻게 수행할 것인지를 결정합니다.  
- 데이터 중복을 방지한다.  
- 고장 복구 기능을 제공한다.  
- Eventual Consistency를 활용하면 MongoDB의 Read Scalability를 향상시킬 수 있다.  
- Write Scalability는 복제 셋을 대신하여 샤딩으로 해결해야 한다.  

3. 쓰기 확인 (WriteConcern)  
애플리케이션은 '쓰기 확인'을 사용하여 쓰기 연산 동작을 제어합니다.  
- 드라이버 단의 슬레이브에 쓰기 적용을 확인하는 방법이다.  
- 서버가 2대인 경우, 최소한 2대의 서버에 write되어야 하며, write될 때까지 설정된 시간 동안 대기해야 한다.

4. 집계  
MongoDB는 다양한 집계 기능도 제공합니다.

**- MongoDB CRUD 읽기 동작**  
1. 읽기 동작
1) Query Interface  
db.users.find(				<- collection
	{ age: { &gt: 18 } },	<- query criteeria
	{ name: 1, address: 1 }	<- projection
).limit(5)					<- cusor modifier

2) Query 특성  
- MongoDB에서 모든 쿼리는 단일 컬렉션에서 사용된다.  
- 사용자는 limit, skips 밈ㅊ sort order를 사용하여 쿼리를 수정할 수 있다.  
- sort()가 설정되지 않으면 쿼리에서 반환된느 다큐먼트 순서는 정의되지 않는다.  
- 기존의 다큐먼트를 update하는 동작은 Query가 갱신하려는 다큐먼트를 선택하는 것과 동일한 쿼리 문법을 사용한다.  
- $match 파이프라인 작업은 집계 파이프라인에서 MongoDB 쿼리에 접근한다.

3) Projection  
MongoDB에서 쿼리는 기본적으로 매칭된 모든 다큐먼트 내부의 필드를 반환합니다.  
따라서 MongoDB가 애플리케이션으로 전송하는 데이터의 양을 줄이기 위해 쿼리에 프로젝션을 추가하여 사용할 수 있습니다.  

800개 데이터의 평균 점수를 계산하여 1개의 결과만을 출력하는 명령
~~~
db.grades.aggregate({'$group':{'_id':'$student_id', 'average':{$avg:'$score'}}), {'$sort':{'average':-1}}, {'$limit':1})
~~~

4) Projection Example  
- 결과 셋에서 1개 필드 추출하기 -> homework  
db.grades.find( {"student_id" : {$lt : 42} }, {"homework" : 0})  
- 2개 필드와 _id 필드를 추출하기  
db.grades.find( {"student_id" : {$lt : 42} }, {"type" : 1, "score" : 1 })  
- _id 필드를 제외하고 2개 필드 추출하기  
db.grades.find( {"student_id" : {$lt : 42} }, {"_id" : 0, "type" : 1, "score" : 1 })  

5) Projection 특성  
- 기본적으로 _id 필드는 결과에 포함된다. 결과 셋에서 _id 필드를 제거하려면 프로젝션 다큐먼트에서 "_id"를 0으로 설정해야 한다.  
- 몽고 DB는 배열이 포함된 필드에서 $elemMatch, $slice, $와 같은 프로젝션 연산자를 제공한다.  
- 집계 프레임워크 파으프라인에서 연관된 프로젝션 기능으로 $project 파이프라인을 사용할 수 있다.