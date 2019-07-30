---
layout: post
current: post
navigation: True
title: (Java) Gradle Dependency
date: 2018-03-30 17:45:00
tags: [sw-dev-lab]
class: post-template
subclass: 'post tag-sw-dev-lab'
author: proms
---

**JAVA - Gradle Dependency**

- 로컬 라이브러리 dependency에 포함시키는 법  
프로젝트 root 경로에 lib 폴더 생성 후, 하위에 jar 를 넣습니다.  
그리고 아래와 같이 fileTree 를 활용하면, maven 이나 별도의 repository 없이 jar로 넣을 수 있습니다.  
dependencies {
   compile fileTree (dir: 'lib', include:['**/*.jar'])
}

- 뚱뚱한 jar 만들기  
소스코드 외 사용중인 lib를 모두 포함하는 jar를 만들려면?  

// 다음과 같이 shadow plug-in 을 추가  
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.github.jengelman.gradle.plugins:shadow:2.0.3'
    }
}

apply plugin: 'com.github.johnrengelman.shadow'
apply plugin: 'java'
apply plugin: 'java-library'

// 저는 lib 하위에 jar를 모았습니다.  
dependencies {
	compile fileTree (dir: 'lib', include:['**/*.jar'])
}

// jar 명령 수행 시, shadowJar 가 실행되도록 했습니다.  
jar {
    finalizedBy shadowJar
}


- 참고
https://medium.com/@airkjh99/gradle-%EB%A1%9C%EC%BB%AC-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-dependency%EC%97%90-%ED%8F%AC%ED%95%A8%EC%8B%9C%ED%82%A4%EA%B8%B0-46142df6838b
http://blog.leocat.kr/notes/2017/10/11/gradle-shadowjar-make-fat-jar  

- 더 많은 정보 참고  