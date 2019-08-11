---
layout: post
title: 'Vue.js 완벽가이드 - 9'
author: kjham.ham
date: 2019-08-07 13:00
tags: [swtech,java,vue-js,front-end]
image: /files/covers/VUE_JS.jpg
---

# 9. 리팩토링 : 네비게이션 가드  

이번 리팩토링은 라우터에 대해 알아봅니다.  
[데이터 호출 시점]  
1. 컴포넌트 라이프 사이클 훅  
 - created : (컴포넌트가 생성)되자마자 호출하는 로직  

2. 라우터 네비게이션 가드  
 - 특정 URL로 접근하기 전의 동작을 정의하는 속성(함수)  

## 9-1. 네비게이션 가드  
네비게이션 가드란, 라우터로 특정 URL에 접근근할 때 해당 URL의 접근을 막는 방법을 말합니다.  
예를 들어, 사용자의 인증 정보가 없으면 특정 페이지에 접근하지 못하게 할 때 사용하는 기술입니다.  

- 애플리케이션 전역에서 동작하는 **전역 가드**  
- 특정 URL에서만 동작하는 **라우터 가드**  
- 라우터 컴포넌트 안에 정의하는 **컴포넌트 가드**  

특정 라우터에 진입하기 전에 혹은 진입할 때 특정 로직이 수행하도록 정의할 수 있습니다.  
Vue 공식 문서에 Navigation Guard를 참조하면 좋습니다..  

## 9-2. 네비게이션 가드 실습  
네이게이션 가드를 이용하여 페이지 전환에 따른 데이터 표현이 더 자연스럽게 되도록 해봅니다.  
현재까지는 AskView -> JobsView로 화면 전환 시,  
JobsView는 선택된 것으로 바뀌고 Spinner가 나타난 후 ..  
데이터가 표현된 후, Spinner가 사라졌습니다.  

이번 실습을 통해서는..  JobsView를 클릭 했을 때..  
Spinner가 표현된 후  데이터를 불러오고, 표현된 다음에  
JobsView가 선택된 것으로 바뀌면서 Spinner가 사라지도록 합니다.  

`/routes/index.js`에 다음과 같이 `beforeEnter`를 정의합니다.  
~~~js
<!-- 코드 생략 -->
export const router = new VueRouter({
    mode: 'history', // URL 주소에 # 제거
    routes: [
        {
            // 기존 접속 주소를 redirect를 이용하여 연결합니다.
            path: '/',
            redirect: '/news',
        },
        {
            path: '/news',
            name: 'news',
            component: NewsView,
            beforeEnter: (to, from, next) => {
                bus.$emit('start:spinner');
                store.dispatch('FETCH_LIST',to.name)
                    .then(() => next())
                    .catch((error) => {
                        console.log(error);
                    });
            }
        },
<!-- 코드 생략 -->
~~~

그리고 `/mixin/ListMixin.js`를 수정합니다.  
~~~js
import bus from '../utils/bus.js';

// mixin
export default {
    // 재사용할 컴포넌트 옵션     
    mounted() {
        bus.$emit('end:spinner');
    }
}
~~~

그리고 `/store/action.js`에서 각 메소드에 `return`을 추가합니다.
~~~js
<!-- 코드 생략 -->
export default {
    FETCH_NEWS(context) {
        return fetchNewsList()
            .then(response => {
                context.commit('SET_LIST', response.data);
                return response;
            })
            .catch(function (error) {
                console.log(error);
            });
    },
<!-- 코드 생략 -->
~~~

위와 마찬가지로 `AskView.vue`와 `JobsView.vue`에도 적용합니다.  
그럼 자연스럽게 페이지가 표현되는 것을 확인할 수 있습니다.  