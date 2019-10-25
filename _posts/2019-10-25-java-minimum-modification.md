---
layout: post
title: '변경 가능성을 최소화한 클래스 설계'
author: kjham.ham
date: 2019-10-25 10:00
tags: [swtech,java,effectivejava,item16]
image: /files/covers/effectivejava.jpg
---

# 변경 가능성을 최소화하라  

불변 클래스란 그 인스턴스의 내부 값을 수정할 수 없는 클래스를 말합니다.  
예로 `String`, `BigInteger`, `BigDecimal`을 볼 수 있습니다.  
불변 클래스는 가변 클래스보다 설계하고 구현하고 사용하기 쉽고, 오류가 생길 여지도 적어 훨씬 안전합니다.  
클래스를 불변으로 만들려면 다음 다섯 가지 규칙을 따르면 됩니다.  

### 1. 객체의 상태를 변경하는 메서드(변경자)를 제공하지 않습니다.  

### 2. 클래스를 확장할 수 없도록 합니다.  
상속을 받는 대표적인 방법은 클래스를 `final`로 선언하는 것이지만 다른 방법도 알아보겠습니다.  

### 3. 모든 필드를 final로 선언합니다.  
새로 생성된 인스턴스를 동기화 없이 다른 스레드로 건네도 문제없이 동작하게끔 보장하는데 필요합니다.  

### 4. 모든 필드를 private으로 선언합니다.  
`public final`로만 선언해도 불변 객체가 되지만, 이렇게 하면 다음 릴리스에서 내부 표현을 바꾸지 못하게 되므로 권장하지 않습니다.  

### 5. 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 합니다.  
클래스에서 가변 객체를 참조하는 필드가 하나라도 있다면 클라이언트에서 그 객체의 참조를 얻을 수 없도록 해야 합니다.  

`예. 불변 복소수 클래스`
~~~java
public final class Complex {
    private final double re;
    private final double im;

    public Complex(double re, double im) {
        this.re = re;
        this.im = im;
    }

    public double readPart() { return re; }
    public double imaginaryPart() { return im; }

    public Complex plus(Complex c) {
        return new Complex(re + c.re, im + c.im);
    }
    public Complex minus(Complex c) {
        return new Complex(re - c.re, im - c.im);
    }
    public Complex times(Complex c) {
        return new Complex(re * c.re - im * c.im, re * c.im + im * c.re);
    }
    public Complex divideBy(Complex c) {
        double tmp = c.re * c.re + c.im * c.im;
        return new Complex((re * c.re + im * c.im) / tmp, (im * c.re - re * c.im) / tmp);
    }
    @Override public boolean equals(Object o) {
        if(o == this) {
            return true;
        }
        if(!(o instanceof Complex))
            return false;
        return Double.compare(c.re, re) == 0 && Double.compare(c.im, im) == 0;
    }
    @Override public int hashCode() { 
        return 31 * Double.hashCode(re) + Double.hashCode(im);
    }
    @Override public String toString() {
        return "(" + re + " + " + im + "i)";
    }
}
~~~

위 예는 Object의 메서드 몇 개를 재정의했고, 사칙연산과 접근자 메서드를 구현하였습니다.  
여기서 사칙연산 메서드들이 인스턴스 자신은 수정하지 않고 새로운 Complex 인스턴스를 만들어 반환하고 있습니다.  
이처럼 피연산자에 함수를 적용해 그 결과를 반환하지만, 피연산자 자체는 그대로인 프로그래밍 패턴을 `함수형 프로그래밍`이라고 합니다.  
또한 `add` 대신 `plus`를 사용한 것은 해당 메서드가 객체의 값을 변경하지 않는다는 사실도 강조하고 있습니다.  
이 명명규칙을 `BigInteger`, `BigDecimal`은 지키지 않아 잘못 사용하여 오류를 발생시키는 사례가 자주 있습니다.  
`함수형 프로그래밍`방식은 코드에서 불변이 되는 영역의 비율이 높아지는 장점을 누릴 수 있습니다.  

## 불변 클래스의 장점

### 불변 객체는 근본적으로 스레드 안전하여 따로 동기화할 필요가 없습니다.  

### 블변 객체는 자유롭게 공유할 수 있음은 물론, 불변 객체끼리는 내부 데이터를 공유할 수 있습니다.  
`BigInteger`는 `부호(sign)`와 `크기(magnitude)`를 가리키는 필드가 있습니다.  
그리고 `negate` 메서드를 호출하면 `부호`만 반대인 새로운 `BigInteger` 객체를 생성합니다.  
이 때, `부호(sign)` 배열은 가변이지만 복사하지 않고 원본 데이터와 공유해도 됩니다.  

### 객체를 만들 때 다른 불변 객체들을 구성요소로 사용하면 이점이 많습니다.  
값이 바뀌지 않는 구성요소들로 이뤄진다면 구조가 복잡하도 불변식을 유지하기 수월합니다.  
예로, `Map` 이나 `Set`의 구성요로소 불변 객체를 사용하는 것을 들 수 있습니다.  

### 불변 객체는 그 자체로 실패 원자성을 제공합니다.  
상태가 절대 변하지 않으니 잠깐이라도 불일치 상태에 빠질 가능성이 없습니다.  

## 불변 클래스의 단점

### 값이 다르면 반드시 독립된 객체로 만들어야 합니다.  
값의 가지수가 많다면 이들을 모두 만드는데 큰 비용일 치러야 합니다.  

## 불변 클래스를 만드는 기본적인 방법

상속하지 못하게 하는 가장 쉬운 방법은 `final` 로 클래스를 선언하는 것이지만 다른 방법도 있습니다.  
모든 생성자를 `private` 혹은 `package-private`으로 만들고 `public 정적 팩터리`를 제공하는 방법입니다.  
~~~java
public class Complex {
    private final double re;
    private final double im;

    private Complex(doube re, double im) {
        this.re = re;
        this.im = im;
    }

    public static Complex valueOf(double re, double im) {
        return new Compelx(re, im);
    }
}
~~~
이 방식은 `private`이 아니라 `package-private`로도 할 수 있기 때문에 유연하게 사용할 수 있습니다.  
패키지 바깥에서 볼 때는 `private`생성자 밖에 없으니 사실상 `final`으로 볼 수 있습니다.  
더 이상 클래스를 확장하는 것이 불가능하기 때문입니다.

포스팅 초입에 작성한 `1. 객체의 상태를 변경하는 메서드(변경자)를 제공하지 않습니다.`는 과한 감이 있기 때문에 다음처럼 완화할 수 있습니다.  
### 1. 어떤 메서드도 객체의 상태 중 외부에 비치는 값을 변경할 수 있습니다.  
어떤 불변 클래스는 계산 비용이 큰 값을 자중에 계산하여 final이 아닌 필드에 캐시해놓기도 합니다.  
똑같은 값을 요청하면 캐시해둔 값을 반환하여 계산 비용을 절감하는 것입니다.  
지연 초기화의 예이기도 한 이 기법은 `String`도 사용합니다.  

> 주의  
직렬화할 때는 추가로 주의할 점이 있습니다.  
Serializable을 구현하는 불변 클래스의 내부에 가변 객체를 참조하는 필드가 있다면 readObject나 readResolve 메서드를 반드시 제공하거나, 
ObjectOutputStream.writeUnshared와 ObjectInputStream.readUnshared 메서드를 사용해야 합니다.  
플랫폼이 제공하는 기본 직렬화 방법이면 충분하더라도 말입니다.  
그렇지 않으면 공격자가 이 클래스로부터 가변 인스턴스를 만들어낼 수 있습니다.