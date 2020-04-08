---
layout: post
title: 'AWS Athena Performance Test'
author: kjham.ham
date: 2020-04-08 16:24
create-date: 2020-04-08 16:24
tags: [swtech,cloud,aws,athena]
##image: /files/covers/effectivejava.jpg
comments: true
---

# AWS Athena Performance Test

본 내용은  Athena 를 이용하여 로그를 분석하기 위한 `최적화 내용`과 수행에 사용한 `SQL Script`, 그리고 `성능 측정 자료` 로 구성되어 있습니다.

## Overview  
- Prerequisite  
- Optimization  
- SQL Script  
- 중요 자료, 성능 측정  
  - 테스트파일 정보 및 변환 소요시간  
  - Select (단건 파일 대용량 측면에서 측정)  
  - Select (다중 파일 대용량 측면에서 측정)  

## Prerequisite  
1. Bucket 생성  
kjham-athena  
2. 각 로그파일이 위치하는 Bucket 내 디렉토리는 다음과 같습니다.  
s3://kjham-athena
	ㄴcust_idx=value
		ㄴdist_idx=value
			ㄴyear=value
				ㄴmonth=value
					ㄴday=value
						ㄴhour=value
즉, 로그 파일의 위치는 다음과 같이 저장됩니다.   
`s3://kjham-athena/cust_idx=1/dist_idx=1/year=2020/month=04/day=07/hour=05/distribution_id.2020-04-07-05.b00c94ed.gz`
3. 신규 파티션이 생길 경우 Athena 가 인지할 수 있도록 해야합니다.  
일반적으로 수행하는 다음 명령은 S3의 모든 경로를 탐색하여 파티션의 유효성을 검사하기 때문에 좋은 방안이 아닙니다.  
~~~sql
msck repair table kjham_athena.partitioned_gz
~~~
다음과 같이 수행하여 파티션을 인지할 수 있도록 합니다.
~~~sql
ALTER TABLE kjham_athena.partitioned_gz
ADD IF NOT EXISTS 
PARTITION (
	cust_idx = '1',
	dist_idx = '1',	
    year = '2020',
    month = '04',
    day = '07',
    hour = '05');
~~~

`partitioned_gz` 는 각 파티션을 의미하는 사용자지정 테이블 이름이고, `msck repair table`를 수행함으로 `partitioned_gz` 테이블이 인지하는 파티션이 추가됩니다.

## Optimization

1. 디렉토리 파티셔닝  
디렉토리를 분할한 방식의 파티션이 주는 이점은 탐색 대상이 되는 파일을 선정할 수 있기 때문에 비용과 성능이 절감되는 효과가 있습니다. 그렇다면 데이터가 많은 한 개의 파일을 대상으로 한다면 ?  
파티션을 적용하기 전과 효과는 동일합니다. 즉, 탐색 대상이 되는 파일을 분류한다는 것에 의미가 있는 작업입니다.  
2. 컬럼기반 파티셔닝  
한개의 대용량 파일을 대상으로 비용과 성능 절감효과를 가져오기 위해서는 컬림 기반으로 데이터를 변환해야 합니다. Athena 가 제공하는 `CTAS`를 이용하여 손쉽게 처리할 수 있습니다.  
`CTAS` 의 비용은 Athena 의 Select 가  스캔한 데이터처럼 동일한 비용을 청구하게 됩니다.  
~~~sql
-- parquet 테이블이 없을 경우 (최초 CTAS 수행)
CREATE TABLE kjham_athena.ctas_1_1_2020_04_07_05
WITH ( format='PARQUET', external_location='s3://kjham-athena/log_partitioned_parquet/cust_idx=1/dist_idx=1/year=2020/month=04/day=07/hour=05', parquet_compression = 'SNAPPY') AS
SELECT *
FROM kjham_athena.log_partitioned_gz
WHERE cust_idx = '1'
        AND dist_idx = '1'
        AND year = '2020'
        AND month = '04'
        AND day = '07'
        AND hour = '05';
        
-- parquet 테이블이 있는 경우 (insert 처리)        
INSERT INTO kjham_athena.log_partitioned_parquet_test
SELECT *
FROM kjham_athena.log_partitioned_gz
WHERE cust_idx = '1' AND dist_idx = '1' AND year = '2020' AND month = '04' AND day = '07' AND hour = '07';
~~~


## SQL Script

~~~sql
-- Database 생성 (DB명 : kjham_athena)
CREATE DATABASE IF NOT EXISTS kjham_athena
  COMMENT 'KJHAM Athena Database'
  LOCATION 's3://kjham-athena/'
  WITH DBPROPERTIES ('creator'='KJHAM', 'Dept.'='TEST ATHENA');

