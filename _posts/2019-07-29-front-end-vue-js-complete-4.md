---
layout: post
title: 'Vue.js 완벽가이드 - 4'
author: kjham.ham
date: 2019-07-29 18:00
tags: [swtech,java,vue-js,front-end]
image: assets/images/VUE_JS.jpg
---

# 4. 스토어 & 중간정리  

## 4-1. Vuex 설치 및 Vuex가 적용된 앱 구조 소개  
지금까지 수행한 프로젝트는 API에서 바로 View로 데이터를 전달하였습니다.  
Vuex를 설치하여 API -> Vuex -> View 로 데이터 전달 과정을 알아보겠습니다.  

터미널에서 `npm i vuex`를 입력하여 vuex를 설치합니다.  

## 4-2. Vuex 모듈화 및 state 적용  
컴포넌트가 많은 프로젝트의 경우 하나의 컴포넌트가 갖고 있는 data를 다른 컴포넌트에 공유할 일이 발생합니다.  

`./src/store/index.js` 파일을 생성합니다.  
`index.js`에 다음과 같이 코드를 작성합니다.  
~~~js
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

export const store = new Vuex.Store({
    state: {
        news: []
    }
})
~~~

`main.js`에 store를 추가합니다.  
~~~js
import Vue from 'vue'
import App from './App.vue'
import { router } from './routes/index.js';
import { store } from './store/index.js';

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
  router,
  store
}).$mount('#app')
~~~

서버를 실행 하면 개발자도구 Vue의 두번째 Tab에서 State 항목으로 news가 등록된 것을 확인할 수 있습니다.  

## 4-3. NewsView에 actions와 실습 안내  
Actions에서 Backend API를 통해 데이터를 수신받습니다.  
이 데이터는 state로 바로 전달하지 못하고, mutations를 통해 전달해야 합니다.  
`index.js`에 actions, mutations를 구현해봅시다.  
~~~js
import Vue from 'vue';
import Vuex from 'vuex';
import { fetchNewsList } from '../api/index.js';

Vue.use(Vuex);

export const store = new Vuex.Store({
    state: {
        news: []
    },
    mutations: {
        SET_NEWS(state, news) {
            state.news = news;
        }
    },
    actions: {
        FETCH_NEWS(context) {
            fetchNewsList()
                .then(response => {
                    console.log(response.data);
                    context.commit('SET_NEWS', response.data);
                    // state.news = response.data; -> 불가능
                })
                .catch(function(error) {
                    console.log(error);                    
                });
        }
    }
})
~~~

`NewsView.vue`에 store를 적용해봅시다.  
이제 store에서 관리하는 news 데이터는 모든 component에서 접근이 가능하게 되었습니다.  
~~~js
<template>
    <div>        
        <div v-for="user in this.$store.state.news">{{ user.title }}</div>
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

## 4-4. JobsView, AskView 적용  
`index.js`에 Jobs, Ask 에 관한 state, mutations, actions를 추가합니다.  
~~~js
import Vue from 'vue';
import Vuex from 'vuex';
import { fetchNewsList } from '../api/index.js';
import { fetchAskList } from '../api/index.js';
import { fetchJobsList } from '../api/index.js'

Vue.use(Vuex);

export const store = new Vuex.Store({
    state: {
        news: [],
        jobs: [],
        ask: []
    },
    mutations: {
        SET_NEWS(state, news) {
            state.news = news;
        },
        SET_JOBS(state, jobs) {
            state.jobs = jobs;
        },
        SET_ASK(state, ask) {
            state.ask = ask;
        }
    },
    actions: {
        FETCH_NEWS(context) {
            fetchNewsList()
                .then(response => {
                    console.log(response.data);
                    context.commit('SET_NEWS', response.data);
                    // state.news = response.data; -> 불가능
                })
                .catch(function(error) {
                    console.log(error);                    
                });
        },
        // context 내부의 commit을 바로 호출하여 간결한 코드 작성이 가능합니다.
        FETCH_JOBS({ commit }) {
            fetchJobsList()
            	// 바로 data를 호출하는 방식입니다.
                .then(({ data }) => {
                    commit('SET_JOBS', data);
                })
                .catch();
        },
        FETCH_ASK({ commit }) {
            fetchAskList()
                .then(({ data }) => {
                    commit('SET_ASK', data);
                })
                .catch();
        }
    }
})
~~~

