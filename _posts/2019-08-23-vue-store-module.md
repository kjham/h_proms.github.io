---
layout: post
title: 'Vue : Store Module'
author: kjham.ham
date: 2019-08-23 13:30
tags: [swtech,java,vuejs,frontend]
image: /files/covers/VUE_JS.jpg
comments: true
---

Vue에서 `Store`를 정의하다보면 하나의 큰 객체 안에 너무 많은 데이터를 저장하게 됩니다.  
규모가 커지며 저장소가 매우 비대해지기 때문에 모듈 단위로 나누어 `Store`를 관리할 수 있습니다.  

다음과 같이 모듈을 나누어 정의하고, Vuex의 modules를 이용하여 분리할 수있습니다.  
그리고 modules 내 정의한 모듈 이름에 따라 호출이 가능합니다.  
~~~vue
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA'의 상태
store.state.b // -> moduleB'의 상태
~~~

그리고 `$store`내 메소드를 이용하여 `Store Module`를 동적으로 등록, 삭제할 수 있습니다.  
[등록]  
`this.$store.registerModule("humanResourceConfig", humanResourceSyncStore)`
[제거]  
`this.$store.unregisterModule("humanResourceConfig")`

`$stroe`로 정의한 action, mutation 등은 전역 네임 스페이스 아래에 등록됩니다.  
여러 action, mutation이 동일할 수 있는 상황이 발생하게 됩니다.  
모듈이 독립적으로 사용되기를 원한다면, `namespace : true`를 `$store` 내 등록해야 합니다.  
다음과 같이 지역화된 모듈을 구분하여 호출이 가능합니다.  
`this.$store.getters["humanResourceSync/humanResourceConfig"]`

