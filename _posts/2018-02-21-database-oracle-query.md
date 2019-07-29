---
layout: post
current: post
navigation: True
title: (Java) 자주사용하는 Oracle Query
date: 2018-03-01 00:00:00
tags: [sw-dev-lab]
class: post-template
subclass: 'post tag-sw-dev-lab'
author: proms
---

**Oracle 자주 사용하는 Query**

-- 해당 스키마에 해당하는 테이블의 통계정보 조회
SELECT TABLE_NAME, NUM_ROWS, CHAIN_CNT, BLOCKS, EMPTY_BLOCKS, AVG_SPACE, AVG_ROW_LEN FROM DBA_TABLES WHERE OWNER = '스키마명';

-- DBMS_STATS 패키지로 갱신되지 않는 테이블 통계 정보 갱신
ANALYZE TABLE 스키마명.테이블명 COMPUTE STATISTICS;