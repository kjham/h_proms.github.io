---
layout: post
title: 'Vue.js 완벽가이드 - 7'
author: kjham.ham
date: 2019-08-02 13:00
tags: [swtech,java,vuejs,frontend]
image: /files/covers/VUE_JS.jpg
comments: true
---

# 7. 리팩토링 : Data Flow, Slot  

사용자 정보를 표시하는 공통 컴포넌트를 만들어 보겠습니다.  

## 7-1. 데이터 흐름 처리 1  
기존에 사용자 페이지인 `UserView.vue` 에서 `created()` 가 호출될 때 사용자 정보를 store에 담아서 화면에 표현하는 작업을 하였습니다.  
이 store 데이터를 이용하여 사용자 공통 컴포넌트를 작성하겠습니다.  

사용자 공통 컴포넌트인 `/components/UserProfile.vue`를 생성합니다.  
computed를 통해 이미 store에 저장된 사용자 정보를 읽어 화면에 표기합니다.  
~~~vue
<template>
    <div>
        <div class="user-container">
                <div>
                    <i class="fas fa-user"></i>
                </div>
                <div class="user-description">
                    <div> {{ userInfo.id }} </div>
                    <div class="time">
                        {{ userInfo.created }}
                    </div>
                </div>                
            </div>
    </div>
</template>

