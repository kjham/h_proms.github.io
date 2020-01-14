---
layout: post
title: '알고리즘 - 이분매칭'
author: kjham.ham
date: 2020-01-14 13:00
tags: [swtech,algorithm]
##image: /files/covers/effectivejava.jpg
---

N 개의 리스트 A와 N 개의 리스트 B 라는 두 개의 컬렉션이 주어집니다.  
즉, 리스트 A와 B의 길이는 갖고 서로 다른 데이터를 갖습니다.  

기능 구현의 목적은 A와 B에 있는 데이터가 각각 1:1 매칭을 이루도록 하는 것입니다.

간단한 예로 다음과 같이 있을 때 ..  
~~~java
List aList = Arrays.asList("A", "B", "C", "D");
List bList = Arrays.asList("1", "2", "3", "4");
~~~
`A-1`, `B-2`, `C-3`, `D-4` 로 매칭을 시켜야 합니다.  
즉, 매칭을 시킬 때 간선이 발생하지 않도록 해야 합니다.  

이와 관련된 다양한 알고리즘이 있습니다.  
### 최대 유량 알고리즘  
`디닉(Dinic's) 알고리즘 - O(V^2*E)`  
`에드몬드 카프(Edmonds karp) 알고리즘 - O(VE^2)`  

### 이분 매칭 알고리즘  
`호프크로프트(HopcroftKarp) 알고리즘 - O(E*sqrt(V))`  
`DFS 이용한 알고리즘 - O(V*E)`  

이 중 간선 없이 매칭을 하기 위해 `이분 매칭 알고리즘` 중 `DFS 이용한 알고리즘 - O(V*E)` 를 이용하였습니다.  

실제 개발 시에는 약간 변형을 하게 되어서, 샘플 코드는 생략합니다.  
다음 링크 참조하시면 도움이 될 듯 해요.  

### 참조  
https://jason9319.tistory.com/149  

