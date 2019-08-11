---
layout: post
title: 'Vue.js 완벽가이드 - 5'
author: kjham.ham
date: 2019-08-01 12:00
tags: [swtech,java,vue-js,front-end]
image: /files/covers/VUE_JS.jpg
---

# 5. 라우터 실전  

## 5-1. User 상세 페이지 구현  
사용자를 클릭했을 때 사용자 상세정보를 확인하는 기능을 추가하겠습니다.  
Dynamic Route Matching을 알아봅니다.  

우선 `api/index.js`에 API를 추가합니다.  
~~~js
import axios from 'axios';

// 1. HTTP Request & Response와 관련된 기본 설정
const config = {
    baseUrl: 'https://api.hnpwa.com/v0/'
}

// 2. API 함수들을 정리
function fetchNewsList() {
    // return axios.get(config.baseUrl + 'news/1.json');
    // ES6 스타일
    return axios.get(`${config.baseUrl}news/1.json`);
}

function fetchJobsList() {
    return axios.get(`${config.baseUrl}jobs/1.json`);
}

function fetchAskList() {
    return axios.get(`${config.baseUrl}ask/1.json`);
}

function fetchUserInfo(name) {
    return axios.get(`${config.baseUrl}user/${name}.json`);
}

export {
    fetchNewsList,
    fetchJobsList,
    fetchAskList,
    fetchUserInfo
}
~~~

그리고 `routes/index.js`에 `/user/:id`를 추가합니다.  
여기서 :id는 Dynamic Route Matching를 이용한 방식입니다.  
~~~js
import Vue from 'vue'
import VueRouter from 'vue-router';
import NewsView from '../views/NewsView.vue';
import JobsView from '../views/JobsView.vue';
import AskView from '../views/AskView.vue';
import UserView from '../views/UserView.vue';
import ItemView from '../views/ItemView.vue';

Vue.use(VueRouter);

export const router = new VueRouter({
    mode: 'history', // URL 주소에 # 제거
    routes: [
        {
            // 기존 접속 주소를 redirect를 이용하여 연결합니다.
            path: '/',
            redirect: '/news',
        },
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
        {
            path: '/user/:id',
            component: UserView,
        },
        {
            path: '/item',
            component: ItemView,
        },
    ]
})
~~~

`store/index.js`에 사용자 정보를 담을 객체를 선언합니다.  
~~~js
import Vue from 'vue';
import Vuex from 'vuex';
import mutations from './mutations.js';
import actions from './actions.js';

Vue.use(Vuex);

export const store = new Vuex.Store({
    state: {
        news: [],
        jobs: [],
        ask: [],
        user: {}
    },
    getters: {
        fetchedAsk(state) {
            return state.ask;
        }
    },
    mutations,
    actions
})
~~~

`store\action.js`에 action을 정의합니다.  
~~~js
import { fetchNewsList, fetchAskList, fetchJobsList, fetchUserInfo } from '../api/index.js';

export default {
    FETCH_NEWS(context) {
        fetchNewsList()
            .then(response => {
                console.log(response.data);
                context.commit('SET_NEWS', response.data);
                // state.news = response.data; -> 불가능
            })
            .catch(function (error) {
                console.log(error);
            });
    },
    FETCH_JOBS({ commit }) {
        fetchJobsList()
            .then(({ data }) => {
                commit('SET_JOBS', data);
            })
            .catch(error => {
                console.log(error);
            });
    },
    FETCH_ASK({ commit }) {
        fetchAskList()
            .then(({ data }) => {
                commit('SET_ASK', data);
            })
            .catch(error => {
                console.log(error);
            });
    },
    FETCH_USER({ commit }, name) {
        fetchUserInfo(name)
            .then(({ data }) => {
                commit('SET_USER', data);
            })
            .catch(error => {
                console.log(error);                
            })
    }
}
~~~

store의 state에 전달하기 위해 `mutations.js`에 코드를 추가합니다.  
~~~js
export default {
    SET_NEWS(state, news) {
        state.news = news;
    },
    SET_JOBS(state, jobs) {
        state.jobs = jobs;
    },
    SET_ASK(state, ask) {
        state.ask = ask;
    },
    SET_USER(state, user) {
        state.user = user;
    }
}
~~~

`NewsView.vue`에 사용자를 클릭했을 때 `UserView.vue`를 호출하도록 코드를 추가합니다.  
~~~js
<template>
    <div>        
        <div v-for="item in this.$store.state.news">            
            <!-- 기존에 사용하던 a 태그 -->
            <!-- <a href="https://google.com">{{ user.title }}</a> -->

            <a v-bind:href="item.url">
                {{ item.title }}
            </a>
            <small>{{ item.time_ago}} by 
                <!-- <router-link v-bind:to="'/user/' + item.user">{{ item.user }}</router-link> -->
                <router-link v-bind:to="`/user/${item.user}`">{{ item.user }}</router-link>
            </small>
        </div>
    </div>
