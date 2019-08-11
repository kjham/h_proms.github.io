---
layout: post
title: 'Vue.js 시작하기 - 4'
author: kjham.ham
date: 2019-07-26 12:00
tags: [swtech,java,vue-js,front-end]
image: /files/covers/VUE_JS.jpg
---

# 템플릿 문법 & Vue-CLI

# 7. 템플릿
뷰의 템플릿 문법이란 뷰로 화면을 조작하는 방법을 의미합니다.  
템플릿 문법은 크게 데이터 바인딩과 디렉티브로 나누어집니다.  

> **데이터 바인딩**
> 데이터 바인딩은 뷰 인스턴스에서 정의한 속성들을 화면에 표시하는 방법입니다.  
> 가장 기본적인 데이터 바인딩 방식은 콧수염 괄호(Mustache Tag)입니다.  
> ~~~html
> <div>{{ message}}</div>
> new Vue({
> 　message = 'hello world'
> })
> ~~~

> **디렉티브**
> 디렉티브는 뷰로 화면의 요소를 더 쉽게 조작하기 위한 문법입니다.  
> 화면 조작에서 자주 사용되는 방식들을 모아 디렉티브 형태로 제공하고 있습니다.  
> 예를 들어 아래와 같이 특정  속성 값에 따라 화면의 영역을 표시하거나 표시하지 않을 수 있습니다.  
> 속성 중 v- 로 붙어있는 것을 뜻하기도 합니다.  
>
> ~~~html
> <div>
> 　Hello <span v-if="show">Vue.js</span>
> </div>
> new Vue({
> 　data: {
> 　　show : false
> 　}
> })
> ~~~

## 7-1. 데이터 바인딩과 computed 속성
computed 속성은 계산된 속성을 뜻합니다.  
다음 코드를 보면 num 의 값이 바뀔 때마다 computed의 doubleNum에 의해 계산된 값이 갱신되는 것을 확인할 수 있습니다.  
~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=<device-width>, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <p>{{ num }}/p>
        <p>{{ doubleNum }}</p>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>
        new Vue({
            el: '#app',
            data: {
                num: 10,
                // doubleNum: 10 * 2
            },
            // 계산된 속성을 뜻함
            computed: {
                doubleNum: function() {
                    return this.num * 2;
                }
            }
        });
    </script>
</body>
</html>
~~~

## 7-2. 뷰 디렉티브와 v-bind
v-bind를 태그에 속성으로 입력하여 인스턴스에 정의된 data 정보를 바인딩할 수 있습니다.  
v-if를 이용하면, 인스턴스에 정의된 data에 따라 if-else문에 의한 동작으로 DOM에 태그로  정의가 가능합니다.  
v-show는 v-if에서 조건이 아닌 것도 화면에 보이지는 않지만 DOM 태그에 보이도록 표기가 가능합니다..  
~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=<device-width>, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <p v-bind:id="uuid" v-bind:class="name">{{ num }}</p>
        <p>{{ doubleNum }}</p>
        <!-- if-else문을 표기하는 예 -->
        <div v-if="loading">
            Loading...
        </div>
        <div v-else>
            test user has been logged in
        </div>
        <!-- v-show를 지정하면 DOM 상에 조건이 아닌 태그도 남아있음 -->
        <div v-show="loading">
            Loading...
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        new Vue({
            el: '#app',
            data: {
                num: 10,
                uuid: 'abc1234',
                name: 'text-blue',
                loading: true
            },
            // 계산된 속성을 뜻함
            computed: {
                doubleNum: function() {
                    return this.num * 2;
                }
            }
        });
    </script>
</body>
</html>
~~~

