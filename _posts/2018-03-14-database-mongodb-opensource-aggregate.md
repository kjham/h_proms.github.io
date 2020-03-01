---
layout: post
title: 'MongoDB : Aggregation Pipeline Optimization'
author: kjham.ham
date: 2018-03-14 17:30
tags: [swtech,nosql,mongodb]
image: /files/covers/blog.jpg
comments: true
---

**openSource MongoDB - Aggregate Pipeline Optimization**

| 명령어 (연산자) | 실행 | 입출력 형태 |
|:-------:|:-------:|:------:|
| $project | reshape | (1:1) 키를 지우거나 추가하기 |
| $match | filler | (n:1) 필터링하기 |
| $group | aggregate | (n:1) 합계 연산 |
| $sort | sort | (1:1) 분류하기 |
| $skip | skips | (n:1) 건너뛰기 |
| $limit | limits | (n:1) 제한하기 |
| $unwind | normalize | (1:n) tags:["red","blue","green"] -> tags:red, tags:green, tags:blue |
| $out | output | (1:1) 출력하기 |

**1. Pipeline Sequence Optimization**

1) $project or $addFields + $match Sequence Optimization

- Before Optimization
// 필드 maxTime, minTime 추가
{ $addFields: {
    maxTime: { $max: "$times" },
    minTime: { $min: "$times" }
} },
// document 에 출력 대상 선정
{ $project: {
    _id: 1, name: 1, times: 1, maxTime: 1, minTime: 1,
    avgTime: { $avg: ["$maxTime", "$minTime"] }
} },
// 출력 대상 필터링
{ $match: {
    name: "Joe Schmoe",
    maxTime: { $lt: 20 },
    minTime: { $gt: 5 },
    avgTime: { $gt: 7 }
} }

- After Optimization
// 출력 대상 먼저 필터링
{ $match: { name: "Joe Schmoe" } },
// 필드 maxTime, minTIme 추가 (필터링이 되서, 추가되는 필드가 더 적을 것입니다.)
{ $addFields: {
    maxTime: { $max: "$times" },
    minTime: { $min: "$times" }
} },
// 추가된 필드에 대한 필터링
{ $match: { maxTime: { $lt: 20 }, minTime: { $gt: 5 } } },
// document 에 출력 대상 선정
{ $project: {
    _id: 1, name: 1, times: 1, maxTime: 1, minTime: 1,
    avgTime: { $avg: ["$maxTime", "$minTime"] }
} },
// 추가된 avgTime에 대한 필터링
{ $match: { avgTime: { $gt: 7 } } }

위와 같이 최적화를 하는 것은, 필터링을 가능한 먼저하여 $addFields, $project 수행을 더 빠르게 하려는 것입니다.

2) $sort + $match Sequence Optimization  
- Before Optimization
{ $sort: { age : -1 } },
{ $match: { status: 'A' } }
- After Optimization
{ $match: { status: 'A' } },
{ $sort: { age : -1 } }

이것도 $sort 를 하기 전에 데이터를 필터링하여 $sort 작업 수행을 원활하게 할 수 있도록 합니다.

3) $redact + $match Sequence Optimization
- Before Optimization
{ $redact: { $cond: { if: { $eq: [ "$level", 5 ] }, then: "$$PRUNE", else: "$$DESCEND" } } },
{ $match: { year: 2014, category: { $ne: "Z" } } }
- After Optimization
// 2014년도의 데이터와 category 필드에 "Z" 문자가 아닌 것만 추출
{ $match: { category: { $ne: "Z" } } }
// level 필드가 5라면 남기고("$$PRUNE"), 아니라면 버림("$$DESCEND")
{ $redact: { $cond: { if: { $eq: [ "$level", 5 ] }, then: "$$PRUNE", else: "$$DESCEND" } } },

이것도 결국 $match 를 먼저 하란 말..

4) $skip + $limit Sequence Optimization
- Before Optimization
{ $skip: 10 },
{ $limit: 5 }
- After Optimization
// $limit operator 를 먼저 수행하는 것이 효율이 좋습니다.
{ $limit: 15 },
{ $skip: 10 }

5) $project + $skip or $limit Sequence Optimization
- Before Optimization
{ $sort: { age : -1 } },
{ $project: { status: 1, name: 1 } },
{ $limit: 5 }
- After Optimization
// age 기준으로 내림차순으로 정렬 수행
{ $sort: { age : -1 } },
// 5개 의 데이터만 추출
{ $limit: 5 },
// status, name 필드만 document 로 추출
{ $project: { status: 1, name: 1 } }

일반적으로 $sort operator는 $limit, $skip operator 보다 우선순위가 높을 수 밖에 없고 (결과가 달라지니깐..)  
$limit 는 $porject 보다 먼저 수행해야 효율이 좋습니다.

그런데 $limit 의 수치가 높다면, $project로 작게 필드를 먼저 추출하는 것이 좋을지 고민해봐야 할 것 같습니다.

2. Pipeline Coalescence Optimization

1) $match + $match Coalescence
- Before Optimization
{ $match: { year: 2014 } },
{ $match: { status: "A" } }
- After Optimization
{ $match: { $and: [ { "year" : 2014 }, { "status" : "A" } ] } }

$and operator 를 사용하면, 두 개의 $match stage 를 하나로 만들 수 있습니다.