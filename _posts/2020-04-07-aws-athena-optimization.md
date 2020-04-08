---
layout: post
title: 'AWS Athena Optimization'
author: kjham.ham
date: 2020-04-08 16:15
create-date: 2020-04-07 09:15
tags: [swtech,cloud,aws,athena]
##image: /files/covers/effectivejava.jpg
comments: true
---

# AWS Athena Optimization

## CloudFront AccessLog Analysis  
웹 사이트, 비디오 및 API 작업을 대기 시간이 짧고 전송 속도가 빠른 브라우저 및 클라이언트에 제공하기 위해 Amazon 에서는 CloudFront를 제공합니다. 그리고 CloudFront의 로그는 S3에 저장되도록 구성할 수 있습니다. 그리고 로그를 분석하기 위한 다양한 방안들이 있습니다.  
- Amazon Redshift  
- Amazon Athena  
- Amazon ES  
- Amazon EMR  
본문에서는 일반적으로 선택하는 Athena를 이용한 방안을 다루고, 최적화 부분을 알아봅니다.  

## Athena Optimazation  
최적화를 수행하기 위해 `비용`과 `성능` 두 가지 측면이 있습니다.  

`비용` 부분에서는 `데이터 저장`과 `쿼리` 가 해당됩니다.  
엑세스 로그는 S3에 저장되면 `GB/월`로 청구되기 때문에 로그 파일은 gzip으로 압축하느 것이 좋습니다. 그리고 `Athena`는 압축된 로그파일을 처리할 수 있습니다. 그리고 압축된 데이터 양에 따라 `쿼리`를 수행하므로 비용 또한 낮아집니다.  
`파티셔닝`의 이점을 이용하여 비용도 절감할 수 있습니다. 시간 기반의 쿼리라고 할 경우 `년, 월, 일, 시간 등` 파티셔닝 하고 도메인에 따라 더 추가하기도 합니다. `Athena`는 파티션 기반으로 필터를 지정하여 더 적은 데이터를 검색하게 됩니다.

`성능` 부분에서는 더 적은 데이터를 스캔하는 것에 초점을 두고 있습니다.  
엑세스 로그를 열기반으로 변환하면 스캔 대상이 되는 데이터를 크게 줄일 수 있습니다. 모든 정보는 그대로 유지되지만, 값을 열별로 저장하는 식으로 변환이 되는 것입니다. 또한 열 기반의 파일을 청크로 분할하고 범위(최소, 최대), 개수, 합계 등 청크 수준에서 메타 데이터를 계산합니다. 관련 없는 파이링나 청크는 Athena가 건너뛰기 때문에 비용이 절감됩니다.

이 외에 [Athena의 10가지 성능 조정 팁](https://aws.amazon.com/ko/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/) 를 참고하여 10가지의 측면에서 성능튜닝을 알아봅니다.

## 1. Data Partition  
파티셔닝을 테이블을 여러 부분으로 나누고 날짜, 국가, 지영 등 열 값을 기준으로 관련 데이터를 함께 유지합니다. 테이블을 만들 때 이를 정의하면 쿼리 당 검색되는 데이터의 양을 크게 줄일 수 있습니다.  
`Athena`는 다음 명명 규칙 중 하나를 따라는 `HIVE 분할`을 지원합니다.  
1)  파티션 열 이름 뒤에 등호 ( '=')와 값이옵니다.  
데이터 세트가 아래 형식으로 파티션 된 경우 `MSCK REPAIRE TABLE` 명령을 실행하여 테이블에 파티션을 자동으로 추가할 수 있습니다.  
~~~
s3://yourBucket/pathToTable/<PARTITION_COLUMN_NAME>=<VALUE>/<PARTITION_COLUMN_NAME>=<VALUE>/
~~~
2) 데이터의“경로”가 위 형식을 따르지 않으면  각 파티션에 대해 ALTER TABLE ADD PARTITION 명령을 사용하여 파티션을 수동으로 추가 할 수 있습니다.  
`Alter Table <tablename> add Partition (PARTITION_COLUMN_NAME = <VALUE>, PARTITION_COLUMN2_NAME = <VALUE>) LOCATION ‘s3://yourBucket/pathToTable/YYYY/MM/DD/’;`
~~~
s3://yourBucket/pathToTable/YYYY/MM/DD/
~~~

파티션 대상 컬럼을 선정할 때는 다음 사항을 고려해야 합니다.  
- 필터로 사용되는 열은 파티셔닝에 적합한 후보입니다.  
- 분할에는 비용이 들기 때문에, 파티션 수가 증가하면 파티션 메타 데이터 검색 및 처리의 오버헤드가 높아지고 파일이 줄어듭니다. 너무 세말하게 파티션하면 이점이 사라집니다.  
- 데이터가 하나의 파티션 값으로 크게 왜곡되고 대부분의 쿼리에서 해당 값을 사용하는 경우 오버헤드로 인해 초기 이점이 사라질 수 있습니다.  