## 7-3. 공식문서 보는 방법
input 기능을 추가해보려고 합니다.  
Vue가 없을 때는 다음과 같은 방식으로 했었습니다.  
~~~shell
# DOM에 직접적으로 input을 생성하는 방식
var input = document.querySelector('input');
# JQuery를 이용하여 input을 생성하는 방식
var input = $('input')
~~~
Vue에서는 어떻게 input을 생성하는지 궁금할 때 보통 공식문서를  참고합니다.  
"https://vuejs.org" 에 접속하여 input을 검색합니다.  
그럼 친절하게도 샘플링을 확인 가능하고, 코드게 쉽게 적용할 수 있습니다.  
다음은 이를 참고하여 작성한 코드입니다.  
~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=<device-width>, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <!-- TODO: 인풋 박스를 만들고 입력된 값을 p 태그에 출력해보세요 -->
        <!-- input에 사용 방법을 모를 때, "https://vuejs.org"의 검색을 이용하면 쉽게 예제를 확인할 수 있습니다. -->
        <input type="text" v-model="message" placeholder="edit me">
        <p>Message is: {{ message }}</p>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        // DOM에 직접적으로 input을 생성하는 방식
        // -> var input = document.querySelector('input');
        // JQuery를 이용하여 input을 생성하는 방식
        // -> var input = $('input')

        new Vue({
            el: '#app',
            data: {
                message: ''
            }
        });
    </script>
</body>
</html>
~~~

## 7-4. methods와 키보드, 마우스 이벤트 처리
메소드는 인스턴스 내 정의할 수 있습니다.  
정의한 메소드를 v-on 을 이용하여 정의한 이벤트에 호출되는 코드를 작성해보겠습니다.  
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
    <div id="app">
        <!-- 클릭 이벤트 발생 시 메소드 호출 방식 -->
        <button v-on:click="logText">click me</button>
        <!-- 키보드 Up 이벤트 발생 시 메소드 호출 방식 -->
        <input type="text" v-on:keyup="logText">
        <!-- 키보드 이벤트 중 Enter 키에 의해서만 메소드 호출 방식 -->
        <input type="text" v-on:keyup.enter="logText">
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        new Vue({
            el: '#app',
            methods: {
                logText: function() {
                    console.log('clicked');                    
                }
            }
        })
    </script>
</body>
</html>
~~~

## 7-5. watch
watch는 데이터의 변화에 따라 특정 로직이 동작하도록 정의할 수 있습니다.  
공식 문서에서는 watch 보다는 computed를 이용하는 것도 좋다고 합니다.  
캐싱과 같은 처리로 더 빠른 연산 처리가 가능하기 때문입니다.  
즉, 간단한 코드들은 watch를 사용하는 행위를 피하라는 의미입니다.  
watch의 경우는 무겁도, 복잡한 메소드나 axios같은 데이터 요청에 의한 동작을 정의할 때 사용하는 것을 권장합니다.  
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
  <div id="app">
    {{ num }}
  </div>

  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script>
    new Vue({
      el: '#app',
      data: {
        num: 10
      },
      computed: {
        // 단순한 값에 의한 계산이나 Validation에 사용
        doubleNum: function() {
          return this.num * 2;
        }
      },
      watch: {
        // 무거운 동작들 정의 (데이터 요청과 같은 행위)
        num: function(newValue, oldValue) {
          this.fetchUserByNumber(newValue);
        }
      },
      methods: {
        fetchUserByNumber: function(num) {
          console.log(num);
          // axios.get(num);
        }
      }
    });
  </script>
</body>
</html>
~~~

## 7-6 . computed를 이용한 클래스 코드 작성 방법
v-bind:class 에 Vue 인스턴스 내 computed 속성을 이용하여 바인딩 처리를 할 수 있습니다.  
Vue 인스턴스 내 data에 선언된 값으로 v-bind:class에 정의 내용을 바인딩 처리로 바꾸는 코드는 다음과 같습니다.  
~~~html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <style>
  .warning {
    color: red;
  }
  </style>
</head>
<body>
  <div id="app">
    <p v-bind:class="errorTextColor">Hello</p>
  </div>
  
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script>
    new Vue({
      el: '#app',
      data: {
        isError: false
      },
      computed: {
        errorTextColor: function() {
          return this.isError ? 'warning' : null;
        }
      }
    });
  </script>
</body>
</html>
~~~

# 8. Vue CLI
CLI (Command Line Interfase) 는 명령어 실행을 위한 인터페이스 역할을 합니다.  

