---
layout: post
title: 'Effective Java를 시작하며'
author: kjham.ham
date: 2019-09-02 09:25
tags: [swtech,java,effectivejava]
---

C언어 개발자로 신입으로 입사하여 2년을 보낸 후, JAVA 개발자로 전환하여 어느덧 3년이 지났습니다.  
이 시기에 필도서로 꼽히는 `Effective Java`를 읽으며서 한 층 더 앞으로 도약하고자 합니다.

---

Java의 아버지인 제임스 고슬링(James Gosling)이 말했다.  
`자바는 '아주 단순한' '노동자용 언어' 다.`

C++의 아버지인 비아네 스트롭스트룹(Bjarne Stroustrup)이 말했다.  
`C++는 '한 가지 방식만 지원하도록 고안된 언어들과는 현격히 다른' '다중-패러다임 언어' 다`
그리고 그는 초창기 Java를 보고 이렇게 말했다.
`갓 탄생한 언어 대부분이 그렇듯, 자바가 상대적으로 간결해 보이는 이유는`  
`어느 정도는 허상이도 어느 정도는 그 기능의 미숙함 때문이다.`  
`자바도 시간이 흐르면서 급격히 커지고 복잡해질 것이다.`  
`결국 두세 배는 커지고 구현 종속적인 확장과 라이브러리가 늘어날 것이다.`  

그로부터 20년이 흐른 지금 두 거장의 말은 모두 맞았다.

이 책은 `java.lang`, `java.util`, `java.io`, `java.util.concurrent`, `java.util.function` 같은 하위 패키지를 효과적으로 사용하게끔 구성되었다.  
예제에서 많은 디자인 패턴을 활용하기도 하고, 안티패턴에 대해서도 설명한다.  
이 책은 `명료성(clarity)`와 `단순성(simplicity)`을 핵심적인 기본원칙으로 두고있다.  
컴포넌트는 정해진 동작이나 예측할 수 있는 동작만 수행해야하고, 사용자를 놀라게 해서는 안된다.  
코드는 복사되는 것이 아니라, 재사용되어야하고 의존성은 최소로 유지해야한다.  

기술 용어는 자바 8용 언어 명세에 따른다.  
자바가 지원하는 타입은 `interface, calss, array, primitive`로 총 네 가지다.  
`annotation`은 interface의 일종이고, `enum`은 class의 일종이다.  
네 가지 타입 중 처음 세 가지는 `reference type`이다.  
class의 멤버로는 `field, method, member class, member interface`가 있다.  
`methods signature`는 `메서드 이름과 입력 배개변수의 타입`들로 이뤄지고 반환갑의 타입은 포함되지 않는다.  


