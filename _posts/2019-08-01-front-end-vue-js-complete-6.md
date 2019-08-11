---
layout: post
title: 'Vue.js 완벽가이드 - 6'
author: kjham.ham
date: 2019-08-01 17:00
tags: [swtech,java,vue-js,front-end]
image: assets/images/VUE_JS.jpg
---

# 6. 리팩토링 : 스타일링, 공통 컴포넌트  

리스트 아이템 컨포넌트 공통화를 위한 리팩토링을 진행합니다.  

## 6-1. 컴포넌트 공통화 리팩토링 소개  
AskView 컴포넌트 하나에 모든 데이터를 출력하였지만,  
각 항목을 Component로 구성하여 리스트화를 작업하려 합니다.  
이를 위한 공통 Component를 생성합니다.  

## 6-2. News 리스트 스타일링  
`NewsView.vue` 스탕일링을 합니다.  
각 항목을 리스트로 표기합니다.  
그리고 스타일링이 필요한 Tag에 클래스명을 지정하여 개별적인 스타일링을 수행합니다.  
~~~vue
<template>
    <div>
        <ul class="news-list">
            <li v-for="item in this.$store.state.news" class="post">
                <!-- 포인트 영역 -->
                <div class="points">
                    {{ item.points }}
                </div>
                <!-- 기타 정보 영역 -->
                <div>
                    <p class="news-title">
                        <a v-bind:href="item.url">
                            {{ item.title }}
                        </a>
                    </p>
                    <small class="link-text">
                        by
                        <router-link v-bind:to="`/user/${item.user}`" class="router-link">
                            {{ item.user }}
                        </router-link>
                    </small>
                </div>
            </li>
        </ul>
    </div>
</template>

<script>
export default {
    created() {      
        this.$store.dispatch('FETCH_NEWS');
    }
}
</script>

<style scoped>
.news-list {
    margin: 0px;
    padding: 0px;
}
.post {
    list-style: none;
    display: flex;
    align-items: center;
    border-bottom: 1px solid #eee;
}
.points {
    margin: 20px;
    width: 80;
    height: 60;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #42b883;
}
.news-title {
    margin: 0;
}
.link-text {
    color: #828282
}
.router-link {
    color: #828282
}
</style>
~~~

`App.vue`에 공통적인 스타일링을 추가합니다.  
~~~vue
<template>
  <div id="app">
    <tool-bar></tool-bar>    
    <transition name="page">
      <router-view></router-view>
    </transition>
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
a {
  color: #34495e;
  text-decoration: none;  
}
a:hover {
  color: #42b883;
  text-decoration: underline;
}
a.router-link-exact-active {
  text-decoration: underline;
}

/* Router Transition */
.page-enter-active, .page-leave-active {
  transition: opacity .5s;
}
.page-enter, .page-leave-to {
  opacity: 0;
}
</style>
~~~

## 6-3. Ask, Jobs 리스트 스타일링  
Ask와 Jobs도 스타일링 작업을 합니다.  
`AskView.vue`에 스타일링을 적용합니다.  
~~~vue
<template>
    <div>
        <ul class="news-list">
            <li v-for="item in fetchedAsk" class="post">
                <!-- 포인트 영역 -->
                <div class="points">
                    {{ item.points }}
                </div>
                <!-- 기타 정보 영역 -->
                <div>
                    <p class="news-title">
                        <router-link v-bind:to="`/item/${item.id}`"> 
                            {{ item.title }}
                        </router-link>
                    </p>
                    <small class="link-text">
                        {{ item.time_ago}} by
                        <router-link v-bind:to="`/user/${item.user}`"> 
                            {{ item.user }}
                        </router-link>
                    </small>
                </div>
            </li>
        </ul>
    </div>
</template>

<script>
import { mapState, mapGetters } from 'vuex';

export default {
    computed: {
        ...mapGetters([
            'fetchedAsk'
        ]),
    },
    created() {
        this.$store.dispatch('FETCH_ASK');
    }
}
</script>

