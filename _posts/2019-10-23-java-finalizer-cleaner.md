---
layout: post
title: 'Finalizer와 Cleaner의 단점'
author: kjham.ham
date: 2019-10-23 13:30
tags: [swtech,java,effectivejava]
image: /files/covers/effectivejava.jpg
comments: true
---

# Finalizer와 Cleaner 사용을 피해라  

자바는 두 가지 객체 소멸자를 제공합니다.  
그 중 `finalizer`는 예측할 수 없고, 상황에 따라 위험할 수 있어 일반적으로 불필요합니다.  
오동작, 낮은 성능, 이식성 문제의 원인이 되기도 합니다.  

자바 9에서는 `finalizer`를 `deprecated API`로 지정하고 `cleaner`를 그 대안으로 소개하고 있습니다.  
`cleaner`는 `finalizer`보다 덜 위험하지만, 여전히 예측할 수 없고, 느리고, 일반적으로 불필요합니다.  

`finalizer`와 `cleaner`는 즉시 수행된다는 보장이 없기 때문에 제 때 실행되어야 하는 작업은 절대 할 수 없습니다.  
파일 닫기를 수행하는 것을 `finalizer`나 `cleaner`에 맡긴다면 중대한 오류를 발생시키게 됩니다.  
시스템이 동시에 열 수 있는 파일 개수에 한계가 있기 때문입니다.  
`finalizer`나 `cleaner`가 신속시 수행할지는 전적으로 가비지 컬렉터 알고리즘에 달려있고, 가비지 컬렉터 구현마다 천차만별입니다.  

상태를 영구적으로 수정하는 작업에서는 절대 `finalizer`나 `cleaner`에 의존해서는 안됩니다.  
데이터베이스 같은 공유 자원의 영구 락 해제를 `finalizer`나 `cleaner`에 맡겨 놓으면 분산 시스템 전체가 서서히 멈출 것입니다.  

`System.gc`나 `System.runFinalization` 메서드도 `finalzer` 나 `cleaner`가 실행될 가능성을 높여줄수는 있어도 보장해주지는 않습니다.  

- finalizer와 cleaner는 심각한 성능 문제도 동반합니다.  
AutoCloseable 객체를 생성하여 가비지 컬렉터가 객체를 수거하기 까지 12ns가 걸렸다면, `finalizer`는 550ns 즉 50배가 느린 성능을 보여줍니다.  
`finalzer`가 가비지 컬렉터의 효율을 떨어뜨리기 때문입니다.

- finalizer를 사용한 클래스는 finalizer 공격에 노출되어 심각한 보안 문제를 일으킬 수 있습니다.  
생성자나 직렬화 과정에서 예외가 발생하면, 생성 되다 만 객체에서 악의적인 하위 클래스의 finalizer가 수행될 수 있게 됩니다.  
finalizer에 정적 필드를 생성하여 가비지 컬렉터가 수집하지 못하도록 할 수 있기 때문이죠.  
객체 생성을 막으려면 생성자에서 예외를 던지는 것만으로 충분하지만 finalizer가 있다면 그렇지도 않습니다.  
final 클래스는 하위 클래스를 만들 수 없어 괜찮지만 이 외에는 finalize 메서드를 final로 선언하여 방어해야 합니다.  

그렇다면 `finalizer`나 `cleaner`를 대신해줄 묘안은 무엇이 있을까요 ?  
`AutoCloseable`를 구현해주고, 사용이 끝난 객체는 `close()`를 호출하도록 하면 됩니다.  

`finalizer`와 `cleaner`의 쓰임새는 두 가지 정도가 있습니다.  
1. 자원의 소유자가 close 메서드를 호출하지 않는 것에 대비한 안전망 역할  
자바 라이브러리의 일부 클래스도 이 역하을 수행하고 있습니다.  
대표적으로 `FileInputStream`, `FileOutputStream`, `ThreadPoolExecutor` 가 있습니다.  

2. 네이티브 피어(native peer)와 연결된 객체  
`네이티브 피어`란 네이티브 메서드를 통해 기능을 위임한 네이티브 객체를 말합니다.  
네이티브 피어는 자바 객체가 아니니 가비지 컬렉터가 그 존재를 알지 못합니다.  
이 때, `finalizer` 나 `cleaner`가 나서서 처리하기에 적당한 작업입니다.  
단, 성능 저하를 감당할 수 없거나 네이티브 피어가 사용하는 자원을 즉시 회수해야 한다면 앞서 설명한 `close` 메서드를 사용해야 합니다.  

사용자가 정의한 `Room` 클래스에 `cleaner`가 구현되어 있고 `cleaner`가 호출될 때 "방청소"가 출력된다고 가정하고 다음 코드를 봅시다.  
~~~java
public class Tennager {
    public static void main(String[] args) {
        new Room(99);
        System.out.println("아무렴");
    }
}
~~~
위 코드에서 "아무렴"은 출력되지만 "방청소"는 출력되지 않고 종료되는 모습을 확인할 수 있을 것입니다.  
`System.gc()`를 종료 전 호출했다면, "방청소"가 보일 수도 있고, 안보일 수도 있습니다. (즉시 호출을 보장하지 않기 때문에)

> System.exit을 호출할 때의 cleaner 동작은 구현하기 나름이다. 청소가 이뤄질지는 보장하지 않는다.  

~~~java
public class Tennager {
    public static void main(String[] args) {
        try (Room myRoom = new Room(7)) {
            System.out.println("안녕");
        }
    }
}
~~~
위 코드에서는 "안녕"에 이어서 "방청소"가 출력되는 것을 볼 수 있을 것입니다.  
`try-with-resource` 블록으로 감쌌기 때문에 자동 청소가 필요하지 않기 때문이지요.  

> 핵심 정리  
cleaner(자바 8까지는 finalizer)는 안전망 역할이나 중요하지 않은 네이티브 자원 회수용으로만 사용하자.  
물론 이런 경우라도 불확실성과 성능 저하에 주의해야 한다.