## 2. Bucket your data  
데이터를 분할하는 또 다른 방법은 단일 파티션 내에 데이터를 버킷하는 것입니다. `Bucketing`을 사용하면 그룹화 할 행이 포함된 하나 이상의 열을 지정하고 해당 행을 여러 버킷에 넣을 수 있습니다. 이를 통해 읽어야하는 버킷만 쿼리할 수 있으므로 읽을 데이터 행 수를 크게 줄일 수 있습니다.  
`Bucketing`에 사용되는 열을 선택할 때는 카디널리티가 높은(고유 값이 많은 것) 쿼리 시간 동안 읽은 데이터를 필터링하는데 자주 사용하는 열이 좋습니다. 예로, 사용자 ID 같은 기본키입니다.  
Athena 내에서 CLUSTERED BY (<버킷 열>) INTO <버킷 수> BUCKETS를 지정하여 Create Table 문에서 버킷 열을 지정할 수 있습니다. 버킷 수는 파일 크기가 최적이되도록해야합니다. 자세한 내용은 [파일 크기 최적화](https://aws.amazon.com/ko/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/#OptimizeFileSizes) 섹션을 참조하십시오.

>> 추가적인 검토와 서치가 더 필요해 보임

## 3. Compress and split files  
파일이 최적의 크기이거나 (다음 섹션 참조) 파일이 분할 가능한 경우 데이터를 압축하면 쿼리 속도가 크게 향상 될 수 있습니다. 데이터 크기가 작을수록 Amazon S3에서 Athena 로의 네트워크 트래픽이 줄어 듭니다.  
|연산|분할가능|압축비|압축/해제 속도|
|---|---|---|---|
|Gzip|No|High|Medium|
|bzip2|Yes|Very High|Slow|
|LZO|No|Low|Fast|
|Snappy|No|Low|Very Fast|
일반적으로 알고리즘의 압축 비율이 높을수록 데이터 압축 및 압축 해제에 더 많은 CPU가 필요합니다. Athena의 경우 기본적으로 데이터를 압축하고 분할 가능한 Apache Parquet 또는 Apache ORC를 사용하는 것이 좋습니다.  

## 4. Optimize file sizes  
데이터 읽기를 병렬화 할 수 있고 데이터 블록을 순차적으로 읽을 수있는 경우 쿼리가보다 효율적으로 실행됩니다. 파일 형식이 분할 가능한지 확인하면 파일 크기에 상관없이 병렬 처리에 도움이됩니다.  
그러나 파일이 너무 작 으면 (일반적으로 128MB 미만) 실행 엔진은 Amazon S3 파일 열기, 디렉토리 나열, 객체 메타 데이터 가져 오기, 데이터 전송 설정, 파일 헤더 읽기, 읽기의 오버 헤드로 추가 시간을 소비 할 수 있습니다. 압축 사전 등. 반면에 파일을 분할 할 수없고 파일이 너무 큰 경우 단일 판독기가 전체 파일 읽기를 완료 할 때까지 쿼리 처리가 대기합니다. 병렬 처리를 줄일 수 있습니다.  
작은 파일 문제를 해결하기위한 한 가지 해결책은 Amazon EMR 에서 S3DistCP 유틸리티 를 사용하는 것 입니다. 작은 파일을 더 큰 객체로 결합하는 데 사용할 수 있습니다. S3DistCP를 사용하여 HDFS에서 Amazon S3로, Amazon S3에서 Amazon S3로, Amazon S3에서 HDFS로 최적화 된 방식으로 대량의 데이터를 이동할 수도 있습니다.

이 외 더 자세한 성능튜닝은 참고 섹션의 링크를 참고하세요.

## Access Log Partition  

Amazon CloudFront는 각 액세스 로그 파일을 CSV 형식으로 선택한 S3 버킷에 제공합니다. 이름은 다음 형식을 따르며, 파일 이름은 UTC 기준으로 작성됩니다. (자세한 내용은 [액세스 로그 구성 및 사용](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/AccessLogs.html) 참조)  
~~~
/optional-prefix/distribution-ID.YYYY-MM-DD-HH.unique-ID.gz
~~~

많은 양의 엑세스 로그 데이터가 있는 경우 일부만 효율적으로 스캔하고 처리가 어렵습니다. 따라서 데이터를 분할해야합니다. 빅 데이터 영역의 대부분 도구(예: Apache Hadoop  Echo System, Athena, Glue 등)는 [Apache Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterPartition)  스타일을 사용하여 파티셔닝을 처리합니다. 
~~~
/optional-prefix/year=YYYY/month=MM/day=DD/hour=HH/distribution-ID.YYYY-MM-DD-HH.unique-ID.gz
~~~

이 구조를 생성하기 위해 S3 이벤트 알림으로 각 파일의 처리를 시작합니다. S3 버킷에 로그파일이 생성되면 이벤트가 Lambda 함수를 트리거 합니다. (`moveAccessLogs`) 기술적으로는 파일을 복사하고 원본파일을 삭제합니다.

> 파티셔닝을 수행하는 상세한 내용이 더 필요해 보임

## Conversion of the Access Logs to a Columnar Format  

Athena는 열 기반을 사용하여 쿼리와 관련이 없는 데이터를 건너 뛰어 비용을 절감할 수 있습니다. 열 기반 중 `Apache ORC`, `Apache Parquet`을 지원합니다.  
변환의 핵심은  [CTAS](https://docs.aws.amazon.com/athena/latest/ug/ctas.html) `CREATE TABLE AS SELECT` 라는 Athena의 기능입니다. CTAS 문으로 생성 된 데이터 파일을 S3의 지정된 위치에 저장합니다. 예를 들어, `CTAS`를 이용하여 매 시간 `CSV` 형식에서 `Apache Parquet` 형식으로 작성해야 합니다. 그리고 결과 데이터가 단일 파티션 된 테이블에 추가됩니다.

## 참고

[대규모로 CloudFront 엑세스 로그 분석](https://aws.amazon.com/ko/blogs/big-data/analyze-your-amazon-cloudfront-access-logs-at-scale/)
[Athena의 10가지 성능 조정 팁](https://aws.amazon.com/ko/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/)