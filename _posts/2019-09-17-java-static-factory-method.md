---
layout: post
title: 'Java의 객체 생성과 파괴 (작성중)'
author: kjham.ham
date: 2019-09-17 09:20
tags: [swtech,java,effectivejava]
---

다음 사항을 관리하는 방법에 대해 설명합니다.  
+ 객체를 만들어야 할 때와 만들지 말아야 할 때를 구분하는 방법  
+ 올바른 객체 생성 방법과 불필요한 생성을 피하는 방법  
+ 제때 파괴됨을 보장하고 파괴 전에 수행해야 할 정리 작업

# 생성자 대신 정적 팩터리 메서드를 고려하라  

클래스는 클라이언트에 public 생성자 대신 정직 팩토리 메서드를 제공할 수 있다.  
장점은 다음과 같다.  

## 1. 이름을 가질 수 있다.  
`BigInteger(int, int, Random)`와 `BigInteger.probablePrime` 중 `값이 소수인 BigInteger를 반환`의 의미를 더 갖고 있는가 ?  
클래스를 설명하는 문서를 참고하지 않다고 쉽게 의미부여를 할 수 있다.  

## 2. 호출될 때마다 인스턴스를 새로 생성하지는 않아도 된다.  
인스턴스를 미리 만들거나 새로 생성한 인스턴스를 캐싱하여 재활용하는 식의 불필요한 객체 생성을 피할 수 있다.  
`플라이웨이트 패턴(Flyweight Pattern)`도 이와 비슷한 기법이라 할 수 있다.  
언제 어느 인스턴스가 살아있게 하는지 통제가 가능하며 `인스턴스 통제 클래스`라 부른다.  
인스턴스를 통제하면 `싱글턴`으로 만들 수 있고, `인스턴스화 불가`로도 만들 수 있다.  
또한 불변 값 클래스에서 동치인 인스턴스가 단 하나뿐임을 보장할 수 있다.  

## 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.  
반환할 객체의 클래스를 자유롭게 선택할 수 있는 유연성을 갖는다.  
예로, 자바 컬렉션 프레임워크는 핵심 인터페이스들에 수정 불가나 동기화 등의 기능을 덧붙인 총 45개의 유틸리티 구현제를 제공하는데..  
이 구현체 대부분을 단 하나의 인스턴스화 불가 클래스인 `java.util.Collections`에서 정적 팩터리 메서드를 통해 얻도록 했다.  
자바 8부터는 인터페이스가 정적 메서드를 가질 수 없다는 제한이 풀렸기 때문에 인스턴스화 불가 동반 클래스를 둘 이유가 별로 없다.  
동반 클래스에 두었던 public 정적 멤버들 상당수를 그냥 인터페이스에 두면 되는 것이다.  

## 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.  
반환 타입의 하위 타입이기만 하면 어떤 클래스의 객체를 반환하든 상관없다.  
예로, EnumSet 클래스는 pulbic 생성자 없이 오직 정적 팩터리 메서드만 제공하는데..  
원소 수에 따라 RegularEnumSet, JumboEnumSet 의 인스턴스를 선택하여 반환한다.  
더 나은 성능의 인스턴스가 구현되었을 때, 기존 것을 대체도 가능하다.  

## 5. 정적 페터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.  
이런 유연함은 `서비스 제공자 프레임워크(Service Provider Framework)`를 만드는 근간이 되며 다음 네 개의 컴포넌트로 구성된다.  
구현체의 동작을 정의하는 `서비스 인터페이스(Service Interface)`  
제공자가 구현체를 등록할 때 사용하는 `제공자 등록 API(Provider Registration API)`  
클라이언트가 서비스의 인스턴스를 얻을 때 사용하는 `서비스 접근 API(Service Access API)`  
서비스 인터페이스의 인스턴스를 생성하는 팩터리 객체를 설명하는 `서비스 제공자 인터페이스(Service Provider Interface)`
대표적으로 `JDBC(Java Database Connectivity)`가 있다.  
`Connection`이 `서비스 인터페이스` 역할을,  
`DriverManager.registerDriver`가 `제공자 등록 API` 역할을,  
`DriverManager.getConnection`이 `서비스 접근 API` 역할을,  
`Driver`가 `서비스 제공자 인터페이스` 역할을 수행한다.  
`의존 객체 주임(Dependency Injection, 의존성 주입)` 프레임워크도 강력한 서비스 제공자라고 생각할 수 있다.  

반면 다음과 같은 단점을 갖기도 한다.  

## 1. 상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.  
어찌 보면 이 제약은 상속보다 컴포지션을 사용하도록 유도하고 불변 타입으로 만들려면 이 제약을 지켜야 한다는 점에서 오히려 장점으로 받아들일 수도 있다.  

## 2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.  
생성자처럼 API 설명에 명확히 드러나지 않으니 사용자는 정적 팩터리 방식 클래스를 인스턴스화할 방법을 알아내야 한다.  
API 문서를 잘 써놓거나 메서드 이름도 널리 알려진 규약을 따라 짓는 식으로 문제를 완화해줘야 한다.  

+ from  
매개변수를 하나 받아서 해당 타입의 인스턴스를 반환하는 형변환 메서드  
`Date d = Date.from(instant);`

+ of  
여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드  
`Set<Rank> faceCards = EnumSet.of(JACK, QUEEM, KING)`;  

+ valueOf  
from과 of의 더 자세한 버전  
`BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);`

+ instance 혹은 getInstance  
매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지는 않는다.  
`StackWalker luke = StackWalker.getInstance(options);`

+ create 혹은 newInstance  
instance 혹은 getInstance와 같지만, 매번 새로운 인스턴스를 생성해 반환함을 보장한다.  
`Object newArray = Array.newInstance(classObject, arrayLen);`

+ getType  
getInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다.  
"Type"은 팩터리 메서드가 반환할 때 객체의 타입이다.  
`FileStore fs = Files.getFileStore(path);`  

+ newType  
newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다.  
"Type"은 팩터리 메서드가 반환할 객체의 타입이다.  
`BufferedReader br = Files.newBufferedReader(path);`  

+ type  
getType과 newType의 간결한 버전  
`List<Complaint> litany = Collections.list(legacyLitany);`  

> 핵심 정리  
> 정적 팩터리 메서드와 public 생성자는 각자의 쓰임새가 있으니 상대적인 장단점을 이해하고 사용하는 것이 좋다.  
> 그렇다고 하더라도 정적 팩터리를 사용하는 게 유리한 경우가 더 많으므로 무작정 public 생성자를 제공하던 습관이 있다면 고치자.  

