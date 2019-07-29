---
layout: post
current: post
navigation: True
title: Vue.js 완벽가이드 - 1
date: 2019-07-28 12:00:00
tags: [sw-dev-lab]
class: post-template
subclass: 'post tag-sw-dev-lab'
author: proms
---

# Vue.js 완벽가이드 - 1

> **1. 강의 소개**  

> **2. 앱 소개 및 가이드**  

> **9. 최종 정리**  

## 1. 강의 소개  

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

## 2. 앱 소개 및 가이드  

### 2-1. 개발 환경
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

### 2-2. 스타일 가이드 & 코딩 컨벤션  
Vue.js 공식 문서에 있는 스타일 가이드 및 코딩 컨벤션을 참고하여 앱을 제작합니다.  
Vue.js의 Core팀에서 제공하는 코드 스타일을 제공합니다.  

## 3. 애플리케이션 제작 : 소개 및 설계  

Haker News 의 OpenAPI인 HNPWA API를 이용하여 페이지를 작성합니다.  
Ask, News, Jobs 의 세 페이지를 작성하고, 사용자 정보를 확인할 수 있도록 구성합니다.  

### 3-1. 라우터 설계  
다음과 같이 다섯 개의 라우터로 구성합니다.  
- Ask 페이지  
- News 페이지  
- Jobs 페이지  
- User 페이지  
- Ask 코멘트 페이지  

### 3-2. Vue-CLI 2.x vs 3.x  
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

## 4. 프로젝트 Setup

`vue create vue-news` 를 입력하여 프로젝트를 생성합니다.  
옵션은 Default를 선택합니다.  

### 4-1. ESLint 도구 소개  


