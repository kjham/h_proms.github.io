---
layout: post
title: 'JPA Tutorial'
author: kjham.ham
date: 2018-03-30 15:13
tags: [swtech,java,jpa]
image: /files/covers/blog.jpg
---

**JAVA - JPA Tutorial**

JPA는 Java Persistent API 의 약자입니다.  
JPA는 여러 ORM 전문가가 참여한 EJB 3.0 스펙 작업에서 기존 EJB ORM이던 Entity Bean을 JPA라고 바꾸고 JavaSE, JavaEE를 위한 영속성(persistence) 관리와 ORM을 위한 표준 기술입니다.
JPA는 ORM 표준 기술로 Hibernate, OpenJPA, EclipseLink, TopLink Essentials과 같은 구현체가 있고 이에 표준 인터페이스가 바로 JPA입니다.

ORM(Object Relational Mapping)이란 RDB 테이블을 객체지향적으로 사용하기 위한 기술입니다.  
RDB 테이블은 객체지향적 특징(상속, 다형성, 레퍼런스, 오브젝트 등)이 없고 자바와 같은 언어로 접근하기 쉽지 않습니다.

> 장점  
객체지향적으로 데이터를 관리할 수 있기 때문에 비즈니스 로직에 집중 할 수 있으며, 객체지향 개발이 가능  
테이블 생성, 변경, 관리가 쉬움 (JPA를 잘 이해하고 있는 경우)  
로직을 쿼리에 집중하기 보다는 객체자체에 집중 할 수 있음  
빠른 개발이 가능. 유지보수 또한 쉬어짐  
반복적인 CRUD의 SQL를 회피  
SQL에 의존하는 개발 회피  

> 단점  
어렵고, 장점을 더 극대화 하기 위해서 알아야 할게 많음  
잘 이해하고 사용하지 않으면 데이터 손실이 있을 수 있음 (persistence context)  
성능상 문제가 있을 수 있음(이 문제 또한 잘 이해해야 해결이 가능)  

일반적으로, JAP 외에 DB에 접근하는 방법으로는 ..  
JDBC API  
IBatis (MyBatis)  
Spring JDBC Template : SQL Mapper  
이 있습니다.

- 참고
http://blog.woniper.net/255  
http://ramees.tistory.com/18  

- 더 많은 정보 참고  
http://blog.woniper.net/255  
https://www.slideshare.net/zipkyh/spring-datajpa?next_slideshow=1  