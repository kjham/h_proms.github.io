---
layout: post
title: 'Private 생성자를 사용하는 이유'
author: kjham.ham
date: 2019-10-21 10:30
tags: [swtech,java,effectivejava,private,construction]
---

# 인스턴스화를 막으려거든 private 생성자를 사용하라  

개발을 할 때 정적 메서드와 정적 필드만 담은 클래스를 만들고 싶을 때가 있습니다.  
객체지향적 사고는 아니지만 나름대로 쓰임새가 있습니다.  
`java.lang.Math`와 `java.util.Arrays`처럼 기본 타입 값이나 배열 관련 메서드들을 모아놓을 수 있습니다.  
`java.util.Collections`처럼 특정 인터페이스를 구현하는 객체를 생성해주는 정적 메서드를 모아놓을 수도 있습니다.  
`final 클래스`와 관련한 메서드들을 모아놓을 때도 사용합니다. final 클래스를 상속해서 하위 클래스에 메서드를 넣는 건 불가능하기 때문입니다.  

정적 멤버만 담은 유틸리티 클래스는 인스턴스로 만들어 쓰려고 설계한 것이 아닙니다.  
그러나 생성자를 명시하지 않으면 컴파일러가 자동으로 기본 생성자를 만들게 됩니다.  
의도치 않게 public 생성자가 만들어지게 되고, 이는 공개된 API에 포함되게 되는 것이지요.  

인스턴스화를 막는 방법은 비교적 간단합니다.  
private 생성자를 추가하면 되는 것이지요.  
~~~java
public class UtilityClass {
    // 기본생성자가 만들어지는 것을 막는다.
    private UtilityClass() {
        throw new AssertionError();
    }
    ... // 나머지 코드는 생략
}
~~~

명시적 생성자가 private이니 외부에서 접근할 수 없습니다.  
또한 `AssertionError`를 throw하니 내부에서도 호출할 수 없도록 합니다.  
그리고 이 방식은 상속도 불가능하게 하는 효과도 갖고 있습니다.  
모든 생성자는 명시적이든 묵시적이든 상위 클래스의 생성자를 호출하게 되는데,  
이를 private으로 선언했으니 하위 클래스가 상위 클래스의 생성자에 접근할 길이 막혀버리기 때문이지요.  