-- 파티션된 테이블 생성 (로그파일 그대로의 Format)
CREATE EXTERNAL TABLE IF NOT EXISTS
    kjham_athena.log_partitioned_gz (
         date DATE,
         time STRING,
         location STRING,
         bytes BIGINT,
         requestip STRING,
         method STRING,
         host STRING,
         uri STRING,
         status INT,
         referrer STRING,
         useragent STRING,
         querystring STRING,
         cookie STRING,
         resulttype STRING,
         requestid STRING,
         hostheader STRING,
         requestprotocol STRING,
         requestbytes BIGINT,
         timetaken FLOAT,
         xforwardedfor STRING,
         sslprotocol STRING,
         sslcipher STRING,
         responseresulttype STRING,
         httpversion STRING,
         filestatus STRING,
         encryptedfields INT 
)
PARTITIONED BY(
		 cust_idx string,
		 dist_idx string,		 
         year string,
         month string,
         day string,
         hour string
		 )
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LOCATION 's3://kjham-athena/log-partitioned-gz/'
TBLPROPERTIES ( 'skip.header.line.count'='2');
~~~

## 중요 자료, 성능 측정

### 테스트파일 정보 및 변환 소요시간
| name | file | gz | 건수 | 소요시간 |
| --- | --- | --- | --- | --- |
| 1_1_2020_04_07_06 | 4.77 mb | 39.26 kb | 1.1만 | 7.55 sec |
| 1_1_2020_04_07_07 | 144mb | 1.15 mb | 34만 | 10.09 sec |
| 1_1_2020_04_08_10 | 431mb | 21.8 mb | 108만 | 20.37 sec |
| 1_1_2020_04_08_11 | 4.21gb | 214 mb | 1000만 | 2min 38sec |

### Select (단건 파일 대용량 측면에서 측정)
~~~sql
SELECT date, sum(bytes) FROM kjham_athena.log_partitioned_parquet_test 
WHERE cust_idx = '1' AND dist_idx = '1' AND year = '2020' AND month = '04' AND day = '07' AND hour = '06'
group by 1;
~~~

| Size(gz) | gz | parqet |
| --- | --- | --- |
| 39.26kb | 2.1 sec / 39.26 kb | 2.17 sec / 0.77 kb |
| 1.15mb | 3.25 sec / 1.15 mb | 2.17 sec / 8.8 kb |
| 21.8mb | 5.02 sec / 21.8mb | 2.49 sec / 2.1 mb |
| 214mb | 31.16 sec / 214.96mb | 4.28 sec / 191.29 mb |

### Select (다중 파일 대용량 측면에서 측정)
~~~sql
SELECT date, count(bytes) FROM kjham_athena.log_partitioned_gz
WHERE cust_idx = '1' AND dist_idx = '1' AND year = '2020' AND month = '04'
group by 1;
~~~

| 로그 건수 | gz | parqet |
| --- | --- | --- |
| 35만 2010건 | 3.48 sec / 1.19 mb | 3.44 sec / 9.73 kb |
| 1135만 3610건 | 34.55 sec / 237 mb | 3.59 sec / 21.4 mb |

### MCDN 적용시 결론  

결론을 내기엔 측정 지표가 부족하긴 하지만, 대략적으로 산출을 수행했습니다.  

- Athena에서 Parquet으로 변환하는 작업은 필수적인 Optimization 입니다.  
- 로그 파일을 압축하는 작업도 필수적인 Optimization 입니다.  
- 시간당 100만건 기준으로 볼 때, 대략적으로 [컬럼기반변환작업 (20sec) + 조회작업 (4sec)] 으로 총 35초가 소요됩니다.. (28,571건 / per sec)  
- 시간당 1,000만건 기준으로 볼 때, 대략적으로 [컬럼기반변환작업 (160sec) + 조회작업 (5sec)] 으로 총 2분 45초가 소요됩니다. (48,780건 / per sec)  
- 즉, 5분 간격으로 로그파일 수집 시 Optimization 작업이 반영된 Athena 가 커버할 수 있는 건수는 약 `850만 ~ 1,450만` 으로 보입니다. (데이터를 압축(약 5초)하고 S3 에 저장하는 시간은 생략)  그리고 초당 발생 건수가 많을 수록, 처리 속도는 비례하여 상승합니다. 상승되는 임계점은 지표를 추출하지 못했습니다.  