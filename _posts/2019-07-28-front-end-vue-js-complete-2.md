---
layout: post
current: post
navigation: True
title: Vue.js 완벽가이드 - 2
date: 2019-07-29 12:00:00
tags: [sw-dev-lab]
class: post-template
subclass: 'post tag-sw-dev-lab'
author: proms
cover: assets/images/VUE_JS.jpg
---

# Vue.js 완벽가이드 - 2

> **5. 라우터 기본**  

## 5. 라우터 기본

## 5-1. 라우터 설치 및 구현  
터미널을 열어 `npm i vue-router --save`를 입력해봅시다.  
`package.json`에서 `dependencies`항목에 `vue-router`을 확인 할 수 있습니다.  

`main.js` 코드 내 router을 추가할 수 있습니다.
~~~js
import Vue from 'vue'
import App from './App.vue'
import VueRouter from 'vue-router';

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
}).$mount('#app')

const router = new VueRouter({
  routes: [

  ]
})

new Vue({
  render: h => h(App),
  router,
}).$mount('#app')
~~~
그러나 main 파일은 설정과 관련된 코드만 작성하는 것이 좋습니다.  

라우터만 관리하는 파일을 생성해보겠습니다.  
`./src/router/index.js` 입니다.  
`index.js` 코드입니다.  
~~~js
import Vue from 'vue'
import VueRouter from 'vue-router';

Vue.use(VueRouter);

const router = new VueRouter({
    routes: [
        {
            // URL주소
            path: '',
            // URL주소로 갔을 때 표시될 컴포넌트
            component: '',
        },
        {
            path: '',
            component: '',
        },
        {
            path: '',
            component: '',
        },
    ]
})
~~~

그리고 다음 파일을 생성하여 컴포넌트를 구성하겠습니다.  
`./src/views/AskView.vue`  
`./src/views/JobsView.vue`  
`./src/views/NewsView.vue`  

그리고 `index.js`를 수정합니다.  
~~~js
import Vue from 'vue'
import VueRouter from 'vue-router';
import NewsView from '../views/NewsView.vue';
import JobsView from '../views/JobsView.vue';
import AskView from '../views/AskView.vue';

Vue.use(VueRouter);

export const router = new VueRouter({
    routes: [
        {
            // URL주소
            path: '/news',
            // URL주소로 갔을 때 표시될 컴포넌트
            component: NewsView,
        },
        {
            path: '/ask',
            component: AskView,
        },
        {
            path: '/jobs',
            component: JobsView,
        },
    ]
})
~~~

그리고 `main.js`를 수정합니다.  
~~~js
import Vue from 'vue'
import App from './App.vue'
import { router } from './router/index.js';

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
  router,
}).$mount('#app')

// const router = new VueRouter({
//   routes: [

//   ]
// })
~~~

## 5-2. router-view를 이용한 라우팅 컴포넌트  
