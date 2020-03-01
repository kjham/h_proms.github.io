---
layout: post
title: '추상 클래스보다는 인터페이스를 우선적으로 !'
author: kjham.ham
date: 2019-11-12 09:30
tags: [swtech,java,effectivejava,interface]
image: /files/covers/effectivejava.jpg
comments: true
---

# 추상 클래스보다는 인터페이스를 우선하라 (아이템 20)  

자바가 제공하는 다중 구현 메커니즘은 `인터페이스`와 `추상 클래스` 두 가지 입니다.  
자바 8부터는 `인터페이스`도 `default method`를 제공하여 구현 형태를 제공할 수 있습니다.  
이 둘의 가장 큰 차이는 추상 클래스가 정의한 타입을 구현하는 클래스는 반드시 추상 클래스의 하위 클래스가 되어야 한다는 점입니다.  
자바는 단일 상속만 지원하니, 추상 클래스 방식은 새로운 타입을 정의하는 데 커다란 제약을 안게 되는 셈입니다.  

### 기존 클래스에도 손쉽게 새로운 인터페이스를 구현해넣을 수 있습니다.
`Comparable`, `Iterable`, `AutoCloseable` 인터페이스가 추가되었을 때 표준 라이브러리의 수많은 기존 클래스가 이 인터페이스들을 구현한 채 릴리스 되었습니다.  
추상 클래스는 계층구조상 공통 조상이 되기 때문에, 모든 자손이 이를 강제로 상속받게 됩니다.  

### 인터페이스는 `믹스인(mixin)` 정의에 안성맞춤입니다.
`믹스인`이란 클래스가 구현할 수 있는 타입으로, 믹스인을 구현한 클래스에 원래의 '주된 타입' 외에도 특정 선택적 행위를 제공한다고 선언하는 효과를 줍니다.  
예로 `Comparable`은 자신을 구현한 클래스의 인스턴스들끼리 순서를 정할 수 있다고 선안하는 믹스인 인터페이스입니다.  
클래스는 두 부모를 섬길 수 없기 때문에 추상클래스를 활용할 수 없습니다.

### 인터페이스로는 계층구조가 없는 타입 프레임워크를 만들 수 있습니다.  
타입을 계층적으로 정의하면 수많은 개념을 구조적으로 잘 표현할 수 있지만, 엄격히 구분하기 어려운 개념도 있습니다.  
다음과 같이 `가수(Singer)`와 `작곡가(Songwriter)` 인터페이스가 있다고 봅시다.  
~~~java
public interface Singer {
    AudioClip sing(Song s);
}
public interface Songwriter {
    Song compose(int chartPosition);
}
~~~
가수겸 작곡가를 하는 가수가 제법있기 때문에, 인터페이스로 구현하는 전혀 아무런 문제가 되지 않습니다.  
~~~java
public interface SingerSongwriter extends Singer, Songwriter {
    AutoClip strum();
    void actSensitive();
}
~~~
같은 구조를 클래스로 만들려면 고도비만 계층구조가 되고, 속성이 n개라고 할 때 2의 n제곱만큼 조합수가 생깁니다.  
이를 `조합 폭발(combinational explosion)`이라 부릅니다.  

### 래퍼 클래스 관용구와 함께 사용하면 기능을 향상시키는 안전하고 강력한 수단이 됩니다.  
타입을 추상클래스로 정의하면 그 타입에 기능을 추가하는 방법은 상속뿐입니다.  
인터페이스의 메서드 중 구현 방법이 명백한 것이 있다면, 그 구현을 `디폴트 메서드`로 제공해 일감을 덜어줄 수 있습니다.  
`디폴트 메서드`도 제약이 있습니다.  
많은 인터페이스가 `equals`와 `hashcode`같은 `Object`의 메서드를 정의하고 있지만, 이들은 디폴트 메서드로 제공해서는 안됩니다.
또한 인터페이스는 인스턴스 필드를 가질 수 없고, public이 아닌 정적 멤버도 가질 수 없습니다. (단, private 정적 메서드는 예외)  
그리고 내가 만들지 않은 인터페이스에는 `디폴트 메서드`를 추가할 수 없습니다.  

