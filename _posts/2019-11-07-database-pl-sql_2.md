---
layout: post
title: 'PL/SQL 에 관하여 2'
author: kjham.ham
date: 2019-11-07 10:45
tags: [swtech,java,database,plsql]
##image: /files/covers/effectivejava.jpg
---

Oracle PL/SQL를 분석하기 위해서 타입별로 구성을 알아보도록 합니다.  

# PL/SQL 유형별 구문

## 1. Procedure  
~~~sql
CREATE OR REPLACE PROCEDURE [Name]
  [IN or OUT Parameter]
IS
  [Declare Variable]
BEGIN
  [Statement]
END;
~~~

## 2. Function  
~~~sql
CREATE OR REPLACE FUNCTION [Name]
  RETURN [Return Data Type]
IS
  [Declare Variable]
BEGIN
  [Statement]
  RETURN [Return Variable];
END;
~~~

## 3. Trigger  
~~~sql
CREATE OR REPLACE TRIGGER [Name]
  [Condition]
IS
  [Declare Variable]
BEGIN
  [Statement]
END;
~~~

## 4. Package  
~~~sql
CREATE OR REPLACE PACKAGE [Name]
IS
  [Decalre Variable]
FUNCTION [Function Name] [Argument]
PROCEDURE [Procedure Name] [Argument]
END [Name];
~~~

## 5. Decalre
~~~sql
DECLARE
  [Declare Variable]
BEGIN
  [Statement]
END;
~~~

## 6. Begin (Declare 생략 or Pl/SQL 호출)  
~~~sql
BEGIN
  [Statement]
END;
~~~

# PL/SQL 목록 및 내용 조회  

## 1. PL/SQL 목록 조회  
PL/SQL 목록만 조회할 경우 다음 SQL을 사용합니다.  
~~~sql
SELECT	DISTINCT OBJECT_NAME
FROM	user_procedures
WHERE	1=1
AND		OBJECT_TYPE IN ('PROCEDURE', 'PACKAGE', 'TRIGGER', 'FUNCTION')  
~~~

## 2. PL/SQL 내용 조회  
다음 SQL을 이용하면 'PROCEDURE', 'PACKAGE', 'TRIGGER', 'FUNCTION' 를 선택적으로 조회가 가능합니다.  
~~~sql
SELECT 	DISTINCT NAME, TYPE, LINE, TEXT
FROM	sys.USER_SOURCE
WHERE	1=1
AND		TYPE IN ('PROCEDURE', 'PACKAGE', 'TRIGGER', 'FUNCTION')
ORDER BY NAME, LINE ASC
~~~
