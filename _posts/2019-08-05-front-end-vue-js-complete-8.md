---
layout: post
title: 'Vue.js 완벽가이드 - 8'
author: kjham.ham
date: 2019-08-05 13:00
tags: [swtech,java,vuejs,frontend]
image: /files/covers/VUE_JS.jpg
comments: true
---

# 8. 리팩토링 : HOC, Mixin  

## 8-1. Spinner 컴포넌트 구현  
로딩바를 표현하는 `Spinner` 컴포너트를 작성하겠습니다.  
그리고 각 페이지로 진힙할 때 로딩바가 출현하도록 코드를 작성합니다.  
먼저 `/componets/Spinner.vue` 를 작성합니다.  
~~~vue
<template>
  <div class="lds-facebook" v-if="loading">
    <div>
    </div>
    <div>
    </div>
    <div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    loading: {
      type: Boolean,
      required: true,
    },
  },
}
</script>

<style>
.lds-facebook {
  display: inline-block;
  position: absolute;
  width: 64px;
  height: 64px;
  top: 47%;
  left: 47%;
}
.lds-facebook div {
  display: inline-block;
  position: absolute;
  left: 6px;
  width: 13px;
  background: #42b883;
  animation: lds-facebook 1.2s cubic-bezier(0, 0.5, 0.5, 1) infinite;
}
.lds-facebook div:nth-child(1) {
  left: 6px;
  animation-delay: -0.24s;
}
.lds-facebook div:nth-child(2) {
  left: 26px;
  animation-delay: -0.12s;
}
.lds-facebook div:nth-child(3) {
  left: 45px;
  animation-delay: 0;
}
@keyframes lds-facebook {
  0% {
    top: 6px;
    height: 51px;
  }
  50%, 100% {
    top: 19px;
    height: 26px;
  }
}
</style>
~~~

