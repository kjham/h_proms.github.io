---
layout: post
title: 'MongoDB Limit, Sort 여러번 사용'
author: kjham.ham
date: 2019-12-17 16:30
tags: [swtech,java,database,mongodb,nosql]
##image: /files/covers/effectivejava.jpg
---

특정 데이터를 검색하기 위해 `limit` 와 `sort`를 여러번 사용해야할 때가 있습니다.

기본적인 Mongo API를 이용해서 시도해보았습니다.

기록된 데이터중 최근 1000건의 데이터 중 가장 마지막 일자의 데이터를 찾는 것이 목표입니다.
~~~java
Criteria criteria = new Criteria();
Query query = new Query();
criteria.and("creDatm").gt(startDate);
query.addCriteria(criteria);

query.with(Sort.by(Direction.ASC, "creDatm"));
query.limit(1000);
query.with(Sort.by(Direction.DESC, "creDatm"));
query.limit(1);

SqlLogM sqlLog = reactiveMongoTemplate.find(query, SqlLogM.class, COLLECTION_NAME).blockFirst();
~~~

잘 되면 좋겠지만, 예상대로 `query` 인스턴스에 적용된 `with, limit` 가 순차적으로 적용되지 않습니다.

따라서 `Aggregation` 을 이용하기로 했습니다.
~~~java
Aggregation aggregation = newAggregation(
        new CustomOperation(
                new Document()
                        .append(AgentStaticWord.PIPELINE_MATCH, new Document()
                                .append("creDatm", new Document().append("$gt", firstDate))
                        )
        ),
        new CustomOperation(
                new Document()
                        .append(AgentStaticWord.PIPELINE_SORT, new Document()
                                .append("creDatm", 1)
                        )
        ),
        new CustomOperation(
                new Document().append("$limit", AnalyzerSettingProperties.ANALYZER_SUMMARYSTAT_FIRST_COUNT)
        ),
        new CustomOperation(
                new Document()
                        .append(AgentStaticWord.PIPELINE_SORT, new Document()
                                .append("creDatm", -1)
                        )
        ),
        new CustomOperation(
                new Document().append("$limit", 1)
        )
).withOptions(Aggregation.newAggregationOptions().allowDiskUse(true).build());

List<SqlLogM> aggResultList = new ArrayList<>();
reactiveMongoTemplate.aggregate(aggregation, COLLECTION_NAME, SqlLogM.class)
        .doOnNext(aggResultList::add)
        .blockLast();

if(aggResultList.isEmpty()) {
    return null;
}
return aggResultList.get(0);
~~~

기존에도 많이 사용했던 `Aggregation`이기 때문에.. 예상대로 잘 동작합니다.