</template>

<script>
export default {
    created() {      
        this.$store.dispatch('FETCH_NEWS');
    }
}
</script>

<style>
</style>
~~~

`UserView.vue`를 생성하여 store를 호출합니다.  
여기서 computed를 이용하여 공통 내용을 정의하였습니다.  
~~~js
<template>
    <div>
        <p>name : {{ userInfo.id }}</p>
        <p>karma : {{ userInfo.karma }}</p>
        <p>created : {{ userInfo.created }}</p>
    </div>
</template>

<script>
export default {
    computed: {
        // 공통 내용 정의
        userInfo() {
            return this.$store.state.user;
        }
    },
    created() {
        const userName = this.$route.params.id;

        // 이 방식을 store를 이용하는 코드로 변환
        // axios.get(`https://api.hnpwa.com/v0/user/${userName}.json`);
        
        // Store의 Action에 정의된 FETCH_USER 호출, 인자로 userName 전달
        this.$store.dispatch('FETCH_USER', userName);
    }
}
</script>

<style>
</style>
~~~

## 5-2. 질문 상세 페이지 구현  
`/api/index.js`에 API를 추가합니다.  
`/routes/index.js`에 라우트를 추가합니다.  
`/store/actions.js, /store/mutations.js`에 각각 action, mutation을 정의합니다.  

그리고 `AskView.vue`를 다음과 같이 수정합니다.  
~~~js
<template>
    <div>
        <div v-for="item in fetchedAsk">
            <a :href="item.url">
                {{ item.title }}
            </a>
            <small>
                {{ item.time_ago}} by 
                <router-link v-bind:to="`/item/${item.id}`"> {{ item.user }}</router-link>
            </small>
        </div>
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

<style>
</style>
~~~

마지막으로 `ItemView.vue`를 추가하고 코드를 작성합니다.  
여기서는 mapGetters를 이용해보았습니다.  
~~~js
<template>
    <div>
        <p>{{ fetchedItem.title }}</p>
        <div>
            {{ fetchedItem.content }}
        </div>
    </div>
</template>

<script>
import { mapGetters } from 'vuex';

export default {
    computed: {
        ...mapGetters(['fetchedItem']),
    },
    created() {
        const itemId = this.$route.params.id;
        this.$store.dispatch('FETCH_ITEM', itemId);
    }
}
</script>

<style>
</style>
~~~

## 5-3. 질문 상세 페이지 스타일링  
스타일링과 v-html 디렉티브 사용법을 소개합니다.  
v-html 디렉티브는 문자열 내 태그가 있을 때, 자동으로 변환하여 출력하도록 합니다.  

아이콘을 적용하기 위해 fontawesome을 `public\index.html` 링크를 추가합니다.  
~~~html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title>vue-news</title>
    <script src="https://kit.fontawesome.com/9a474936c1.js"></script>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but vue-news doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
~~~

그리고 `ItemView.vue` 스타일링을 하겠습니다.  
~~~vue
<template>
    <div>
        <selection>
            <!-- 질문 상세 정보 -->
            <div class="user-container">
                <div>
                    <i class="fas fa-user"></i>
                </div>
                <div class="user-description">
                    <router-link :to="`/user/${fetchedItem.user}`">
                        {{ fetchedItem.user }}
                    </router-link>                    
                    <div class="time">
                        {{ fetchedItem.time_ago }}
                    </div>
                </div>                
            </div>
            <h2> {{ fetchedItem.title }} </h2>
        </selection>
        <selection>
            <!-- 질문 댓글  -->
            <div v-html="fetchedItem.content">
                <!-- {{ fetchedItem.content }} -->
            </div>
        </selection>
    </div>
</template>

<script>
import { mapGetters } from 'vuex';

export default {
    computed: {
        ...mapGetters(['fetchedItem']),
    },
    created() {
        const itemId = this.$route.params.id;
        this.$store.dispatch('FETCH_ITEM', itemId);
    }
}
</script>

<style scopped>
.user-container {
    display: flex;
    align-items: center;
    padding: 0.5rem;
}
.fa-user {
    font-size: 2.5rem;    
}
.user-description {
    padding-left: 8px;
}
.time {
    font-size: 0.7rem;
}
</style>
~~~

## 5-4. 라우터 트랜지션  
`<router-view />` 를 `<transition />`으로 감싸면 transition 기능을 사용할 수 있습니다.  
`App.vue`에 trasition을 추가하고 스타일링을 하겠습니다.  
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

/* Router Transition */
.page-enter-active, .page-leave-active {
  transition: opacity .5s;
}
.page-enter, .page-leave-to {
  opacity: 0;
}
</style>
~~~