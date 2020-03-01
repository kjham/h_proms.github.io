---
layout: post
title: 'PL/SQL 에 관하여'
author: kjham.ham
date: 2019-10-30 09:30
tags: [swtech,java,database,plsql]
##image: /files/covers/effectivejava.jpg
comments: true
---

# PL/SQL 이란 ?  
- Oracle’s Procedural Language extension to SQL 의 약자  
- 변수정의, 조건처리(IF), 반복처리(LOOP, WHILE, FOR)등을 지원하며,오라클 자체에 내장되어 있는 Procedure Language  

# PL/SQL Block Structure  
~~~sql
DECLARE (Declarative Section(선언부))
    - Optional
    - Variables, cursors, user-defined exceptions
BEGIN (Executable Section(실행부))
    - Mandatory
    - SQL Statements
    - PL/SQL Statements
EXCEPTION (Exception Handling Section(예외처리))
    - Actions to perform when errors occur
END
    - Mandatory
~~~
- PL/SQL 블록내에서는 한 문장이 종료할 때마다 세미콜론(;)을 사용  
- END뒤에 세미콜론(;)을 사용하여 하나의 블록이 끝났다는 것을 명시  
- DECLARE나 BEGIN이라는 키워드로 PL/SQL블럭이 시작  
- 단일행 주석 : --  
- 여러행 주석 : /* */  
- 행에 / 가 있으면 종결  

# PL/SQL 블럭의 유형  

### Anonymous Block (익명 블록)  
- 이름이 없는 블록을 의미 하며, 실행하기 위해 프로그램 안에서 선언 되고 실행시에 실행을 위해 PL/SQL 엔진으로 전달 된다.  
선행 컴파일러 프로그램과 SQL*Plus 또는 서버 관리자에서 익명의 블록을 내장 할 수 있다.
~~~sql
[ DECLARE ]
BEGIN
    -- statements
[ Exception ]
END ;
~~~

### Procedure (프로시저)  
- 특정 작업을 수행할수 있는 이름이 있는 PL/SQL 블록으로서, 매개 변수를 받을수 있고, 반복적으로 사용할수 있다.  
보통 연속 실행 또는 구현이 복잡한 트랜잭션을 수행하는 PL/SQL블록을 데이터베이스에 저장하기 위해 생성 한다.  
~~~sql
PROCEDURE name 
IS
BEGIN
    -- statements
[ EXCEPTION ]
END ;
~~~

### Function (함수)  
- 보통 값을 계산하고 결과값을 반환하기 위해서 함수를 많이 사용 한다.  
대부분 구성이 프로시저와 유사하지만 IN 파라미터만 사용 할 수 있고, 반드시 반환 될 값의 데이터 타입을 RETURN문에 선언해야 한다.  
또한 PL/SQL블록 내에서 RETURN문을 통해서 반드시 값을 반환 해야 한다.
~~~sql
FUNCTION name 
RETURN datetype
IS
BEGIN
    -- statements
RETURN values;
[EXCEPTION]
END ;
~~~

# 프로시저(Procedure)란?  
특정 작업을 수행 하는, 이름이 있는 PL/SQL BLOCK 이다.  
매개 변수를 받을 수 있고, 반복적으로 사용 할 수 있는 BLOCK 이다.  
보통 연속 실행 또는 구현이 복잡한 트랜잭션을 수행하는 PL/SQL BLOCK을 데이터베이스에 저장하기 위해 생성 한다.  
`프로시저 문법`  
~~~sql
CREATE OR REPLACE procedure name 
   IN argument 
   OUT argument 
   IN OUT argument 

IS 

   [변수의 선언]

BEGIN  --> 필수 

   [PL/SQL Block] 
   -- SQL문장, PL/SQL제어 문장 

   [EXCEPTION]  --> 선택
  -- error가 발생할 때 수행하는 문장

END;  --> 필수 
~~~
- CREATE OR REPLACE 구문을 사용하여 생성 한다.  
- IS 로 PL/SQL의 블록을 시작 한다.  
- LOCAL 변수는 IS 와 BEGIN 사이에 선언 한다.  

`프로시저 작성 예제`  
~~~sql
-- 프로시저의 이름은 update_sal이다 
-- update_sal 프로시저는 사번을 입력받아 급여를 인상 한다. 
-- 프로시저를 끝마칠 때에는 항상 "/"를 지정 한다.
SQL> CREATE OR REPLACE PROCEDURE update_sal 
     /* IN  Parameter */
     (v_empno    IN    NUMBER) 
         
     IS 

     BEGIN 

       UPDATE emp 
       SET sal = sal  * 1.1 
       WHERE empno = v_empno; 

       COMMIT; 

     END update_sal; 
     /     
~~~

# 함수(Function)란?  
보통 값을 계산하고 결과 값을 반환하기 위해서 함수를 많이 사용 한다.  
대부분 구성이 프로시저와 유사 하지만 IN 파라미터만 사용 할 수 있다.  
반드시 반환 될 값의 데이터 타입을 RETURN문에 선언해야 한다.  
또한 PL/SQL블록 내에서 RETURN문을 통해서 반드시 값을 반환해야 한다.  
`함수 문법`  
~~~sql
 -- PL/SQL 블록에는 적어도 한 개의 RETURN 문이 있어야 한다. 
-- PL/SQL Block은 함수가 수행 할 내용을 정의한 몸체부분이다.    
CREATE OR REPLACE FUNCTION function name 
  [(argument...)] 
  
  RETURN datatype
    -- datatype은 반환되는 값의 datatype입니다. 

IS 

  [변수 선언 부분]

BEGIN

  [PL/SQL Block]
   
  -- 리턴문이 꼭 존재해야 합니다
  RETURN 변수; 
        
END;   
~~~

`함수 작성 예제`  
~~~sql
SQL> CREATE OR REPLACE FUNCTION FC_update_sal
     (v_empno         IN    NUMBER)

      -- 리턴되는 변수의 데이터타입을 꼭 정의해야 합니다
      RETURN  NUMBER

    IS
    
    -- %type 변수가 사용(스칼라 데이터 타입 참고)
    v_sal  emp.sal%type;

    BEGIN

    UPDATE emp
    SET sal  = sal  * 1.1
    WHERE empno  = v_empno;

    COMMIT;

    SELECT sal
    INTO v_sal
    FROM emp
    WHERE empno = v_empno;

    -- 리턴문이 꼭 존재해야 합니다
    RETURN v_sal;

   END;   
   /   
~~~


출처 : http://www.gurubee.net/lecture/1343