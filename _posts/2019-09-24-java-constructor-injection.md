---
layout: post
title: 'Java의 Construction Injection'
author: kjham.ham
date: 2019-09-24 16:10
tags: [swtech,java]
comments: true
---

의존성을 주입할 때, Field Injection을 권장하고 있지 않습니다.  
대신 Construction Injection을 권장합니다. 또한, IntelliJ에서 Field Injection은 Warnning으로 감지합니다.

~~~java
public class Testclass {
    @Autowired // -> Warnning 발생
    private ClassName claseVariable;

    public Testclass() { }
}
~~~

+ 단일 책임의 원칙  
Field Injection은 의존성을 주입하기 쉽습니다.  
@Autowired Annotation만 선언하면 여러 개의 변수를 쉽게 나열할 수 있습니다.  
Constrction Injection을 사용하면 생성자에 파라미터가 많다는 것을 느끼고, 리팩토링을 자연스럽게 생각하게 됩니다.  

+ 숨겨진 의존성  
DI 컨테이너를 사용하는 것은 자신의 의존성뿐만 아니라 제공된 의존성까지 책임집니다.  
메서드나 생성자를 통해 확실한 표현이 되어야 하는데, Field Injection은 의존성을 숨겨버리게 됩니다.  

+ DI 컨터이너의 결합성  
DI 프레임워크의 핵심은 관리되는 클래스가 DI 컨테이너에 의존성이 없어야 합니다.  
즉, 필요한 의존성을 전달하면 독립적으로 인스턴스화 할 수 있는 단순 POJO여야 합니다.  
DI 컨테이너 없이도 단위 테스트를 수행할 수 있습니다.  
Field Injection은 필요한 의존성을 가진 클래스를 바로 인스턴스화 할 수 없습니다.  

+ 불변성  
Filend Injection은 final로 선언이 불가능 하지만, Constrction Injection은 가능합니다.  
즉, 데이터 변조를 방지할 수 있습니다.

+ 순환 의존성  
Construction Injection은 순환 의존성을 가질 경우 BeanCurrentlyCreationException이 발생하여 순환 의존성을 알 수 있습니다.  

> 결론  
Field Injection이 보다 쉽게 코드 작성이 가능하지만, 더 좋은 디자인 패턴과 코드 품질을 위해 Construction Injection을 권장합니다.