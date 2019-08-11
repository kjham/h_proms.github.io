---
layout: post
title: 'Redis Tutorial'
author: kjham.ham
date: 2018-03-30 15:29
tags: [swtech,java,database,redis]
image: /files/covers/blog.jpg
---

**JAVA - REDIS Tutorial**

- Redis 란? (**RE**mote **DI**ctionary Server)  
Salvatore Sanfilippo 가 개발한 오픈 소스 소프트웨어  
휘발성이면서 영속성을 가진 key-value 형 스토어

Memory DB : 메모리에 데이타를 저장, 관리  

- Redis 특징  
1. 오픈 소스 소프트웨어  
2. 디스크가 아닌 메모리 기반의 데이터 저장소이다. (In-Memory data structure store)  
3. NoSQL & Cache 솔루션이며 메모리 기반으로 구성된다.  
4. 명시적으로 삭제, expire를 설정하지 않으면 데이터는 삭제되지 않는다(영구적 보존)  
5. 여러대의 서버 구성 가능하다.  
6. 데이터베이스로 사용될 수 있으며, Cache로도 사용될 수 있는 기술이다.  
7. 성능은 서버에 따라 다르나 초당 2만 ~ 10만회 수행한다.  

Memory 위에서 동작하는 Key/value 저장소(Store)인 레디스는 NoSQL DBMS로 분류되며 동시에 Memcached와 같은 인메모리(In-memory) 솔루션으로 분리된다.  
성능은 Memcached이 갖고 있는 좋은점을 기반으로 만들어졌기 때문에 Memcached보다 우수하긴하지만 더욱 복잡하며, 다양한 데이터 구조체를 지원하기 때문에 Message Queue, Shared memory, Remote Dictionary 용도로 사용될 수도 있다.  
대부분의 언어를 사용할 수 있지만, Linux를 이용하기를 권장하고 있다.  
그리고, 안전한 데이터의 보관과 백업을 위해 두 가지 방법을 제공한다.  

1) 다른 서버의 메모리에 실시간으로 복사본을 남길 수 있다.  
2) 디스크에 직접 저장하는 방법도 존재한다.  

NoSQL 중에서도 Redis가 주목을 받는 이유는 다음과 같다.  
1) 데이터 저장소로 입력/출력이 가장 빠른 메모리를 채택.  
2) 단순한 구조의 데이터 모델인 Key-Value 방식을 통해 빠른 속도.  
3) 캐시 및 데이터 스토어에 유리.  
4) 다양한 API 지원.  

Redis는 페이스북, 인스타그램, 네이버 LINE 서비스, StackOverflow, 블리자드 등 대형 서비스 없체들이 사용자들의 대규모 메세지를 실시간으로 처리하기 위하여 사용하고 있다.

> 인메모리 캐시(In-memory Cache)란?  
메모리 캐시 기반의 제품이 많은 시스템에서 사용되는 이유는 단연 성능!!!  
캐시 방식을 통해 DB Read의 부하를 감소할 수 있기 때문이다.  
서비스 요청이 증가하여 DB요청이 많아지면 DB서버 부하가 증가하게 되는데 메모리  
캐시가 적용되면 성능 및 처리속도가 향상된다.

- Redis 장점  
1. 리스트, 배열과 같은 데이터를 처리하는데 유용하다.  
1) value 값으로 문자열, 리스트, Set, Sorted set, Hash 등 여러 데이터 형식을 지원.  
2) 따라서 다양한 방식으로 데이터를 활용할 수 있다.  
3) 리스트형 데이터 입력과 삭제가 MySQL에 비해서 10배정도 빠르다고 한다.  

2. 여러 프로세스에서 동시에 같은 key에 대한 갱신을 요청할 경우, Atomic 처리로 데이터 부정합 방지 Atomic처리 함수를 제공

3. 메모리를 활용하면서 영속적인 데이터 보존  
1) 명령어로 명시적으로 삭제, expires를 설정하지 않으면 데이터가 삭제되지 않는다.  
2) 스냅샷(기억장치) 기능을 제공하여 메모리의 내용을 *.rdb 파일로 저장하여 해당 시점으로 복구할 수 있다.  

