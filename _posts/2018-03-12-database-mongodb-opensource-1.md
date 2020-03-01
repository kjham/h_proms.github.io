---
layout: post
title: 'MongoDB : Introduction'
author: kjham.ham
date: 2018-03-12 16:00
tags: [swtech,nosql,mongodb]
image: /files/covers/blog.jpg
comments: true
---

**penSource MongoDB - Introduction**

**- MongoDB 란?**
몽고DB는 NoSQL(Not only SQL) 데이터베이스로, JSON(JavaScript Object Notation) 형태의 데이터를 저장합니다.  
SQL을 지원하지 않기 때문에 Join 개념이 없고, 스키마는 유동적입니다.  
여기서 유동적이라는 말은 몽고DB에서 저장하는 데이터 단위가 '다큐먼트'라는 것을 의미하는데, 이는 RDBMS에서 행 단위의 레코드라고 할 수 있습니다.  
따라서 몽고DB의 다큐먼트 속성은 SQL처럼 정형화되어 있지 않고, 가변적이기 때문에 모든 문서 형태가 비정형 데이터를 저장하고 처리하는 데 적합합니다.

다큐먼트를 집합적으로 부를 때는 '컬렉션'이라 합니다.  
컬렉션은 RDBMS에서 테이블 개념과 논리적으로 유사하지만, 데이터 구조를 미리 정의할 필요가 없습니다.  
몽고DB는 조인 기능을 포기하고, 이를 Embedding 과 Linking 기능으로 대체하였습니다.  
또한 다큐먼트 간에 트랜잭션은 없지만, 다큐먼트가 계층화되어 있고, 다큐먼트를 단위적(Atomic)으로 처리할 수 있으므로 트랜잭션과 유사한 효과를 냅니다.  
또한 여러 다큐먼트 간에 걸친 트랜잭션을 지원하지 않습니다.

RDBMS 			MongoDB  
Database	->	Database  
Table 		->	Collection  
Index 		->	Index  
Row			->	Document  
Join 		->	Embedding & Linking

**- MongoDB의 기본 지식 이해**  
1. MongoDB Json Document  
JSON Document 안에는 key 와 value 가 있습니다.  
{"a" : 6, "b" : 7, "fruit": {"apple", "pear", "banana"}}

JSON은 기본적으로 2개의 데이터 구조체를 만들기 위해 배열과 Dictionary를 사용합니다.  
배열은 [ , , ] 로, Dictionary 는 { keyword : value, value, ... } 로 표현됩니다.  
{ name: "Park", SN: 38010, class: { "CH707", "BC385", "AZ208" }}

MongoDB는 스키마가 없으며, 이는 서로 다른 2개의 다큐먼트가 동일한 스키마를 가질 필요가 없는 것을 의미합니다.

**- MongoDB Shell 이해**  
1. Database 로 이동  
@root> use test

2. Collection / Document 생성  
@root> db.things.save( { a : 1, b : 2, c : 3 })  
이는 things 라는 Collection 과 { a : 1, b : 2, c : 3} 이라는 한 개의 Document 를 생성함을 의미합니다.

3. Collection 내 Document 조회
@root> db.things.find()

4. 현재 사용 중인 Database, Collection 조회  
@root> show dbs  
@root> show collections
                                                          