<style scoped>
.news-list {
    margin: 0px;
    padding: 0px;
}
.post {
    list-style: none;
    display: flex;
    align-items: center;
    border-bottom: 1px solid #eee;
}
.points {
    margin: 20px;
    width: 80;
    height: 60;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #42b883;
}
.news-title {
    margin: 0;
}
.link-text {
    color: #828282
}
.router-link {
    color: #828282
}
</style>
~~~

`JobsView.vue`에도 스타일링을 적용합니다.  
~~~vue
<template>
    <div>
        <ul class="news-list">
            <li v-for="job in this.$store.state.jobs" class="post">
                <!-- 포인트 영역 -->
                <div class="points">
                    {{ job.points || 0}}
                </div>
                <!-- 기타 정보 영역 -->
                <div>
                    <p class="news-title">
                        <a :href="job.url">
                            {{ job.title }}
                        </a>
                    </p>
                    <small class="link-text">
                        {{ job.time_ago}} by
                        <a :href="job.url"> 
                            {{ job.domain }}
                        </a>
                    </small>
                </div>
            </li>
        </ul>
    </div>
</template>

<script>
export default {    
    created() {
        this.$store.dispatch('FETCH_JOBS');
    }
}
</script>

<style scoped>
.news-list {
    margin: 0px;
    padding: 0px;
}
.post {
    list-style: none;
    display: flex;
    align-items: center;
    border-bottom: 1px solid #eee;
}
.points {
    margin: 20px;
    width: 80;
    height: 60;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #42b883;
}
.news-title {
    margin: 0;
}
.link-text {
    color: #828282
}
.router-link {
    color: #828282
}
</style>
~~~

## 6-4. 공통 컴포넌트 ListItem 제작  
`ListItem.vue`를 정의하여 공통 컴포넌트를 생성합니다.  

먼저 `routes/index.js`의 각 항목에 `name`을 정의합니다.  
그리고 공통 컴포넌트 내 `created()` 에 router name으로 분기처리하여 각 View에 해당하는 `$store` 데이터를 요청합니다.  
그리고 `computed`에 마찬가지로 분기를 태워 `template`에 전달할 데이터를 반환하도록 합니다.  
`ListItem.vue` 코드입니다.  
~~~vue
<template>
    <div>
        <ul class="news-list">
            <li v-for="item in listItems" class="post">
                <!-- 포인트 영역 -->
                <div class="points">
                    {{ item.points }}
                </div>
                <!-- 기타 정보 영역 -->
                <div>
                    <p class="news-title">
                        <a v-bind:href="item.url">
                            {{ item.title }}
                        </a>
                    </p>
                    <small class="link-text">
                        {{ item.time_ago }} by
                        <router-link v-bind:to="`/user/${item.user}`" class="router-link">
                            {{ item.user }}
                        </router-link>
                    </small>
                </div>
            </li>
        </ul>
    </div>
</template>

<script>
export default {
    created() {
        // 라우터에 설정한 name에 따른 분기
        const name = this.$route.name;
        var actionName = '';
        if (name ==='news') {
            actionName = 'FETCH_NEWS';
        } else if (name ==='ask') {
            actionName = 'FETCH_ASK';
        } else if (name ==='jobs') {
            actionName = 'FETCH_JOBS';
        }
        this.$store.dispatch(actionName);        
    },
    computed: {
        listItems() {
            const name = this.$route.name;
            if (name ==='news') {
                return this.$store.state.news;
            } else if (name ==='ask') {
                return this.$store.state.ask;
            } else if (name ==='jobs') {
                return this.$store.state.jobs;
            }
        }
    }
}
</script>

<style scoped>
.news-list {
    margin: 0px;
    padding: 0px;
}
.post {
    list-style: none;
    display: flex;
    align-items: center;
    border-bottom: 1px solid #eee;
}
.points {
    margin: 20px;
    width: 80;
    height: 60;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #42b883;
}
.news-title {
    margin: 0;
}
.link-text {
    color: #828282
}
.router-link {
    color: #828282
}
</style>
~~~

