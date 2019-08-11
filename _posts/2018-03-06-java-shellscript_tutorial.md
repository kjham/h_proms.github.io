---
layout: post
title: 'ShellScript Tutorial'
author: kjham.ham
date: 2018-03-06 00:00
tags: [swtech,shell]
image: /files/covers/blog.jpg
---

**Java - Shell-Script Tutorial**

~~~
 if [ 값1 조건식 값2 ];then
   수행문
 fi 
~~~

~~~
 if [ 값1 조건식 값2 -a 값1 조건식 값2 ];then
   수행문
 fi 

 if [ 값1 조건식 값2 -o 값1 조건식 값2 ];then
   수행문
 fi
~~~

**변수 타입 선언 (declare)**
$ declare -i a (a는 정수형 변수)
$ a=12
$ a=a+1 (let 필요 없음)
$ echo $a
이름	의미
declare -r 변수	읽기 전용 변수로 선언
declare -i변수	정수형 변수로 선언
declare -a 변수	배열 변수로 선언
declare -f	스크립트 안에정의된 모든 함수들을 보여준다.
declare -f 함수	해당 함수 내용을 보여준다.
declare -x 변수	환경변수로 export

**산술 비교 연산**
산술 비교 연산자	의미
정수1 -eq정수2	두 정수가 같으면 참 아니면 거짓
정수1 -ne 정수2	두 정수가 다르면 참 아니면 거짓
정수1 -gt 정수2	정수1이 정수2보다 크면 참 아니면 거짓
정수1 -ge 정수2	정수1이 정수2보다 크거나 같으면 참 아니면 거짓
정수1 -lt 정수2	정수1이 정수2보다 작으면 참 아니면 거짓
정수1 -le 정수2	정수1이 정수2보다 작거나 같으면 참 아니면 거짓

**문자열 비교 연산**
문자열 비교 연산자	의미
문자열1 == 문자열2	두 문자열이 같으면 참 아니면 거짓
문자열1 != 문자열2	두 문자열이 다르면 참 아니면 거짓
-n 문자열	문자열이 null이 아니면 참
-z 문자열	문자열이 null이면 참

**파일 관련 연산**
파일 관련 연산자	의미
-a 파일	해당 파일이 존재하면 참
-e 파일	해당 파일이 존재하면 참
-r 파일	사용자가 해당 파일을 읽을 수 있으면 참
-w 파일	사용자가 해당 파일을 쓸 수 있으면 참
-x 파일	사용자가 해당 파일을 실행할 수 있으면 참
-o 파일	사용자가 해당 파일의 소유자이면 참
-z 파일	해당 파일의 크기가 0이면 참
-f 파일	해당 파일이 일반 파일이면 참
-d 파일	해당 파일이 디렉터리이면 참

**파일 및 디렉토리 삭제**
rm -fr directory/
rm -fr file

**압축 및 해제**
tar -cvf test.tar abc b c d
tar -xvf test.tar -C /home/etc

**chkconfig**
chkconfig --list
chkconfig --level 2345 [파일명] on

**vi 편집**
:set number
:set tabstop=4

**cp 복사**
cp /home /home_copy
cp -r /home /home_files_copy
cp -rp /home /home_all_attr_copy