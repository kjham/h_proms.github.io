---
layout: post
title: 'Public 클래스의 접근자 메서드'
author: kjham.ham
date: 1019-10-25 09:30
tags: [swtech,java,effectivejava,item16]
image: /files/covers/effectivejava.jpg
---

# Public 클래스에서는 Public 필드가 아닌 접근자 메서드를 사용하라

다음 코드는 어떤 단점이 존재할까 ?  
~~~java
class Point {
    public double x;
    public double y;
}
~~~
필드 `x, y`에 직접 접근할 수 있기 때문에 캡슐화의 이점을 제공받지 못하게 됩니다.  
- API 수정하지 않고는 내부 표현을 바꿀 수 없음  
- 불변식을 보장받지 못함  
- 외부에서 필드에 접근할 때 부수 작업을 수행할 수 없음  

따라서 각 필드를 `private`으로 선언하고 `getter, setter`를 활용하는 것이 일반적입니다.  

하지만 `package-private`, `private` 클래스는 데이터 필드를 노출한다 해도 하등의 문제가 없습니다.  
이 방식이 클래스 선언 면에서나 클라이언트 코드 면에서나 접근자 방식보다 훨씬 깔끔합니다.  

public 클래스의 필드를 직접 노출하지 말라는 규칙을 어기는 대표적인 사례로 `java.awt.package`의 `Point`, `Dimension`이 있습니다.  
public 클래스가 불변이라면 불변식을 보장하는 것 외에는 여전히 단점이 존재하기 때문에 이 규칙을 준수하는 것이 좋습니다.  

`불변 필드를 노출한 public 클래스.. 과연 좋은가?`  
~~~java
public final class Time {
    private static final int HOURS_PER_DAY  =24;
    private static final int MINUTES_PER_HOUR = 60;

    public final int hour;
    public final int minute;

    public Time(int hour, int minute) {
        if(hour < 0 || hour >= HOURS_PER_DAY)
            throw new IllegalArgumentException("시간: " + hour);
        if(minute < 0 || minute >= MINUTES_PER_HOUR)
            throw new IllegalArgumentException("분: " + minute);
        this.hour = hour;
        this.minute = minute;
    }    
}