그리고 각 `View.vue`에 공통 컴포넌트를 적용합니다.  
`AskView.vue` 입니다.  
~~~vue
<template>
    <list-item></list-item>
</template>

<script>
import ListItem from '../components/ListItem.vue';

export default {
    components: {
        ListItem,
    },
}
</script>

<style>
</style>
~~~
`NewsView.vue` 입니다.  
~~~vue
<template>
    <list-item></list-item>
</template>

<script>
import ListItem from '../components/ListItem.vue'

export default {
    components: {
        ListItem,
    },
}
</script>

<style>
</style>
~~~
`JobsView.vue` 입니다.  
~~~vue
<template>
    <div>
        <list-item></list-item>
    </div>
</template>

<script>
import ListItem from '../components/ListItem';

export default {   
    components: {
        ListItem,
    },
}
</script>

<style>
</style>
~~~

News, Ask는 문제없이 잘 동작하지만, Job는 몇몇 데이터가 누락된 것을 확인할 수 있습니다..  

## 6-5. template 속성과 v-if 활용  
`v-if`를 이용하여 Tag 분기 처리를 수행하도록 하겠습니다.  
`ListItem.vue`에 다음과 같이 `v-if` 코드를 적용합니다.  
~~~vue
<template>
    <div>
        <ul class="news-list">
            <li v-for="item in listItems" class="post">
                <!-- 포인트 영역 -->
                <div class="points">
                    {{ item.points || 0 }}
                </div>
                <!-- 기타 정보 영역 -->
                <div>
                    <!-- 타이틀 영역 -->
                    <p class="news-title">
                        <template v-if="item.domain">
                            <a v-bind:href="item.url">
                                {{ item.title }}
                            </a>
                        </template>
                        <template v-else>
                            <router-link v-bind:to="`item/${item.id}`"> 
                                {{ item.title }}
                            </router-link>                            
                        </template>
                    </p>
                    <small class="link-text">                        
                        {{ item.time_ago }} by
                        <router-link 
                            v-if="item.user"
                            v-bind:to="`/user/${item.user}`" class="router-link">
                            {{ item.user }}
                        </router-link>
                        <a :href="item.url" v-else>
                            {{ item.domain }}
                        </a>
                    </small>
                </div>
            </li>
        </ul>
    </div>
</template>

<script>
export default {
    created() {
        // 라우터에 설정한 name에 따른 분기
        const name = this.$route.name;
        var actionName = '';
        if (name ==='news') {
            actionName = 'FETCH_NEWS';
        } else if (name ==='ask') {
            actionName = 'FETCH_ASK';
        } else if (name ==='jobs') {
            actionName = 'FETCH_JOBS';
        }
        this.$store.dispatch(actionName);        
    },
    computed: {
        listItems() {
            const name = this.$route.name;
            if (name ==='news') {
                return this.$store.state.news;
            } else if (name ==='ask') {
                return this.$store.state.ask;
            } else if (name ==='jobs') {
                return this.$store.state.jobs;
            }
        }
    }
}
</script>

<style scoped>
.news-list {
    margin: 0px;
    padding: 0px;
}
.post {
    list-style: none;
    display: flex;
    align-items: center;
    border-bottom: 1px solid #eee;
}
.points {
    margin: 20px;
    width: 80;
    height: 60;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #42b883;
}
.news-title {
    margin: 0;
}
.link-text {
    color: #828282
}
.router-link {
    color: #828282
}
</style>
~~~

현재까지 작성된 코드로 다음과 같이 만들어진 것을 확인할 수 있습니다.  
![News Page](/assets\images\VUE_JS_COMPLETE_01.jpg){: .center-image }  
![Ask Page](/assets\images\VUE_JS_COMPLETE_02.jpg){: .center-image }  
![Jobs Page](/assets\images\VUE_JS_COMPLETE_03.jpg){: .center-image }  