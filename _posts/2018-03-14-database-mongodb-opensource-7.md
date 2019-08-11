---
layout: post
title: 'MongoDB : 집계 연산'
author: kjham.ham
date: 2018-03-14 16:30
tags: [swtech,nosql,mongodb]
image: /files/covers/blog.jpg
---

**openSource MongoDB - 집계 연산**

**- 집계동작 소개**  

1. 집계 파이프라인

db.orders.aggregate( [ 
	-- status field 가 "A" 인 것을 추출
	{ $match: { status: "A" } },
	-- 그룹 식별 값은 "cust_id" field 로..
	-- 그룹핑 된 document 들의 amount 값을 합친 것을 "total" field 로..
	{ $group: { _id: : "$cust_id", total : { $sum : "$amount" } } }
	])

- 파이프라인 연산자와 인덱스  
$match, $sort 파이프라인 연산자들은 파이프라인의 시작점에 사용하는 경우 인덱스로서의 장점을 발휘합니다.  
버전 2.4 이후로 도입된 $geoNear 파이프라인 연산자는 위치 인덱스의 장점을 갖습니다.  

- 선점 필터링 (Early Filtering)  
데이터의 작은 집합만을 요청하는 경우에는 파이프라인의 시작점에서 입력되는 다큐먼트를 제한하기 위해 $match, $limit, $skip 단계를 사용합니다.

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

2. 집계 연산 동작

1) $count

2) $distinct

3) $group

4) $sum

5) $addToSet  
category 로 넣는 연산자

6) $push  
caterory 로 넣되 중복 제거가 안됨

7) $max, $min

8) $project  
{
	"city" : "ACMAR",
	"loc" : [ -86.51557, 33.584132 ],
	"pop" : 6055,
	"state" : AL,
	"_id" : "35004"
} 
일 경우,
db.zips.aggregate([ 
	{ $project: {_id:0, city:{$toLower:"$city"}, pop:1, state:1, zip:"$_id" } }
	])  
로 집계 한다면 ...  
{
	"city" : "acmar",  
	"pop" : 6055,  
	"state" : "AL",  
	"zip" : "35004"  
}
로 리턴됩니다.  

9) $match  
임의의 컬렉션 필드에서 '20' 이상인 다큐먼트를 찾는 쿼리는 다음과 같습니다.  
db.<collection>.aggregate([{$match:{<field>:{$gt:20}}}])

10) $sort  
집계 연산 동작은 디스크, 메모리 기반의 정령을 모두 지원합니다.  
기본적으로 메모리 기반을 둔 정렬을 시도하고, 모든 파이프라인 단계에서 100MB의 제한이 있습니다.  
또한 $group 전후에 정렬할 수 있습니다.  
1이 오름차순, -1이 내림차순 입니다.

11) $limit, $skip  
보통 $skip을 사용한 후 $limit를 사용합니다.  
$skip 은 주어진 값 만큼 건너뛰고 목록화 하는 것이고,  
$limit 는 주어진 값 만큼 목록화 하는 것입니다.  

12) $first, $last  
$group 단계에서 $first, $last 를 사용할 수 있습니다.  
$first 는 첫 번째 값을, $last 는 마지막 값을 반환합니다.  

13) $unwind  
MongoDB의 Document는 배열을 가질 수 있는데, 배열에 있는 값을 $group에 사용하기 위해 배열을 분리하는 과정에서 $unwind 연산자를 사용할 수 있습니다.  
이와 반대되는 것은 $push 연산자 입니다.  

14) $redact
{ $redact: { $cond: { if: { $eq: [ "$level", 5 ] }, then: "$$PRUNE", else: "$$DESCEND" } } },
{ $match: { year: 2014, category: { $ne: "Z" } } }

{ $match: { year: 2014 } },
{ $redact: { $cond: { if: { $eq: [ "$level", 5 ] }, then: "$$PRUNE", else: "$$DESCEND" } } },
{ $match: { year: 2014, category: { $ne: "Z" } } }

15) $cond

16) $addFields

17) $lookup

**- 집계 연산 성능**
참고주소 : https://docs.mongodb.com/manual/core/aggregation-pipeline-optimization/

1. $sort + $match Sequence Optimization
{ $sort: { age : -1 } },
{ $match: { status: 'A' } }
**TO**
{ $match: { status: 'A' } },
{ $sort: { age : -1 } }

2. $project or $addFields + $match Sequence Optimization
{ $addFields: {
    maxTime: { $max: "$times" },
    minTime: { $min: "$times" }
} },
{ $project: {
    _id: 1, name: 1, times: 1, maxTime: 1, minTime: 1,
    avgTime: { $avg: ["$maxTime", "$minTime"] }
} },
{ $match: {
    name: "Joe Schmoe",
    maxTime: { $lt: 20 },
    minTime: { $gt: 5 },
    avgTime: { $gt: 7 }
} }
**TO**
{ $match: { name: "Joe Schmoe" } },
{ $addFields: {
    maxTime: { $max: "$times" },
    minTime: { $min: "$times" }
} },
{ $match: { maxTime: { $lt: 20 }, minTime: { $gt: 5 } } },
{ $project: {
    _id: 1, name: 1, times: 1, maxTime: 1, minTime: 1,
    avgTime: { $avg: ["$maxTime", "$minTime"] }
} },
{ $match: { avgTime: { $gt: 7 } } }

