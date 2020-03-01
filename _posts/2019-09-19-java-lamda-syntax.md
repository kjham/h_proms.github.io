---
layout: post
title: 'Java에서 자주 사용하는 lamda식 문법'
author: kjham.ham
date: 2019-09-19 09:20
tags: [swtech,java,lamda]
comments: true
---

저는 Java에서 Lamda를 자주 사용하는 편입니다..  
Front End 개발을 한 동안 하면서 Lamda식 문법이 헷갈려서 한 곳에 정리를 하려 합니다.  

### List 를 Map 으로 변환   
Map의 Value에 Instance를 그대로 넣고 싶을 때는 `Function.identy()`를 사용합니다.  
~~~java
public Map<Integer, InstanceNm> convertLitToMap(List<InstanceNm> objectList) {
    return objectList.stream().collect(Collectors.toMap(InstanceNm:getId, Function.identy(), (v1, v2) -> v1, HashMap::new));
}
~~~

