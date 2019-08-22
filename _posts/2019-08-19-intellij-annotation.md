---
layout: post
title: 'IntelliJ 주석 자동완성'
author: kjham.ham
date: 2019-08-22 11:00
tags: [swtech,ide,intellij]
---

Back-End 개발을 수행하면 주석을 자동으로 완성하여 넣어야 할 일이 많이 있습니다.  
클래스나 메소드의 용도 및 히스토리를 넣는 것이지요.  

IntelliJ 에서 이를 수행하기 위한 세팅 방법을 알아봅니다.  

[File]-[Setting]를 선택하거나,  
[Ctrl] + [Alt] + [S] 단축키를 누릅니다.  

`Live Template`를 검색합니다.  

우측에 [+]를 클릭하여 [Template Group]를 추가합니다.  
저는 `Java` 로 생성하였습니다.  

그리고 `Java` 그룹을 선택 후, 다시 [+]를 클릭하여 [Live Templates]를 선택합니다.  

다음과 같이 정의하였습니다.  
`Abbreviation : cmtClass`  
`Description : 클래스 주석`  
`Template text : `  
~~~java
/**
* <pre>
* $package$
* ㄴ $class$ 
* </pre>
* @date : $date$ $time$
* @author : 함건주
* @version : 1.0.0
* @see : 
**/
~~~

그리고 하단에 `change` 를 클릭하여 `Jva` 그룹을 선택합니다.  

마지막으로 `Edit Variables`를 선택하여 정의한 변수에 매칭할 `Expression`를 적절하게 선택합니다.  
저는 다음과 같이 `Expression`을 선택했습니다.  
`$package$ : currentPackage()`  
`$class$ : fileName()`  
`$date$ : date()`  
`$time$ : time()`  

그리고 `class` 파일로 이동하여 주석을 자동완성 해보겠습니다.  
`Abbrevation`에 정의한 키워드를 입력하면 자동완성 목록이 나타나는데  
여기서 cmtClass를 선택하면 됩니다.  
~~~java
/**
* <pre>
* com.wv.iss.common.controller.humanResourceSync
* ㄴ humanResourceSyncController.java 
* </pre>
* @date : 2019-08-22 오전 11:38
* @author : 함건주
* @version : 1.0.0
* @see : 
**/
~~~

적용이 잘 되는 것을 확인했습니다.  
위와 같은 방식으로 작성하여 Methods타입의 템플릿도 정의하여 사용하면 됩니다.  