## 4-5. map 헬퍼 함수를 이용한 AskView 풀이  

`index.js`에 getters를 추가하고, `AskView.vue`에 데이터를 더 쉽게 가져오는 방법을 알아봅니다.  
`index.js` 코드 입니다.  
~~~js
import Vue from 'vue';
import Vuex from 'vuex';
import { fetchNewsList, fetchAskList, fetchJobsList } from '../api/index.js';

Vue.use(Vuex);

export const store = new Vuex.Store({
    state: {
        news: [],
        jobs: [],
        ask: []
    },
    getters: {
        fetchedAsk(state) {
            return state.ask;
        }
    },
    mutations: {
        SET_NEWS(state, news) {
            state.news = news;
        },
        SET_JOBS(state, jobs) {
            state.jobs = jobs;
        },
        SET_ASK(state, ask) {
            state.ask = ask;
        }
    },
    actions: {
        FETCH_NEWS(context) {
            fetchNewsList()
                .then(response => {
                    console.log(response.data);
                    context.commit('SET_NEWS', response.data);
                    // state.news = response.data; -> 불가능
                })
                .catch(function(error) {
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
        }
    }
})
~~~

`AskView.vue` 코드 입니다.  
mapState를 활용한 방법과 mapGetters를 이용한 방법이 있습니다.  
~~~js
<template>
    <div>
        <div v-for="item in fetchedAsk">{{ item.title }}</div>
    </div>
</template>

<script>
import { mapState, mapGetters } from 'vuex';

export default {
    computed: {
        // spread operator
        ...mapGetters([
            'fetchedAsk'
        ]),

        // #2
        // ...mapState({
        //     ask: state => state.ask
        // })

        // #1
        // ask() {
        //     return this.$store.state.ask;
        // }
    },
    created() {
        this.$store.dispatch('FETCH_ASK');
    }
}
</script>

<style>

</style>
~~~

## 4-6. Store 속성 모듈화  
`store\index.js`에서 actions와 mutations를 모듈화 해보겠습니다.  
`store\actions.js`와 `store\mutations.js` 파일을 생성합니다.  

`index.js` 코드 입니다.  
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
        ask: []
    },
    getters: {
        fetchedAsk(state) {
            return state.ask;
        }
    },
    mutations: mutations,
    actions: actions
})
~~~

`actions.js` 코드 입니다.  
~~~js
import { fetchNewsList, fetchAskList, fetchJobsList } from '../api/index.js';

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
    }
}
~~~

`mutations.js` 코드 입니다.  
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
    }
}
~~~

## 4-7. 중간 정리 및 스타일링  
Ask, Jobs, News의 각 타이틀에 링크를 연결하고, small tag로 꾸며보겠습니다.  
`AskView.vue` 파일입니다.  
`v-bind:href`를 `:href`로 축약하여 표현할 수 있습니다.  
~~~js
<template>
    <div>
        <div v-for="item in fetchedAsk">
            <a :href="item.url">
                {{ item.title }}
            </a>
            <small>{{ item.time_ago}} by {{ item.user }}</small>
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
`JobsView.vue` 파일입니다.  
~~~js
<template>
    <div>
        <div v-for="job in this.$store.state.jobs">            
            <a :href="job.url">
                {{ job.title }}
            </a>
            <small>{{ job.time_ago}}, {{ job.domain }}</small>
        </div>
    </div>
</template>

<script>
export default {    
    created() {
        this.$store.dispatch('FETCH_JOBS');
    }
}
</script>

<style>
</style>
~~~
`NewsView.vue` 파일입니다.  
~~~js
<template>
    <div>        
        <div v-for="item in this.$store.state.news">            
            <!-- 기존에 사용하던 a 태그 -->
            <!-- <a href="https://google.com">{{ user.title }}</a> -->

            <a v-bind:href="item.url">
                {{ item.title }}
            </a>
            <small>{{ item.time_ago}} by {{ item.user }}</small>
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