4. Redis Server는 1개의 싱글 쓰레드로 수행되며, 따라서 서버 하나에 여러개의 서버를 띄우는 것이 가능하다.  
1) Master - Slave 형식으로 구성이 가능함  
2) 데이터 분실 위험을 없애주는 것이 바로 위 Master - Slave 방식이다.

위 기능을 이용하면 실시간으로 데이터를 다른 서버에 복제한다.  
즉, Master server가 down되어도, slave server로 접속하면 바로 서비스를 계속할 수 있다.  
그리고 레디스의 성능을 거의 떨어뜨리지 않고 디스크 쓰기 기능을 제공한다.  
레디스 시작 시 이 데이터를 읽어 들어므로 데이터 분실 위험은 거의 없다고 봐도 된다.  

5. Redis에는 5가지의 데이터형을 사용할 수 있다.  
1) String  
Strings (일반적인 Key-Value)
(1) string 이라고 해서 문자열만 저장할 수 있는게 아니라,  
(2) 이진 데이타도 저장이 가능하다.(참고로 redis 에는 정수형, 실수형 이 따로 없다.)  
(3) key 에 넣을 수 있는 데이타의 최대 크기는 512 mb 이다.  

* 데이타형의 값은 정수인 경우는 int 로 아니면 raw 로 encoding 된다.  

2) Lists  
Lists (Array 형태로 Key 1개에 n개의 값을 가짐, 중복 값 가능)  
(1) 배열이라고 생각해도 된다.  
(2) 한 key 에 넣을 수 있는 요소의 최대 개수는 4,294,967,295 개이다.  

* 데이타형의 값은 설정파일에서 정해준 조건보다 큰 경우는 linkedlist 아니면 ziplist 로 encoding 된다.

3) Sets  
Sets (group 형태로 Key 1개에 n개의 중복되지 않은 값을 가짐)  
(1) 정렬되지 않은 집합 형으로 key 에 중복된 데이타는 존재하지 않는다  
(2) 추가, 제거 및 존재체크 시 소모되는 시간이, sets 에 포함된 요소의 수에 관계없이 일정하다  
(3) 한 key 에 넣을 수 있는 요소의 최대 개수는 4,294,967,295 개이다  

* 데이타형의 값은 설정파일에서 정해준 조건보다 큰 경우는 hashtable 아니면 intset 로 encoding 된다.

4) Sorted sets  
Sorted Sets (group 형태이나 각 member에 score 값을 가짐 Key-Member-Score)  
(1) sorted sets 는 가장 진보한 redis 데이타 형이라고 한다.  
(2) 요소의 추가, 제거, 업데이트는 매우 빠른 방법으로 진행되는데 이는 "요소의 개수의 로그" 에 비례하는 시간이 사용된다.  
(3) 링킹 시스템 등에서 사용되기 좋다.  
(4) sets 의 각요소마다 score 라는 실수 값을 가지고 있는 형태로 score 값으로 오름차순 정렬된다.  
(5) key 에 중복된 데이타는 존재하지 않지만 score 값은 중복 가능하다  

* 데이타형의 값은 설정파일에서 정해준 조건보다 큰 경우는 skiplist 아니면 ziplist 로 encoding 된다.  

5) Hashs  
Hashes (Object 형태의 Key-Field-Value)
(1) lists 와 비슷한데 "필드명", "필드값" 의 연속으로 이루어져 있다.  
(2) 한 key 에 포함할 수 있는 field-value 쌍의 최대 개수는 4,294,967,295 개이다.  

* 데이타형의 값은 설정파일에서 정해준 조건보다 큰 경우는 hashtable 아니면 zipmap 로 encoding 된다.

- 참고  
http://codingmania.tistory.com/18  
http://mydb.tistory.com/210  

- 더 많은 정보 참고  
https://redis.io/documentation