한편, 인터페이스와 `추상 골격 구현(skeletal implementation)` 클래스를 함꼐 제공하는 식으로 인터페이스와 추상 클래스의 장점을 모두 취하는 방법도 있습니다.  
인터페이스로 타입과 필요한 디폴트 메서드 몇 개를 제공하고, 골격 구현 클래스가 나머지 메서드를 구현하면 됩니다.  
이것이 바로 `템플릿 메서드 패턴` 입니다.  
관례상 인터페이스 이름이 `Interface`라면 그 골격 클래스의 이름은 `AbstractInterface`로 짓습니다.  
예로, `AbstractCollection`, `AbstractSet`, `AbstractList` 각각이 핵심 컬렉션 인터페이스의 골격 구현입니다.  

다음 코드는 완벽히 동작하는 List 구현체를 반환하는 정잭 패터리 메서드로, `AbstractList` 골격 구현으로 활용했습니다.  
~~~java
static List<Integer> intArrayAsList(int[] a) {
    Objects.requireNonNull(a);

    // 다이아몬드 연산자를 이렇게 사용하는 건 자바 9부터 가능합니다.
    // 더 낮은 버전을 사용한다면 <Integer> 로 수정합니다.
    return new AbstractList<>() {
        @Override public Integer get(int i) { 
            return a[i]; // 오토 박싱 (아이템 6)
        }

        @Override public Integer set(int i, Integer val) {
            int oldVal = a[i];
            a[i] = val;     // 오토 언박싱
            return oldVal;  // 오토 박싱
        }

        @Override public int size() {
            return a.length;
        }
    }
}
~~~
골격 구현 클래스의 아름다운은 추상 클래스처럼 구현을 도와주는 동시에, 추상 클래스로 타입을 정의할 때 따라오는 심각한 제약에서는 자유롭다는 점에 있습니다.  

골격 구현 작성은 상대적으로 쉽습니다.  
가장 먼저, 인터페이스를 잘 살펴 다른 메서드들의 구현에 사용되는 기반 메서드들을 선정합니다.  
이 기반 메서드들은 골격 구현에서는  추상 메서드가 될 것입니다.  
그 다음으로, 기반 메서드들을 사용해 직접 구현할 수 있는 메서드를 모두 디폴트 메서드로 제공합니다.  
단, equals와 hashCode같은 Object의 메서드는 디폴트 메서드로 제공하면 안 된다는 사실을 향상 유념하자.  
만약 인터페이스의 메서드가 모두 기반 메서드와 디폴트 메서드가 된다면 골격 구현 클래스를 별도로 만들 이유가 없습니다.  
이외 구현해야 할 메서드가 남아있다면, 골격 구현 클래스를 하나 만들어 나머지를 정의합니다.  
골격 구현 클래스에 필요하면 `public`이 아닌 필드와 메서드를 추가해도 됩니다.  

`골격 구현 클래스의 예`  
~~~java
public abstract class AbstractMapEntry<K, V> implements Map.Entry<K, V> {
    // 변경 가능한 엔트리는 이 메서드를 반드시 재정의해야 합니다.  
    @Override public V setValue(V value) {
        throw new UnsupprotedOperationException();
    }

    // Map.Entry.equals의 일반 규열을 구현합니다.
    @Override public boolean equals(Object o) {
        if(o == this)
            return this;
        if(!(o instanceof Map.Entry))
            return false;
        Map.Entry<?,?> e = (Map.Entry) o;
        return Objects.equals(e.getKey(), getKey()) 
            && Objects.equals(e.getValue(), getValue());
    }

    // Map.Entry.hashCode의 일반 규역을 구현합니다.  
    @Override public int hashCode() {
        return Object.hashCode(getKey()) ^ Objects.hashCode(getValue());
    }

    @Override public String toString() {
        return getKey() + "=" + getValue();
    }
}
~~~
> Map.Entry 인터페이스나 그 하위 인터페이스로는 이 골격 구현을 제공할 수 없습니다.  
> 디폴트 메서드는 equals, hashCode, toString 같은 Object 메서드를 재정의할 수 없기 때문입니다.

> 핵심 정리  
> 일반적으로 다중 구현용 타입으로는 인터페이스가 가장 적합하다.  
> 복잡한 인터페이스라면 구현하는 수고를 덜어주는 골격 구현을 함께 제공하는 방법을 꼭 고려하자.  
> 골격 구현은 '가능한 한' 인터페이스의 디폴트 메서드로 제공하여 그 인터페이스를 구현한 모든 곳에서 활용하도록 하는 것이 좋다.  
> '가능한 한'이라고 한 이유는, 인터페이스에 걸려 있는 구현상의 제약 때문에 골격 구현을 추상 클래스로 제공하는 경우가 더 흔하기 때문이다.