<script>
export default {
    computed: {
        userInfo() {
            return this.$store.state.user;
        }
    },
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

그리고 기존의 정의된 `UserView.vue`에서 UserProfile를 호출합니다.  
~~~vue
<template>
    <div>
        <user-profile></user-profile>
    </div>
</template>

<script>
import UserProfile from '../components/UserProfile.vue';

export default {
    components: {
        UserProfile,
    },    
    created() {
        const userName = this.$route.params.id;
        this.$store.dispatch('FETCH_USER', userName);
    }
}
</script>

<style>
</style>
~~~

## 7-2. 데이터 흐름 처리 2  
이번에는 UserView의 데이터를 props를 이용하여 전달하는 방법입니다.  
기존에 정의된 UserView의 변화를 최소화 하면서 데이터를 전달할 수 있습니다.  
`UserView.vue`에는 컴포넌트를 추가하고, 컴포넌트에 props를 이용하여 데이터를 전달하는 코드만 작성하면 됩니다.  
~~~vue
<template>
    <div>
        <user-profile :info="userInfo"></user-profile>
    </div>
</template>

<script>
import UserProfile from '../components/UserProfile.vue';

export default {
    components: {
        UserProfile,
    },    
    computed: {
        userInfo() {
            return this.$store.state.user;
        }
    },
    created() {
        const userName = this.$route.params.id;
        this.$store.dispatch('FETCH_USER', userName);
    }
}
</script>

<style>
</style>
~~~

그리고 `UserProfile.vue`에서는 props로 데이터를 수신 후 전달받은 객체로 데이터를 표기하면 됩니다.  
~~~vue
<template>
    <div>
        <div class="user-container">
                <div>
                    <i class="fas fa-user"></i>
                </div>
                <div class="user-description">
                    <!-- <div> {{ userInfo.id }} </div>
                    <div class="time">
                        {{ userInfo.created }}
                    </div> -->
                    <div> {{ info.id }} </div>
                    <div class="time">
                        {{ info.created }}
                    </div>
                </div>                
            </div>
    </div>
</template>

<script>
export default {
    props: {
        info: Object
    },
    // computed: {
    //     userInfo() {
    //         return this.$store.state.user;
    //     }
    // },
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

## 7-3. 데이터 흐름 처리 비교  

> props 방식  
1. UserView는 created()를 이용하여 $store의 action()을 호출합니다.  
2. $store는 axios를 이용하여 데이터를 요청하여 수신받습니다.  
3. $store는 mutations를 통해 state로 데이터를 전달합니다.  
4. UserView는 computed()를 통해 $store의 state에 접근하여 데이터를 읽습니다.  
5. UserProfile는 props로 UserView의 읽어온 데이터를 화면에 반영합니다.  

> $store 방식  
1. UserView는 created()를 이용하여 $store의 action()을 호출합니다.  
2. $store는 axios를 이용하여 데이터를 요청하여 수신받습니다.  
3. $store는 mutations를 통해 state로 데이터를 전달합니다.  
4. UserProfile는 computed()를 통해 $store의 state에 접근하여 데이터를 읽습니다.  
5. UserProfile는 읽어온 데이터를 화면에 반영합니다.  

비교를 하면, 데이터를 읽어오는 것 까지는 같지만 ..  
props를 이용하여 전달하는 것과 $store에 직접 접근하는 것으로 나누어 집니다.  
상황에 따라 선택해서 사용해야할 것으로 보입니다.  

## 7-4. slot을 이용한 사용자 프로필 컴포넌트 구현  

slot은 하위 컴포넌트에서 표현할 것을 상위 컴포넌트에서 정의할 수 있도록 지원합니다.  
ListItem 공통 컴포넌트를 작성하면서 props와 $store 방식을 사용했다면,  
이번에는 slot을 이용하여 공통적으로 표현될 `UserProfile.vue`를 수정하겠습니다.  
~~~vue
<template>
    <div>
        <div class="user-container">
                <div>
                    <i class="fas fa-user"></i>
                </div>
                <div class="user-description">
                    <!-- <div> user name : {{ info.id }} </div> -->
                    <slot name="username">
                        <!-- 상위 컴포넌트에서 정의할 영역 -->
                    </slot>
                    <div class="time">
                        <!-- {{ info.created }} -->
                        <slot name="time">
                            <!-- 상위 컴포넌트에서 정의할 영역 -->
                        </slot>
                    </div>
                    <slot name="karma">
                        
                    </slot>
                </div>                
            </div>
    </div>
</template>

<script>
export default {
    props: {
        info: Object
    },
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

`UserProfile.vue`에서 `slot`을 정의하였으니,  
UserProfile을 호출하는 기존 `UserView.vue`를 수정합니다.  
~~~vue
<template>
    <div>
        <user-profile :info="userInfo">
            <div slot="username">{{ userInfo.id }}</div>
            <template slot="time">{{ userInfo.created }}</template>
            <template slot="karma">{{ userInfo.karma }}</template>
        </user-profile>
    </div>
</template>

<script>
import UserProfile from '../components/UserProfile.vue';

export default {
    components: {
        UserProfile,
    },    
    computed: {
        userInfo() {
            return this.$store.state.user;
        }
    },
    created() {
        const userName = this.$route.params.id;
        this.$store.dispatch('FETCH_USER', userName);
    }
}
</script>

<style>
</style>
~~~

그리고 `ItemVuew.vue`에 사용자 정보를 `UserProfile.vue`를 이용하도록 수정합니다.  
~~~vue
<template>
    <div>
        <section>
            <!-- 사용자 정보 -->
            <user-profile :info="fetchedItem">
                <div slot="username">{{ fetchedItem.user }}</div>
                <template slot="time">{{ fetchedItem.time_ago }}</template>
                <template slot="karma">{{ fetchedItem.points }}</template>
            </user-profile>
            <!-- <div class="user-container">
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
            </div>             -->
        </section>
        <section>
            <h2> {{ fetchedItem.title }} </h2>
        </section>
        <section>
            <!-- 질문 댓글  -->
            <div v-html="fetchedItem.content"></div>
        </section>
    </div>
</template>

<script>
import { mapGetters } from 'vuex';
import UserProfile from '../components/UserProfile.vue';

export default {
    components: {
        UserProfile,
    },
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

## 7-5. 사용자 프로필 컴포넌트 스타일링 및 코드 정리  
사용자 프로필 컴포넌트를 공통으로 사용하도록 작업하였습니다.  
`ItemView.vue`에서는 사용자 이름을 클릭 시, 사용자 페이지로 이동하는 처리가 필요합니다.  
그리도 `karma`가 작성일 다음 줄에 나오도록 `template`을 `span`으로 수정합니다.  
~~~vue
<template>
    <div>
        <section>
            <!-- 사용자 정보 -->
            <user-profile :info="fetchedItem">
                <router-link slot="username" :to="`/user/${fetchedItem.user}`">
                    {{ fetchedItem.user }}
                </router-link>
                <span slot="time">{{ 'Posted ' + fetchedItem.time_ago }}, </span>
                <span slot="karma">{{ fetchedItem.points }}</span>
            </user-profile>
        </section>
        <section>
            <h2> {{ fetchedItem.title }} </h2>
        </section>
        <section>
            <!-- 질문 댓글  -->
            <div v-html="fetchedItem.content"></div>
        </section>
    </div>
</template>

<!--아래 코드 생략 -->
~~~

`UserView.vue`도 다음과 같이 스타일링 작업을 합니다.  
~~~vue
<template>
    <div>
        <user-profile :info="userInfo">
            <div slot="username">{{ userInfo.id }}</div>
            <span slot="time">{{ 'Joined ' + userInfo.created }}, </span>
            <span slot="karma">{{ userInfo.karma }}</span>
        </user-profile>
    </div>
</template>

<!-- 아래 코드 생략 -->
~~~