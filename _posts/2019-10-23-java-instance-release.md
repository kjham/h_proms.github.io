---
layout: post
title: '객체 참조 해제에 관하여'
author: kjham.ham
date: 2019-10-23 13:00
tags: [swtech,java,effectivejava]
---

# 다 쓴 객체 참조를 해제하라

C, C++에서 메모리를 직접 관리하다가 가비지 컬렉터가 있는 Java로 넘어오면서
메모리 관리를 더 이상 하지 않는다고 생각하면 안됩니다.  
메모리 누수는 여전히 발생할 수 있기 때문입니다.  

`메모리 누수 예`  
~~~java
public class Stack {
    private Object[] elements;
    private int size = 0;

    ...

    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        return elements[--size];
    }
}
~~~

위 예에서 `elements`에 저장된 데이터를 `pop`을 했더라도, 여전히 `elements`에는 객체가 담겨 있습니다.  
더 이상 사용하지 않더라도 말이죠.  
따라서 다음과 같이 `elements[size] = null`을 처리하여 이를 방지할 수 있습니다.  
~~~java
public Object pop() {
    if(size == 0)
        throw new EmptyStackException();
    Object result = elements[--size];
    elements[size] = null;
    return result;
}
~~~

그렇다면 개발자는 `null` 처리를 매번 신경써서 해야할까요 ?  
그렇지는 않습니다. `null`을 처리해야 할 부분을 인지하고 그 때 마다 신경쓰면 됩니다.  

1. 자기 메모리를 직접 관리하는 클래스  
위 예처럼 직접 메모리르 관리하게 될 때는 `null` 처리를 해야할 부분에 신경을 써야 합니다.  

2. 캐시 역할을 갖고 있는 기능/모듈  
외부에서 키를 참조하는 동안만 엔트리가 살아 있는 캐시가 필요한 상황이라면 `WeakHashMap`을 사용하여 캐시를 생성하면 됩니다.  
사용을 모두 마친 엔트리는 자동으로 캐시를 제거할 것입니다.  
이 외에는 캐시를 생성할 때 유효기간을 만들거나, 일정 시간마다 제거할 수 있도록 해야합니다.  

3. `리스너(listener)` 혹은 `콜백(callback)`을 사용하는 로직  
콜백을 계속 등록하기만 하고 해지하지 않는다면 계속 콜백을 쌓일것입니다.  
이 때 콜백을 약한 참조로 저장한다면 가비지 컬렉터가 즉시 수거할 것입니다.  
예를 들면 `WeakHashMap`에 키로 저장하면 됩니다.  

> 핵심 정리  
메모리 누수는 겉으로 잘 드러나지 않아 시스템에 수년간 잠복하는 사례도 있다.  
이런 누수는 철저한 코드 리뷰나 힙 프로파일러 같은 디버깅 도구를 동원해야만 발견되기도 한다.  
그래서 이런 종류의 문제는 예방법을 익혀두는 것이 매우 중요하다.