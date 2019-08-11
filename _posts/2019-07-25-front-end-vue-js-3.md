---
layout: post
title: 'Vue.js 시작하기 - 3'
author: kjham.ham
date: 2019-07-25 17:00
tags: [swtech,java,vue-js,front-end]
image: assets/images/VUE_JS.jpg
---

# 라우터 & Axios

# 5. 라우터
Vue 라우터는 Vue.js의 공식 라우터입니다.  
Vue.js를 사용한 싱글 페이지 앱을 쉽게 만들 수 있도록 Vue.js의 코어와 긴밀히 통합되어 있습니다.

아래의 기능을 포함합니다.  
- 중첩된 라우트/뷰 매핑  
- 모듈화된, 컴포넌트 기반의 라우터 설정  
- 라우터 파라미터, 쿼리, 와일드카드  
- Vue.js의 트랜지션 시스템을 이용한 트랜지션 효과  
- 세밀한 네비게이션 컨트롤  
- active CSS 클래스를 자동으로 추가해주는 링크  
- HTML5 히스토리 모드 또는 해시 모드(IE9에서 자동으로 폴백)  
- 사용자 정의 가능한 스크롤 동작  

## 5-1. 라우터 코드 정의
new VueRouter({}) 를 이용하여 라우터 인스턴스를 정의할 수 있습니다.  
그리고 new Vue({}) 객체의 router 속성에 인스턴스로 정의한 VueRouter를 부여합니다.  
VueRouter 인스턴스 내 routes 에 정의한 각 Page로 구성된 화면을 확인할 수 있습니다.  
`<router-link to="">Login</route-link>` 는 기존`<a></a>` 태그이며, 사용자에게 링크를 제공하는 역할을 수행합니다.

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
        <div>
            <!-- 기존 앵커태그 : <a href="/login"></a> -->
            <router-link to="/login">Login</router-link>            
            <router-link to="/main">Main</router-link>
        </div>
        <!-- 컴포넌트가 뿌려지는 영역 -->
        <router-view></router-view>
    </div>

    <!-- 순서 중요! Vue를 먼저 넣고, Router를 넣어야합니다. -->
    <!-- 아래는 CDN 방식입니다. -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>

    <script>
        var LoginComponent = {
            template: '<div>login</div>'
        }
        var MainComponent = {
            template: '<div>main</div>'
        }
        var router = new VueRouter({
            // 페이지의 라우팅 정보 (어떤 URI에 어떤 Page가 표현될 지 정의)
            routes: [
                // 로그인 페이지 정보
                {
                    // 페이지의 url
                    path: '/login',
                    // 해당 url에서 표시될 컴포넌트
                    component: LoginComponent
                },
                // 메인 페이지 정보
                {
                    path: '/main',
                    component: MainComponent
                }
            ]
        });

        new Vue({
            el: '#app',
            router: router
        });
    </script>
</body>
</html>
~~~

# 6. HTTP 통신 라이브러리

> **Axios (액시오스)**  
뷰에서 권고하는 HTTP 통신 라이브러리는 Axios입니다.  
Promise 기반의 HTTP 통신 라이브러리이며 상대적으로 다른 HTTP 통신 라이브러리들에 비해 문서화가 잘 되어 있고 API가 다양합니다.

> **Ajax (에이작스)**  
Ajax는 비동기적인 웹 애플리케이션의 제작을 위한 기법입니다.  
이전에는 데이터를 수정하려면 서버에 요청하고 수신받아야했지만, Ajax를 이용하면 비동기적으로 수정이 가능합니다.  
대표적으로 Jquery.ajax() 가 있습니다.

> **Vue Resource**
Vue.js에서 공식 HTTP 통신 라이브러리였지만, 지금은 Axios로 변경되었다.  
Vue Resource 기준의 샘플은 오래된 것일 가능성이 크다는 점을 참고하면 됩니다.

## 6-1.  Axios (액시오스) 
Vue.js의 창시자 애반 유가 Vue Resource에서 Axios를 공식 Http 통신 라이브러리로 변경하였습니다.  

> **오픈소스 활용법**  
> Star 지수, commits 수, contributors 수가 많은 것을 활용하는 것이 좋음  
> Commit 이력을 볼 때 수정일이 비교적 최근인 것들이 있는 것이 좋음

Promise는 Java Script의 비동기 처리 패턴을 의미합니다.  
비동기 처리 패턴은 다음과 같이 변화되었습니다.  
1. callback  
2. promise  
3. promise + generator  
4. async $ await  

## 6-2. Axios 실습
다음 코드는 axios를 통해 서버에 데이터를 요청하여 수신받는 모습을 보여줍니다.  
getData로 표기된 버튼을 클릭했을 때 Json 형식의 데이터를 수신받아 화면에 보여줍니다.  
~~~html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Axios</title>
</head>
<body>
  <div id="app">
    <button v-on:click="getData">get user</button>
    <div>
      {{ users }}
    </div>
  </div>

  <!-- CDN 방식으로 Vue 와 Axios 를 불러옴 -->
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script src="https://unpkg.com/axios/dist/axios.min.js"></script>

  <script>
    new Vue({
      el: '#app',
      data: {
        users: []
      },
      methods: {
        getData: function() { 
          var vm = this;
          axios.get('https://jsonplaceholder.typicode.com/users/')
            .then(function(response) {
              // 성공할 경우 처리 루트
              console.log(response.data);
              vm.users = response.data;
              // call-back 함수 내 의 this는 Vue 객체를 뜻하지 않음
              // this.users = response.data;
            })
            .catch(function(error) {
              // 실패할 경우 처리 루트
              console.log(error);
            });
        }
      }
    })
  </script>
</body>
</html>
~~~

## 6-3. HTTP 통신 구조
1. 브라우저는 서버에 HTTP Request를 수행하여 데이터 송신을 요청합니다.  
2. 서버는 Back-End 로직을 수행합니다.  
3. 서버는 Database에서 필요한 데이터를 조회합니다.  
4. 조회된 데이터로 Back-End 로직을 수행하여 최종 응답 데이터를 생성합니다.  
5. 서버는 HTTP Response로 응답받아 화면에 데이터를 표현합니다.  

## 6-4. 크롬 개발자 도구 네트워크 패널
1. Chrome 브라우저에서 F12 (Windows 기준) 을 누릅니다.  
2. Network 패널을 클릭합니다.  
개발하면서 가장 많이 접하게 되는 패널입니다.  
3. HTTP 통신 작업을 수행할 때, XHR에 수행 이력이 생성됩니다.  
- [Headers]-[General] 에서 URL, METHOD, STATUS CODE를 확인 가능합니다.  
- [Request Headers], [Response Header] 에서도 여러가지 정보 확인이 가능합니다.  
- [Response], [Preview] 에서 수신받은 정보를 확인 가능합니다.  