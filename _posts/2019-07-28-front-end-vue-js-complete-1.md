---
layout: post
title: 'Vue.js 완벽가이드 - 1'
author: kjham.ham
date: 2019-07-28 12:00
tags: [swtech,java,vue-js,front-end]
image: /files/covers/VUE_JS.jpg
---

# 1. 강의소개 & 설치

## 1-1. 강의 소개  

Application을 직접 제작하며 Vue.js의 구조를 이해합니다.  
Application 제작을 위한 실무 학습을 수행합니다.  

1. Vue.js를 이용한 웹 서비스 구현 절차  
2. 실무 프로젝트 진행 방식 (컴포넌트, 라우터, API, 스토어)  
3. 컴포넌트 디자인 패턴과 자바스크립트 비동기 처리  
4. Mixins과 HOC를 이용한 컴포넌트 재활용 방법  
5. 외부 라이브러리 모듈화 및 실무 프로젝트 구성 방법  
6. 사용자 경험을 높이는 라우터 설계 방법과 고급 라우터 패턴  

뉴스정보를 읽어와서 화면에 표기하는 Application을 제작합니다.  
각 페이지를 클릭하여 라우터 처리를 수행하도록 합니다.  
각 항목을 클릭할 때 상세 내용을 읽어와 화면에 표기합니다.  

## 1-2. 개발 환경
개발환경은 다음과 같습니다.  
> Node.js  
> Visual Studio Code (+ Plug-In)  
> > Vetur : 코드 자동완성  
> > TSLint : TypeScript를 사용하지 않아도 타입에 대한 감지를 수행  
> > ESLint :   
> > Auto Close Tag : Tag 자동완성  
> > Live Server : 라이브 서버 실행 지원  
> > Night Owl : 코드 테마  
> 
> GithubGist

## 1-3. 스타일 가이드 & 코딩 컨벤션  
Vue.js 공식 문서에 있는 스타일 가이드 및 코딩 컨벤션을 참고하여 앱을 제작합니다.  
Vue.js의 Core팀에서 제공하는 코드 스타일을 제공합니다.  

## 1-4. 라우터 설계  

Haker News 의 OpenAPI인 HNPWA API를 이용하여 페이지를 작성합니다.  
Ask, News, Jobs 의 세 페이지를 작성하고, 사용자 정보를 확인할 수 있도록 구성합니다.  

다음과 같이 다섯 개의 라우터로 구성합니다.  
- Ask 페이지  
- News 페이지  
- Jobs 페이지  
- User 페이지  
- Ask 코멘트 페이지  

## 1-5. Vue-CLI 2.x vs 3.x  
> **명령어**  
- 2.x : vue init '프로젝트 템플릿 이름' '파일 위치'  
- 3.x : vue create '프로젝트 이름'  

> **Web-Pack 설정 파일**  
- 2.x : 노출 0  
- 3.x : 노출 X  

> **프로젝트 구성**  
- 2.x : Github의 템플릿 다운로드  
- 3.x : 플러그인 기반으로 기능 추가  

> **ES6 이해도** (ES6 : Javacript 문법)  
- 2.x : 필요 X  
- 3.x : 필요 O  

## 1-6. 프로젝트 Setup

`vue create vue-news` 를 입력하여 프로젝트를 생성합니다.  
`default (babel, eslint)` 를 선택합니다.  
`babel` 은 ES6를 변환하기 위한 도구입니다.  
`npm run serve`를 입력하면 프로젝트를 수행할 수 있습니다.  

## 1-7. ESLint 도구 소개  

HelloWorld 컴포넌트 제거 후, 다음과 같이 코드를 생성하여 저정하면 ESLint에 의해 오류가 발생합니다.  
~~~js
<template>
  <div id="app">
    hello
  </div>
</template>

<script>

export default {
  methods: {
    fetchData() {
      console.log('hello');      
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
~~~

다음과 같이 에러 내용이 나타납니다.  
~~~shell
Module Warning (from ./node_modules/eslint-loader/index.js):
error: Unexpected console statement (no-console) at src\App.vue:12:7:
  10 |   methods: {
  11 |     fetchData() {
> 12 |       console.log('hello');
     |       ^
  13 |     }
  14 |   }
  15 | }


1 error found.

You may use special comments to disable some warnings.
Use // eslint-disable-next-line to ignore the next line.
Use /* eslint-disable */ to ignore all warnings in a file.
~~~

ESLint는 기본적인 코딩 스타일을 강제하여 오류를 알려주는 역할을 합니다.  
~~~js
// 자바해석기가 코드 라인 끝에 ';' 를 넣어주기 때문에 문제는 없습니다.
var a = 10;
var a = 10
import Appheader from './components/AppHeader'
import Vue from 'vue'

// 이 경우에도 문제는 없습니다.
if(a == 10) {
	console.log('10이다')
}

// 다음 같은 경우는 자바해석기가 어디에 ';'를 넣어야할 지 모릅니다.
if(a == 1) console.log('10이다') b() c()

// trailing comma
// 컴포넌트를 구성할 때에도 ','를 넣기를 권장하고 있습니다.
components: {
	'컴포넌트 이름': 컴포넌트 내용,
}
~~~

ESLint를 설정을 어떻게 끌 수 있을까요 ?  
1. Component 마다 `/* eslint-disable */` 를 넣는 방법이 있습니다.  
2. 전체 코드에 적용하는 방법은 다음과 같습니다.
1) src 하위에 `vue.config.js` 를 생성합니다.
2) 그리고 다음과 같이 코드를 작성합니다.
~~~js
module.exports = {
    lintOnSave: false
}
~~~