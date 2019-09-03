---
layout: post
title: 'Linux Shell Command 정리'
author: kjham.ham
date: 2019-08-30 14:45
tags: [swtech,linux,shell]
---

### `sed (stream editor)`  
ed 명령어와 grep명령어 기능의 일부를 합친 것입니다.  
각 라인을 읽으면서 ed에서 사용하던 형식의 대치작업을 수행합니다.  

#### 치환
`sed 's/as-is대상/to-be대상' 파일명`  
`sed 's/abcd/transfer' filename.txt` 라고 했을 때..  
filename.txt 파일 내 abcd 라는 문자열을 transfer로 변환합니다.  

#### 삭제
`sed '/abcd/d' filename.txt`  
filename.txt 파일 내 abcd 문자열이 포함된 줄을 삭제하여 출력합니다.  
`sed '/abcd/!d' filename.txt`  
filename.txt 파일 내 abcd 문자열이 있는 줄을 제외하고 모두 삭제합니다.  
`sed '1,2d' filename.txt`  
첫 두줄을 삭제합니다.  
`sed '/^$/d' filename.txt`  
공백란을 삭제합니다.  

### 