그리고 `ListItem.vue`의 created()에 정의한 코드를 제거합니다.  
~~~vue
<!-- 코드 생략 -->
<script>
export default {    
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
<!-- 코드 생략 -->
~~~

`/store/action.js/`에 response를 반환하도록 수정합니다.  
~~~js
<!-- 코드 생략 -->
export default {
    FETCH_NEWS(context) {
        fetchNewsList()
            .then(response => {
                context.commit('SET_NEWS', response.data);
                return response;
            })
            .catch(function (error) {
                console.log(error);
            });
    },
<!-- 코드 생략 -->    
~~~

`/utils/bus.js` 파일을 생성하여 코드를 작성합니다.  
~~~js
import Vue from 'vue';

//export const bus = new Vue();
// 아래처럼 지정된 이름으로 호출해야 함
//import { bus } from './bus.js';

export default new Vue();
// 아래와 같이 자유롭게 정의된 이름으로 호출 가능
// import Bus from './bus.js'
// import BusBBB from './bus.js'
~~~

`NewsView.vue` 코드를 수정하고, `ItemView.vue, JobsView.vue`도 반영합니다.  
~~~vue
<template>
    <list-item></list-item>
</template>

<script>
import ListItem from '../components/ListItem.vue'
import bus from '../utils/bus.js';

export default {
    components: {
        ListItem,
    },
    created() {
        bus.$emit('start:spinner');
        // 3초 뒤 수신하도록 임시코드 작성
        setTimeout(() => {
            this.$store.dispatch('FETCH_NEWS')
                .then(() => {
                    console.log('fetched');
                    bus.$emit('end:spinner');  
                })
                .catch((error) => {
                    console.log(error);                
                });
        }, 3000);
    }
}
</script>

<style>
</style>
~~~

## 8-2. 하이 오더 컴포넌트  
뷰의 하이 오더 컴포넌트는 리액트의 하이 오더 컴포넌트에서 기원된 것입니다.  
컴포넌트의 로직(코드)을 재사용하기 위한 고급 기술입니다.  

이번 실습을 통해 `AskView.vue`, `JobsView.vue`, `NewsView.vue`를 모두 제거하고, 공통 컴포너트로 관리를 하게 됩니다.  

`store` 관련하여 먼저 코드 수정을 해봅니다.  
`/store/index.js`에 `list` 변수를 생성하고 기존 변수는 제거합니다.
~~~js
import Vue from 'vue';
import Vuex from 'vuex';
import mutations from './mutations.js';
import actions from './actions.js';

Vue.use(Vuex);

export const store = new Vuex.Store({
    state: {
        user: {},
        item: {},
        list: [],
    },
    getters: {
        fetchedAsk(state) {
            return state.ask;
        },
        fetchedItem(state) {
            return state.item;
        }
    },
    mutations,
    actions
})
~~~

`/store/action.js`도 `FETCH_LIST`를 작성하고 각각의 코드는 모두 제거합니다.  
~~~js
import { fetchUserInfo, fetchCommentItemInfo, fetchList } from '../api/index.js';

export default {
    FETCH_USER({ commit }, name) {
        fetchUserInfo(name)
            .then(({ data }) => {
                commit('SET_USER', data);
            })
            .catch(error => {
                console.log(error);                
            })
    },
    FETCH_ITEM({ commit }, itemId) {
        fetchCommentItemInfo(itemId)
            .then(({ data }) => {
                commit('SET_ITEM', data);
            })
            .catch(error => {
                console.log(error);                
            })
    },
    FETCH_LIST({ commit }, pageName) {
        fetchList(pageName)
            .then(({ data }) => commit('SET_LIST', data))
            .catch(error => console.log(error));
    },
}
~~~

`/store/mutation.js`도 `SET_LIST`만 작성하면 되겠지요..
~~~js
export default {
    SET_USER(state, user) {
        state.user = user;
    },
    SET_ITEM(state, item) {
        state.item = item;
    },
    SET_LIST(state, item) {
        state.list = item;
    }
}
~~~

`/components/ListItem.vue`에 `store`로부터 list 데이터를 읽어오도록 합니다.  
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
    computed: {
        listItems() {
            return this.$store.state.list;
        }
    }
}
</script>
<!-- 코드 생략 -->
~~~

그리고 `ListView.vue`의 코드입니다.
~~~vue
<template>
    <div>
        <list-item></list-item>
    </div>
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

마지막으로 `AskView.vue`, `JobsView.vue`, `NewsView.vue`를 모두 제거합니다.  

하이 오더 컴포넌트 방식을 이용하여 3개의 컴포넌트를 1개로 합치어 정상적인 화면이 표현되는 것을 확인할 수 있습니다.  

## 8-3. Mixin 활용  
Mixin은 여러 컴포넌트 간의 공통으로 사용하고 있는 로직 기능들을 재사용하는 방법입니다.  
Mixin에 정의할 수 있는 재사용 로직은 data, methods, created 등과 같은 컴포넌트 옵션입니다.  

하이 오더 컴포너트는 컴포넌트의 Depth가 깊어지기 때문에..  
컴포넌트 통신이 생겼을 때 불편한 점이 발생합니다.  

`/mixin/ListMinin.js` 파일을 생성합니다.  
~~~js
import bus from '../utils/bus.js';

// mixin
export default {
    // 재사용할 컴포넌트 옵션 
    created() {
        bus.$emit('start:spinner');
        this.$store.dispatch('FETCH_LIST', this.$route.name)
            .then(() => {
                console.log('fetched');
                bus.$emit('end:spinner');
            })
            .catch((error) => {
                console.log(error);
            });
    },
}

// hoc
// export default function create() {
//     // 재사용할 컴포넌트 옵션 
// }
~~~

그리고 `NewsView.vue`에 mixin을 적용해봅니다.  
즉, created() 로 정의한 내용을 mixin에 정의하여 공통적으로 사용할 수 있도록 한 것을 확인할 수 있습니다.  
~~~vue
<template>
    <list-item></list-item>
</template>

<script>
import ListItem from '../components/ListItem.vue'
import bus from '../utils/bus.js';
import ListMixIn from '../mixin/ListMixin.js';

export default {
    components: {
        ListItem,
    },
    mixins: [ListMixIn]
}
</script>

<style>
</style>
~~~

그리고 `router`에서 하이 오더 컴포넌트 방식이 아닌,  
직접적으로 NewView 컴포넌트를 호출하는 것으로 수정하면 됩니다.  

그러면 mixin보다 high order compoenet가 depth가 하나 더 깊은 것을 볼 수 있을겁니다..  