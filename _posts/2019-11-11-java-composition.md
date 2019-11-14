---
layout: post
title: 'Composition 활용'
author: kjham.ham
date: 2019-11-11 09:30
tags: [swtech,java,effectivejava,composition]
image: /files/covers/effectivejava.jpg
---

# 상속 보다는 컴포지션을 사용하라 (아이템 18)  

상속은 코드를 재사용하는 강력한 수단이지만 항상 최선은 아닙니다.  
잘못 사용하면 오류를 내기 쉬운 소프트웨어를 만듭니다.  
상위 클래스와 하위클래스를 모두 같은 프로그래머가 통제하는 패키지 안에서라면 상속도 안전한 방법입니다.  

### 메소드 호출과 달리 상속은 캡슐화를 깨뜨립니다.
상위 클래스가 확장을 충분히 고려하고 문서화도 제대로 해두지 않으면 하위 클래스는 상위 클래스의 변화에 발맞춰 수정돼야만 합니다.  
~~~java
public class InstrumentedHashSet<E> extends HashSet<E> {
    private int addCount = 0;

    ...

    @Override
    public boolean add(E e) {
        addCount ++;
        return super.add(e);
    }

    @Override Boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }

    ...
}
~~~
위 클래스를 이용하여 다음과 같은 코드를 작성할 때 오류를 범하게 됩니다.  
~~~java
InstrumentedHashSet<String> s = new InstrumentedHashSet<>();
s.addAll(List.of("틱", "틱틱"));
~~~
개발자가 원하는 카운트는 `2`가 나와야겠지만.. 실제로는 `4`가 반환됩니다.  
상위 클래스의 `addAll`이 `add`로 구현되어 있기 때문입니다.  
* 참고 : `List.of()`는 Java9부터 지원하고, 그 이전에는 `Arrays.asList`를 사용했습니다.  

### 컴포지션 설계  
기존 클래스를 확장하는 대신, 새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조하게 하자.  
기존 클래스가 새로운 클래스의 구성 요소로 쓰인다는 뜻에서 이러한 설계를 `컴포지션(Compostion)`이라 합니다.  
새 클래스의 인스턴스 메서드들은 기존 클래스의 대응하는 멤서드를 호출해 그 결과를 반환합니다.  
이 방식을 `전달(forwarding)`이라 하며, 새 클래스의 메서드들을 `전달 메서드(forwarding method)`라 부릅니다.  
그 결과 **새로운 클래스는 기존 클래스의 내부 구현 방식의 영향에서 벗어나며, 심지어 기존 클래스에 새로운 메서드가 추가되더라도 전혀 영향받지 않습니다.**  

`래퍼 클래스 - 상속 대신 커포지션을 사용`  
~~~java
public clas InstrumentedSet<E> extends ForwardingSet<E> {
    private int addCount = 0;

    ...

    @Override
    public boolean add(E e) {
        addCount ++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }

    public int getAddCount() { return this.addCount; }
}
~~~
`재사용할 수 있는 전달 클래스`  
~~~java
public class ForwardingSet<E> implements Set<E> {
    private final Set<E> s;
    public ForwardingSet(Set<E> s) { this.s = s; }

    ...
    public boolean add(E e) { return s.add(e); }

    public boolean addAll(Collection<? extends E> c) { return s.addAll(c); }
    ...
}
~~~
위 `InstrumentedSet`클래스는 `HashSet`의 모든 기능을 정의한 `Set` 인터페이스를 활용해 설계되어 견고하고 아주 유연합니다.  
구체적으로 Set 인터페이스를 구현했고, Set의 인스턴스를 인수로 받는 생성자를 하나 제공합니다.  
임의의 Set에 계측 기능을 덧씌워 새로운 Set으로 만드는 것이 이 클래스의 핵심입니다.  

다른 `Set` 인스턴스를 감싸고(wrap) 있다는 뜻에서 `InstrumentedSet`같은 클래스를 래퍼 클래스라 하며,  
다른 `Set`에 계측 기능을 덧씌운다는 뜻에서 `데코레이터 패턴(Decorator pattern)`이라고 합니다.  
컴포지션과 전달 조합은 넓은 의미로 `위임(delegation)`이라고 부릅니다.  

래퍼 클래스는 단점이 거의 없지만, `콜백(callback)` 프레임워크와는 어울리지 않는다는 점만 주의하면 됩니다.  
콜백 프레임워크에서는 자기 자신의 참조를 다른 객체에 넘겨서 다음 호출(콜백) 때 사용하도록 합니다.  
내부 객체는 자신을 감싸고 있는 래퍼의 존재를 모르니 대신 자신(this)의 참조를 넘기고, 콜백 때는 래퍼가 아닌 내부 객체를 호출하게 됩니다.  
이를 `SELF 문제`라고 합니다.

### 컴포지션 대신 상속을 사용하기로 결정하기 전 자문해야 할 질문  
- 확장하려는 클래스의 API에 아무런 결함이 없는가 ?  
- 결함이 있다면, 이 결함이 여러분 클래스의 API까지 전파돼도 괜찮은가 ?  
컴포지션으로는 이런 결함을 숨기는 새로운 API를 설계할 수 있지만, 상속은 상위 클래스의 API를 `그 결함까지도` 그대로 계승한다.

> 핵심 정리  
상속은 강력하지만 캡슐화를 해친다는 문제가 있다.  
상속은 상위 클래스와 하위 클래스가 순수한 is-a 관계일 때만 써야 한다.  
is-a 관계일 때도 안심할 수만은 없는게, 하위 클래스의 패키지가 상위 클래스와 다르고, 상위 클래스가 확장을 고려해 설계되지 않았다면 여전히 문제가 될 수 있다.  
상속의 취약점을 피하려면 상속 대신 컴포지션과 전달을 사용하자.  
특히 래퍼 클래스로 구현할 적당한 인터페이스가 있다면 더욱 그렇다.  
래퍼 클래스는 하위 클래스보다 견고하고 강력하다.  