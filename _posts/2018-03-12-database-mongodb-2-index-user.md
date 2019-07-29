---
layout: post
current: post
navigation: True
title: (New NoSQL & mongoDB) Index&User
date: 2018-03-12 16:13:00
tags: [sw-dev-lab]
class: post-template
subclass: 'post tag-sw-dev-lab'
author: proms
---

**New NoSQL & mongoDB - Index&User**

**- Index를 사용하는 이유?**  
데이터 검색을 빠르게 처리하기 위해서 사용합니다.  

**- Index 종류**  
각 Index 에 대한 특징은 시간이 날 때 알아봅시다.  
1. Non-Unique / Unique Index

2. Single Field / Compound Index

3. Multi Key Index

4. Background Index

5. TTL Index

6. Sparse Index

7. Partial Index

8. GeoSpatial(2d) Index

9. GeoSpatial(2dsphere) Index

10. GeoHayStack Index

11. Text Index

12. Hashed Index

13. Covered Index

**- 사용자 생성 및 관리**
MongoDB는 RDBMS와 달리 스키마와 계정 관계 같은 것이 없습니다.  
컬렉션과 별도라고 볼 수 있습니다.  

OS 인증 방식 과 DB 인증 방식 두 가지가 있습니다.

사용자 권한 롤은 4가지로 나누어집니다.  
1. Database User Role  
2. DB Administration Role  
3. Administrative Role  
4. Any Database Role  

위에 넘버링이 높을수록 권한이 높습니다.