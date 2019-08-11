---
layout: post
title: '(Java) Logging Introduction (1)'
author: kjham.ham
date: 2018-02-21 00:00
tags: [swtech,java,logging]
image: /files/covers/blog.jpg
---

**Java - Logging Introduction (1) : 종류**

Application Project 를 진행하면 필수 요소 중 하나가 Log 를 남기는 Framework 구성입니다.
제품 개발이나 운영 시, 발생하는 문제를 추적하여 원인을 파악하기 위함이죠.

굉장히 유용한 Framework 이기 때문에 종류 또한 꽤 다양합니다.
또한, Logging 작업이 굉장히 많은 Project 를 위해 관리해주는 또 다른 Framework 가 있기도 하죠.
심지어 비용을 지불하고 구매할 정도로 막강한 기능을 갖고 있죠.

우선, 종류에 대해 간단히 살펴보고 자세히 알아보도록 하겠습니다.

1. Java Util Logging (JUL)
제가 직접 Performance Test 를 한 것은 아니지만, Log4J 와 Logback 에 비해 평가가 그리 좋지 못합니다.
평가의 척도는 속도나 기능적인 면이지요.

2. Log4J 1.x
지금까지 가장 많이 사용된 Logging Framwork 일겁니다. 그러나, Log4J2 와 Logback 의 등장으로 사용 빈도가 점차 줄어들고 있지 않나 생각됩니다.
공식적으로 Log4J 1.x 는 유지보수/개발이 끝났기 때문이죠.

3. Log4J 2.x
Log4J 후속작이며, 많이 활성화 되고 있는 Logging Framework 입니다.
나중에 다루게 된다면, Log4J 2.x 와 Logback 비교가 꽤 유용한 공부가 될 것 같습니다.

4. Logback
마찬가지로 Log4J의 후속작입니다.
- ![참고 주소](https://beyondj2ee.wordpress.com/2012/11/09/logback-%EC%82%AC%EC%9A%A9%ED%95%B4%EC%95%BC-%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0-reasons-to-prefer-logback-over-log4j/)

5. SLF4J Simple Logging (SLF4J SL)

**- 참고주소**  
- java logback configuration  
http://gubok.tistory.com/440
일자별 백업파일의 보관기관 : <maxHistory>30</maxHistory>

이상입니다. 포스팅을 마치겠습니다.