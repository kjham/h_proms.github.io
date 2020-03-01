---
layout: post
title: '알고리즘 - 이분매칭'
author: kjham.ham
date: 2020-01-14 13:00
tags: [swtech,algorithm]
##image: /files/covers/effectivejava.jpg
comments: true
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

간단하게 구현한 샘플 코드입니다.  
~~~java
// 메인 메소드
public static void main(String[] args) {
  // 테스트 수행
  execMatchExample();
}

// 매칭 테스트 수행
public static void execMatchExample() {
  // 간선 체크 주체
  Map<Integer, Map<String, Integer>> inputMap = new HashMap<>();
  Map<String, Integer> col1 = new HashMap<>();
  col1.put("컬럼1", 1);
  col1.put("컬럼2", 2);
  col1.put("컬럼3", 3);
  inputMap.put(1, col1);
  Map<String, Integer> col2 = new HashMap<>();
  col2.put("컬럼1", 4);
  col2.put("컬럼2", 5);
  inputMap.put(2, col2);
  Map<String, Integer> col3 = new HashMap<>();
  col3.put("컬럼2", 6);
  inputMap.put(3, col3);				
  
  // 점유한 노드의 정보
  Map<String, Integer> allocateMap = new HashMap<>();
  allocateMap.put("컬럼1", 0);
  allocateMap.put("컬럼2", 0);
  allocateMap.put("컬럼3", 0);
  
  int count = 0;
  for(Map.Entry<Integer, Map<String, Integer>> entry : inputMap.entrySet()) {
    // 특정 노드를 처리했는지 여부
    Map<String, Boolean> checkMap= new HashMap<>();
    checkMap.put("컬럼1", false);
    checkMap.put("컬럼2", false);
    checkMap.put("컬럼3", false);
    
    if(allocateNode(entry.getKey(), inputMap, checkMap, allocateMap)) {
      count ++;
    }
  }
  System.out.println(count + " 개의 매칭 성공");
  
  for(Map.Entry<String, Integer> entry : allocateMap.entrySet()) {
    System.out.println("ObjectId : " + entry.getValue() + " -> " + entry.getKey() + ", id: " + inputMap.get(entry.getValue()).get(entry.getKey()));
  }
}

// 노드 할당
public static boolean allocateNode(int key, Map<Integer, Map<String, Integer>> inputMap, Map<String, Boolean> checkMap, Map<String, Integer> allocateMap) {		
  if(inputMap.get(key) != null) {
    for(int i = 0; i < inputMap.get(key).size(); i++) {
      String columnNm =inputMap.get(key).keySet().toArray()[i].toString();
      System.out.println("arr.get(" + key + ")[" + i + "] = " + columnNm);
      
      if(checkMap.get(columnNm)) {
        continue;
      } else {
        checkMap.put(columnNm, true);
      }
      
      if(allocateMap.get(columnNm) == 0 || allocateNode(allocateMap.get(columnNm), inputMap, checkMap, allocateMap)) {
        allocateMap.put(columnNm, key);
        return true;
      }
    }
  }
  
  return false;
}
~~~

### 참조  
https://jason9319.tistory.com/149  