3. $redact + $match Sequence Optimization
{ $redact: { $cond: { if: { $eq: [ "$level", 5 ] }, then: "$$PRUNE", else: "$$DESCEND" } } },
{ $match: { year: 2014, category: { $ne: "Z" } } }
**TO**
{ $match: { year: 2014 } },
{ $redact: { $cond: { if: { $eq: [ "$level", 5 ] }, then: "$$PRUNE", else: "$$DESCEND" } } },
{ $match: { year: 2014, category: { $ne: "Z" } } }

4. $project + $skip or $limit Sequence Optimization
{ $sort: { age : -1 } },
{ $project: { status: 1, name: 1 } },
{ $limit: 5 }
**TO**
{ $sort: { age : -1 } },
{ $limit: 5 }
{ $project: { status: 1, name: 1 } },

5. $match + $match Coalescence
{ $match: { year: 2014 } },
{ $match: { status: "A" } }
**TO**
{ $match: { $and: [ { "year" : 2014 }, { "status" : "A" } ] } }

6. $lookup + $unwind Coalescence
{
  $lookup: {
    from: "otherCollection",
    as: "resultingArray",
    localField: "x",
    foreignField: "y"
  }
},
{ $unwind: "$resultingArray"}
**TO**
{
  $lookup: {
    from: "otherCollection",
    as: "resultingArray",
    localField: "x",
    foreignField: "y",
    unwinding: { preserveNullAndEmptyArrays: false }
  }
}

**- 집계 연산 활용**  
1. 우편번호 데이터 셋의 집계 동작  
1) 인구가 1,000만 명 이상인 주 반환  
db.zips.aggregate([  
	{ $group: { _id:"state", totalPop:{ $sum : "$pop" }}},  
	{ $matach: {totalPop: { $gte: 10*1000*1000 }}}  
])

2) 각 주의 도시 인구 평균을 반환  
db.zips.aggregate([  
	{ $group: { _id: { state: "$state", city: "city" }, pop: { $sum: "$pop" }}},
	{ $group: { _id: "$_id.state", avgCityPop: {$avg: "$pop" }}}
])

P1. state, city 를 키로 인구 합산  
P2. 첫번째 $group의 state 를 키로 갖으면서, pop의 평균 추출  

3) 각 주의 도시 인구가 가장 많은 곳과 적은 곳을 반환  
db.zips.aggregate([
	{ $group: 
		{ 	_id: { state: "$state", city: "$city" },
			pop: { $sum: "$pop"}}},
	{ $sort: { pop:1 }},
	{ $group:
		{	_id: "$_id.state",
			biggestCity: { $last: "$_id.city" },
			biggestPop: { $last: "$pop" },
			smallestCity: { $first: "$_id.city" },
			smallestPop: { $first: "$pop" }
		 }},
	// 아래 코드는 선택적으로 할 수 있습니다.  
	{ $project:
		{ 	_id:0,
			state:"$_id",
			biggestCity: { name: "$biggestCity", pop: "$biggestPop" },
			smallestCity: { name: "$smallestCity", pop: "$smallestPop" }
		}
	}
])

2. 사용자의 선호 데이터 집계 연산 활용  
가상의 스포츠 클럽을 대상으로 하는 사례입니다.  

1) 데이터 모델  
{	_id: "jane",
	joined: ISODate{"2011-03-02"},
	likes: ["golf", "racquetball"]
}

2) 다큐먼트 정규화와 분류  
이름을 대문자로 표시하고, 알파벳 순으로 정렬해봅시다.  
db.sports.aggregate{[ 
	{ $project: { name:{$toUpper: "$_id"}, _id:0 }},
	{ $sort: { name:1 }}
]}

3) 가입 월별로 사용자 이름 반환  
db.sports.aggregate{[
	{ $project: 
		{ 	month_joined: { $month: "$joined" },
			name: "$_id",
			_id: 0
		}},
	{ $sort: { month_joined: 1 }}
]}

4) 월별로 가입한 회원 숫자 반환  
db.sports.aggregate{[
	{ $project:
		{ month_joined: { $month: $"joined" }}},
	{ $group:
		{ _id: { month_joined: "$month_joined"}, number: { $sum: 1}}},
	{ $sort: {"_id.month_joined": 1}
]}

5) 회원들이 가장 선호하는 종목 5개 반환
db.sports.aggregate{[
	{ $unwind: "$likes" },
	{ $group: { _id: "$likes", number: { $sum:1 }}},
	{ $sort: { "$number":-1 }},
	{ $limit: 5}
]}

3. MapReduce 활용 사례  
{
	_id: ObjectId("50a8240b927d5d8b5891743c"),
	cust_id: "abc123",
	ord_date: new Date("Oct 04, 2012"),
	status: 'A',
	price: 25,
	items: [ { sku: "mmm", qty: 5, price: 2.5 }, {sku: "nnn", qty: 5, price: 2.5 }]
}

1) 고객별 합산 금액 반환  
var mapFunction1 = function() { 
		emit(this.cust_id, this.price);
};
var reduceFunction1 = function(keyCustId, valuePrices) {
	return Array.sum(valuePrices);	
};
db.orders.mapReduce(
	mapFunction1,
	reduceFunction1,
	{ out: "map_reduce_example" }
)