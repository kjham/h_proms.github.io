---
layout: post
current: post
navigation: True
title: Vue.js 시작하기 - 1
date: 2019-07-23 12:00:00
tags: [sw-dev-lab]
class: post-template
subclass: 'post tag-sw-dev-lab'
author: proms
---

# Vue.js 시작하기 - 1

>**1. 개발환경 설정**  
1-1. Vue.js 설치하기  
1-2. Plug-In 설치하기  

> **2. Vue 소개**  
2-1. Vus는 무엇인가?  
2-2. 기존 웹 개발 방식  
2-3. Reactivity 구현  

## 1. 개발환결 설정
## 1-1. Vue.js 설치하기

- Chrome
- Visual studio code
- Node.js
- Vue.js devtools

## 1-2. Plug-In 설치하기

- Vetur
- Night Owl
- Material Icon Theme
- Live Server
- ESLint
- Prettier
- Auto Close Tag
- Atom Keymap

## 2-1. Vue는 무엇인가?

MVVM 패턴의 ViewModel Layer에 해당하는 View단 라이브러리  
> DOM Listeners
View에서 DOM에 있는 Event를 수신하여 DOM Listeners를 통해 Model에 전달  

> Data Bindings
Model 에서 Business Logic을 처리후 Data Bindings를 통해 View에 전달

## 2-2. 기존 웹 개발 방식

- Auto Close Tag 플러그인으로 태그 작성 간편화
- ! + [Tab키] 를 누르면 기본적인 템플릿이 자동 작성 됨
- div#app 으로 입력 시, <dev id="app"></div> 로 자동 완성 됨

HTML + Javascript 방식으로 수행
~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app"></div>
    
    <script>
        var div = document.querySelector('#app')
        var str = 'hello world'
        div.innerHTML = str;
        // str이 변경되었을 때, div.innerHTML을 다시 호출해야 함
        str = 'hello world !!!';
        div.innerHTML = str;        
    </script>
</body>
</html>
~~~

## 2-3. Reactivity 구현

Vue.js 의 핵심 기능 중 하나인 Reactivity를 이용하여 실습 수행  
`Object.defineProperty(대상 객체, 객체의 속성, { 정의 내용})`  
즉, defineProperty를 이용하면 데이터를 실시간으로 동적 데이터를 화면에 반영 가능  

다음과 같이 함수를 이용하여 구현 가능
~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app"></div>
    
    <script>
        var div = document.querySelector('#app')
        var viewModel = {};

        // 즉시 실행 함수
        (function() {
            function init() {
            Object.defineProperty(viewModel, 'str', {
                // 속성에 접근했을 때의 동작을 정의
                get: function() {
                    console.log('접근');
                },
                // 속성에 값을 할당했을 때의 동작을 정의
                set: function(newValue) {
                    console.log('할당', newValue);
                    render(newValue);
                }
            });
            }

            function render(value) {
                div.innerHTML = value;
            }

            init();
        })();
        
    </script>
</body>
</html>
~~~