## 8-1. Vue CLI 설치
다음과 같이 버전을 확인 후 설치를 진행해봅니다.  
~~~shell
# node.js 버전 확인
$ node -v
v10.16.0
# npm 버전 확인
$ npm -v
6.9.0
# vue cli 설치
$ npm install -g @vue/cli
...
added 840 packages from 545 contributors in 46.242s
~~~

권한이 없을 경우는 다음과 같이 합니다.  
`sudo npm install -g @vue/cli`

설치 위치는 다음과 같습니다.   
- Windows 기준  
`%USERPROFILE%\AppData\Roaming\npm\node_modules`  
- Mac 기준  
`usr/local/lib/node_modules`  

## 8-2. 프로젝트 생성 및 실행
> **Vue CLI 2.x**
vue init '프로젝트 템플릿 유형' '프로젝트 폴더 위치'  
vue init 'webpack-simple' '프로젝트 폴더 위치'  

> **Vue CLI 3.x**
vue create '프로젝트 폴더 위치'  

~~~shell
# 프로젝트 생성 명령어
$ vue create vue-cli
Vue CLI v3.9.3
# default 프로젝트 선택
? Please pick a preset: default (babel, eslint)
...
...
�  Get started with the following commands:
...
# vue-cli 폴더로 이동
$ cd vue-cli
# vue-cli 프로젝트 실행
$ npm run 
...
App running at:
- Local:   http://localhost:8080/
- Network: http://192.168.103.1:8080/
Note that the development build is not optimized.
To create a production build, run npm run build.
...
# 실행까지 완료되었습니다. 위 주소로 접속하여 화면을 확인해봅니다.
~~~

## 8-3. 프로젝트 폴더 구조
- package.json  
npm run serve에서의 serve는 package.json내에 정의되어 있어서 호출이 가능한겁니다.  
scripts: { } 안에 정의되어 있습니다.  

- index.html  
~~~js
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title>vue-cli</title>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but vue-cli doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
    <!-- 즉, src 폴더 하위에 정의되어 있는 것이 자동으로 추가됨을 의미합니다 -->
  </body>
</html>
~~~

- main.js  
~~~js
import Vue from 'vue'
// Component 파일을 import 한겁니다.
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
}).$mount('#app')

// 기존에 하던 방식으로 표현하면 아래와 같습니다.
// new Vue({
//   el: 'app',
//   // import 한 컴포넌트 파일을 render함수에 전달한겁니다.
//   render: h =>(app)
// });
~~~

## 8-4. 싱글 파일 컴포넌트 소개
Vue 파일을 하나 생성하였습니다.  
Vue 파일은 HTML, JavaScript, CSS의 영역을 모두 합칠 수 있다고 볼 수 있습니다.  
가장 하단에 지금까지 해왔던 구조를, 상단에 있는 template, script, style 에 정의한 것을 볼 수 있습니다.
~~~js
<template>
    <!-- HTML -->
    <div>header</div>
</template>

<script>
export default {
    // JAVA Script - 인스턴스 옵션
    methods: {
        addNum: function() {

        }
    }
}
</script>

<style>
    /* CSS */
</style>

// 지금까지 해왔던 구조
// var appHeader = {
//     template: '<div>header</div>',
//     method: {
//         addNum: function() {

//         }
//     }
}
~~~

## 8-5. App.vue와 HelloWorld.vue
App.vue 코드를 살펴봅니다.  
HelloWorld.vue를 import 하며 HelloWorld 로 정의한 것을 확인할 수 있습니다.  
그리고 HelloWorld 컴포넌트의 msg 속성에 'Welcome to Your Vue.js App'라고 template 내에 정의했습니다.  
HelloWorld.vue 컴포넌트를 가면 template에 msg 를 출력하는 코드가 있습니다.  
따라서 최종적으로 화면에 이와 관련된 문자열이 출력된는 것을 확인할 수 있습니다.
~~~js
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>

    <!-- 컴포넌트 명령법 종류는 다음 세 가지가 있습니다 -->
    <!-- 
    <hello-world></hello-world>
    <HelloWorld></HelloWorld>
    <HelloWorld /> 
    -->
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  // 인스턴스 옵션 속성 or 컴포넌트 옵션 속성
  name: 'app',
  components: {
    HelloWorld
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