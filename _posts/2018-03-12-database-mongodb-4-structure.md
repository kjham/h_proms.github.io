---
layout: post
title: 'MongoDB : Structure'
author: kjham.ham
date: 2018-03-12 17:39
tags: [swtech,nosql,mongodb]
image: /files/covers/blog.jpg
comments: true
---

**New NoSQL & mongoDB - Structure**

**- Server Structre**  
1. Process 영역
2. Memory 영역
3. File 영역

**- Storage Engine**
1. Memory Mapping 엔진 (MMAP)  
파일 시스템 기반의 이 엔진은 1.x, 2.x 까지 사용되었습니다.  
초당 10만건 이상의 빅데이터에 대한 빠른 쓰기/읽기 작업을 수행한 후 분석, 통계 작업까지 효과적으로 수행할 수 있도록 최적화된 저장 엔진 입니다.  
서버 장애 시 복구 작업이 가능한 Journal 로그도 제공합니다.  
Single CPU 기반의 프로세싱  위주로 처리되기 때문에 CPU 개수보다 충분한 크기의 메모리 자원이 더 요구됩니다.

2. WiredTiger 저장엔진
이는 3.x 버전부터 제공되는 파일 시스템 기반의 저장 엔진입니다.  
다수의 사용자가 트랜잭션 위주의 데이터를 처리하는데 최적화 되었습니다.  
Point in Time Recovery 복구 기능이 제공됩니다.  
다중 CPU 기반의 트랜잭션 위주 데이터 처리에 적합하기 떄문에 충분한 개수의 CPU 와 메모리 자원이 요구됩니다.

- 메모리 영역
Resident Area : 사용자 데이터를 실제로 처리하는 메모리 영역  
Virtual Area : 가상 메모리 영역 (사용자 데이터를 압축 및 암호화)

- 프로세스 영역
Server Process : mongod.exe 를 통한 서버 구동을 위한 프로세스
Client Process : 사용자 애플리케이션을 위한 프로세스

- 파일 영역
Data File : 사용자 데이터를 저장하는 영역
Diagnostic File : 시스템 백업 및 복구를 위한 정보를 저장하는 영역
Journal(Log) File : 자동 복구를 위한 백업 데이터를 저장하는 영역

- 주요 특징
Document-Level Lock 제공으로 다수의 사용자가 트랜잭션 위주의 데이터를 빠르게 처리할 수 있도록 동시성 제공  
인메모리 구조의 개선으로 MongoDB 서버의 빠른 처리 성능 개선  
Multi-Core를 활용할 수 있는 시스템 구조, 이로인해 다중 쓰레드를 통해 집중화를 최소화하였으며 동시성을 향상  
CheckSums 기능을 통해 시스템 장애 또는 저장 장치 장애로부터 발생하는 데이터 유실 최소화 가능, 또한 File System의 훼손(Corrupt) 상태를 분석할 수 있는 기능 추가  
압축(Compression) 기능을 통해 저장 공간 최소화 가능 (기본적으로 Snappy Compression 기능 제공)

MMAP와 비교하여 Storage Size 점유율이 10%로 줄었고, 처리속도도 5배 이상 향상되었습니다.