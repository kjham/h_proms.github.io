---
layout: post
title: '정적유틸클래스, 싱글턴 대신 의존객체주입 활용'
author: kjham.ham
date: 2019-10-22 10:30
tags: [swtech,java,effectivejava,dependency injection]
image: /files/covers/effectivejava.jpg
---

# 자원을 직접 명시하지말고 의존객체주입을 사용하라

많은 클래스가 하나의 자원에 의존하는 경우가 많습니다.  
가령 맞춤법 검사기는 사전 역할의 Dictionary에 의존합니다.  

`정적 유틸리티 클래스` 구현 방식  
~~~java
public class SpellChecker {
    private static final Lexicon dictionary = ... ;

    private SpellChecker() { }

    public static boolean isValid(String word) { ... }
    public static List<String> suggestions(String typo) { ... }
}
~~~

`싱글턴` 구현 방식  
~~~java
public class SpellChecker {
    private final Lexcion dictionary = ...;

    private SpellChecker(...) { }
    public static SpellChecker INSTANCE = new SpellChecker(...);

    public boolean isValid(String word) { ... }
    public List<String> suggestion(String typo) { ... }
}
~~~

두 방식 모두 다음 사항 때문에 훌륭한 방법입라 할 수 없습니다.
- 사전이 언어별로 존재할 수 있음  
- 특수 어휘용 사전이 별도로 있을 수 있음  
- 테스트용 사전이 필요할 수 있음  

사용자 자원에 따라 동작이 달라지는 클래스에는 `정적 유틸리티 클래스`나 `싱글턴 방식`은 적합하지 않습니다.  
클래스(SpellChecker)가 여러 자원 인스턴스를 지원해야 하며, 클라이언트가 원하는 자원(Dictionary)을 사용해야 합니다.  

`인스턴스를 생성할 때 생성자에 필요한 자원 전달` 구현 방식  
~~~java
public class SpellChecker {
    private final Lexicon dictionary;

    public SpellChecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }

    public boolean isValid(String word) { ... }
    public List<String> suggestions(String typo) { ... }
}
~~~

이 방식은 dictionary라는 하나의 자원만 사용하지만,  
자원이 몇 개든 의존 관계가 어떻든 상관없이 잘 동작합니다.  
또한 불변을 보장하여 여러 클라이언트가 의존 객체들을 안심하고 공유할 수 있습니다.  
`의존객체주입`은 생성자, 정적 팩터리, 빌더 모두에 똑같이 응용할 수 있습니다.
이 패턴의 쓸만한 변형으로 생성자에 자원 팩터리를 넘기는 방식인 `팩터리 메서드 패턴`이 있습니다.  

예로 다음 코드는 클라이언트가 제공한 팩터리가 생성한 타일(Tile)들로 구성된 모자이크(Mosaic)를 만드는 메서드입니다.  
`Mosaic create(Supplier<? extends Tile> tileFaction) { ... }`

> 핵심정리  
클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원이 클래스 동작에 영향을 준다면
싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다. 
이 자원들을 클래스가 직접 만들게 해서도 안 된다. 
대신 필요한 자원을 (혹은 그 자원을 만들어주는 팩터리를) 생성자에 (혹은 정적 팩터리나 빌더에) 넘겨주자. 
의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 기막히게 개선해준다.
