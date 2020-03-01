---
layout: post
title: '싱글턴 생성 방식'
author: kjham.ham
date: 2019-10-21 10:00
tags: [swtech,java,effectivejava,enum,construction]
image: /files/covers/effectivejava.jpg
comments: true
---

`싱글턴`이란 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말합니다.  

클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기 어렵습니다.  
타입을 인터페이스로 정의한 다음 그 인터페이스를 구현해서 만든 싱글턴이 아니라면 싱글턴 인스턴스를 가짜 구현으로 대체할 수 없기 때문입니다.  

[1] `public static final 필드` 방식의 싱글턴의 예 입니다.
~~~java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }

    public void leaveTheBuilding() { ... }
}
~~~
위 방식은 INSTANCE를 초기화할 때 딱 한번만 호출됩니다.  
public, protected 생성자가 없으므로 전체 시스템에서 인스턴스가 하나 뿐임을 보장합니다.  
> 장점  
해당 클래스가 싱글턴임이 API에 명백히 드러납니다.  
코드를 간결하게 작성할 수 있습니다.  

[2] `정적 팩터리 방식의 싱글턴`방식의 싱글턴 예 입니다.
~~~java
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    public static Elvis getInstance() { return INSTANCE; }

    public void leaveTheBuilder() { ... }
}
~~~
`Elvis.getInstance()`는 항상 같은 객체를 반환하도록 작성된 코드입니다.  
> 장점  
API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있습니다. 또한 유일한 인스턴스를 반환하던 팩터리 메서드가 호출하는 스레드별로 다른 인스턴스를 넘겨주게 할 수도 있습니다.  
정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있습니다.  
정적 팩터리의 메서드 참조를 공급자로 사용할 수 있습니다. Evlis::getInstnace를 Supplier<Elvis>로 사용하는 식입니다.  

[1], [2] 방식 모두 싱글턴 클래스를 직렬화하려면 단순히 Serializable를 구현한다고 선언하는 것만으로는 부족합니다.  
모든 인스턴스 필드를 `transient`라고 선언하고 `readResolve` 메서드를 제공해야 합니다.  
이렇게 하지 않으면 직렬화된 인스턴스를 역직렬화할 때마다 새로운 인스턴스가 만들어집니다.  
~~~java
// 싱글턴임을 보장해주는 readResolve 메서드
private Object readResolve() {
    // '진짜' Elvis를 반환하고, 가짜 Elvis는 가비지 컬렉터에 맡긴다.
    return INSTANCE;
}
~~~


[3] 싱글턴을 만드는 세 번째 방법은 `원소가 하나인 열거타입`을 선언하는 것입니다.  
~~~java
public enum Elvis {
    Instance;

    public void leaveTheBuilder() { ... }
}
~~~
public 필드 방식과 비슷하지만, 더 간결하고 추가 노력 없이 직렬화가 가능합니다.  
또한 제 2의 인스턴스가 생성되는 것도 방지할 수 있습니다.  
대부분 상황에서 열거 타입이 싱글턴을 만드는 가장 좋은 방법입니다. 조금 부자연스럽긴 하죠..  
단, enum 외에 클래스를 상속해야한다면 이 방법은 사용할 수 없습니다.  