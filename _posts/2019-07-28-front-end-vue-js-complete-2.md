---
layout: post
title: 'Vue.js 완벽가이드 - 2'
author: kjham.ham
date: 2019-07-29 12:00
tags: [swtech,java,vuejs,frontend]
image: /files/covers/VUE_JS.jpg
comments: true
---

# 2. 라우터 기본

## 2-1. 라우터 설치 및 구현  
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

## 2-2. router-view를 이용한 라우팅 컴포넌트  
`App.vue`에 `router-view`를 추가합니다.  
그럼 router변경에 따라 화면에 컴포넌트가 표기됩니다.  
~~~js
<template>
  <div id="app">
    <router-view></router-view>
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

`http://localhost:8082/#/jobs`  
`http://localhost:8082/#/news`  
`http://localhost:8082/#/ask`  
주소에 따라 생성한 컴포넌트가 표기되는 것을 확인할 수 있습니다.

## 2-3. redirect 속성과 router-link
URL 입력이 아니라 링크에 의해 페이지 변경을 다루어보겠습니다.  

헤더를 생성하기 위해 `ToolBar.vue` 를 정의하겠습니다.  
`router-link`를 이용하여 URL을 정의합니다.  
~~~js
<template>
    <div>
        <router-link to="/news">News</router-link>
        <router-link to="/ask">Ask</router-link>
        <router-link to="/jobs">Jobs</router-link>
    </div>
</template>

<script>
export default {

}
</script>

<style>

</style>
~~~

`ToolBar.vue`를 App.vue에 적용합니다.  
Vue에서 태그를 입력할 때는 Pascal 방식보다는 '-'로 이루어진 태그를 사용합니다.  
~~~js
<template>
  <div id="app">
    <!-- Pascal 방식의 Tag 명명법 -->
    <!-- 1 - Strongly Recommended -->
    <!-- <ToolBar></ToolBar> -->

    <!-- Vue에서 제공하는 자동완성 -->
    <!-- 2 - Essential -->
    <tool-bar></tool-bar>    
    <router-view></router-view>
  </div>
</template>

<script>
import ToolBar from './components/ToolBar.vue';

export default {
  components: {
    ToolBar,
  },
}
</script>

<style>
</style>
~~~

저장 후, 화면에서 각 URL로 이동할 수 있는 Header가 생긴 것을 확인할 수 있습니다.  

## 2-4. ToolBar와 Router Link 스타일링  
Styling은 주로 Web에 화면을 띄우고 [F12]를 클릭하여 Element 요소 값을 수정하며 확인합니다.  

먼저 `Toolbar.vue`에 정의한 header를 수정하겠습니다.  
Header의 기존적인 색감과 각 링크가 클릭되었을 때 스타일링을 주었습니다.  
~~~js
<template>
    <div class="header">
        <router-link to="/news">News</router-link> |
        <router-link to="/ask">Ask</router-link> |
        <router-link to="/jobs">Jobs</router-link>
    </div>
</template>

<script>
export default {

}
</script>

// scoped는 해당 Component만 정의되도록 합니다.
<style scoped>
.header {
    color: white;
    background-color: #42b883;
    display: flex;
    padding: 8px;
}
.header .router-link-exact-active {
    color: #35495e
}
.header a {
    color: white
}
</style>
~~~

`App.vue`에 Padding을 줍니다.  
~~~js
<template>
  <div id="app">
    <!-- Pascal 방식의 Tag 명명법 -->
    <!-- 1 - Strongly Recommended -->
    <!-- <ToolBar></ToolBar> -->

    <!-- Vue에서 제공하는 자동완성 -->
    <!-- 2 - Essential -->
    <tool-bar></tool-bar>    
    <router-view></router-view>
  </div>
</template>

<script>
import ToolBar from './components/ToolBar.vue';

export default {
  components: {
    ToolBar,
  },
}
</script>

<style>
body {
  padding: 0;
  margin: 0;
}
</style>
~~~