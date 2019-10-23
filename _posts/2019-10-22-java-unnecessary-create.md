---
layout: post
title: '불필요한 객체 생성'
author: kjham.ham
date: 2019-10-22 11:30
tags: [swtech,java,effectivejava]
image: /files/covers/effectivejava.jpg
---

# 불필요한 객체 생성을 피해라

똑같은 기능의 객체를 매번 생성하기보다는 객체 하나를 재사용하는 편이 나을 때가 많습니다.  
재사용은 빠르고 세련됩니다. 특히 불변 객체는 언제든 재사용할 수 있습니다.  

`String s = new String("bikini")`  
이 문장은 실행될 때 마다 String 인스턴스를 새로 만듭니다.  
상황에 따라 수백만개 생설될 수 있고, 쓸데없는 행위입니다.  

`String s = "bikini"`  
이 코드는 새로운 인스턴스를 매번 만드는 대신 하나의 String 인스턴스를 사용합니다.  

즉, 생성자 대신 정적 팩터리 메서드를 제공하는 불면 클래스에서는 정적 팩터리 메서드를 사용해 불필요한 객체 생성을 피할 수 있습니다.  
`Boolean(String)` 생성자 대신이 `Boolean.valueOf(String)` 팩터리 메서드를 사용하는 것이 좋습니다.  

하지만 다음 같은 경우는 정적 팩터리 메서드를 잘못 사용한 예 입니다.
~~~java
static boolean isRomanNemeral(String s) {
    return s.matches("^(?=.)M*(C[MD]|D?C{0,3})"); // 정규표현식 생략
}
~~~
`matches` 내부에서 만들어지는 `Pattern` 인스턴스가 한 번 사용디고 버려지기 때문입니다.  

다음과 같이 코드를 작성하는 것이 더 좋습니다.  
~~~java
public class RomanNumerials {
    private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})"); // 정규표현식 생략

    static boolean isRomanNumeral(String s) {
        return Roman.matchers(s).matches();
    }
}
~~~

다음 코드도 아주 불필요한 객체 생성이 포함되어 있습니다.  
~~~java
private static long sum() {
    Long sum = 0L;
    fot(long i = 0; i <= Integer.MAX_VALUE; i++)
        sum += i;
    return sum;
}
~~~
sum 의 타입으로 `Long` 대신이 `long`을 사용했다면 불필요한 객체 생성을 피하고 더 빠를 것입니다.  