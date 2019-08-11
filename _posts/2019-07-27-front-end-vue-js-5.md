---
layout: post
title: 'Vue.js 시작하기 - 5'
author: kjham.ham
date: 2019-07-26 12:00
tags: [swtech,java,vue-js,front-end]
image: /files/covers/VUE_JS.jpg
---

# 싱글 파일 컴포넌트 & 최종정리  

# 7. 싱글 파일 컴포넌트
빈 vue 파일을 생성 후 scf(scaffold)키워드로 자동완성으로 기본 템플릿 코드를 작성할 수 있습니다.  
<template> </template> 에는 하나의 <div /> 만 선언이 가능합니다.  
components 폴더 하위에 컴포넌트를 생성하게 되는데, 보통 두 단어 이상으로 구성하길 권장합니다.  
html에 존재하는 기존 tag와 중복될 수 있기 때문입니다.  

App.vue  
```javascript
<template>
  <!-- 하나의 Element 만 선언해야 합니다. (뒤에 <div> </div> 중복 불가) -->
  <div>
    <!-- <app-header v-bind:프롭스 속성 이름="상위 컴포넌트의 데이터 이름"></app-header> -->
    <!-- 하위 컴포넌트에서 인식하도록 선언한 v-on -->
    <app-header 
      v-bind:propsdata="str"      
      v-on:renew="renewStr"
      ></app-header>
  </div>
  <!-- 오류 발생 -->
  <!-- <div></div> -->
</template>

<script>
// .vue 까지 붙여야 파일 바로가기 수행이 가능합니다.
import AppHeader from './components/appHeader.vue'

export default {
  // vue-cli를 사용하면서  data: {} 이 아니라 function()을 이용해야합니다.
  data: function() {
    return {
      str: 'Header'
    }
  },
  components: {
    'app-header': AppHeader
  },
  methods: {    
    renewStr: function() {
      this.str = 'hi'
    }
  }
}
</script>

<style>

</style>
```

appHeader.vue  
~~~js
<template>
    <header>
        <!-- 상위 컴포넌트의 데이터를 수신받아 표기 -->
        <h1>{{ propsdata }}</h1>
        <!-- sendEvent를 이용하여 상위 컴포넌트에 전달 -->
        <button v-on:click="sendEvent">send</button>
    </header>
</template>

<script>
export default {
    props: ['propsdata'],
    methods: {
        sendEvent: function() {
            this.$emit('renew')
        }
    }
}
</script>

<style>

</style>
~~~

# 8. 최종 프로젝트
다음 명령어를 수행하여 새로운 프로젝트를 생성하고 실행합니다.  
`vue create vue-form`  
`cd vue-form`  
`npm run serve`  

간단하게 마크업 구조를 잡아보겠습니다.  
App.vue  
~~~js
<template>
  <!-- vue-cli 프로젝트를 생성하고, 간단하게 mark-up 구조를 잡아봅니다. -->
  <form action="">
    <div>
      <label for="username">id: </label>
      <input id="username" type="text">
    </div>
    <div>
      <label for="password">pw: </label>
      <input id="password" type="text">
    </div>
    <button>login</button>
  </form>
</template>

<script>
export default {

}
</script>

<style>

</style>
~~~

username과 password에 이벤트를 처리하고, 입력값이 변수에 입력되도록 추가합니다.  
App.vue  
~~~js
<template>
  <!-- vue-cli 프로젝트를 생성하고, 간단하게 mark-up 구조를 잡아봅니다. -->
  <form v-on:submit="submitForm">
    <div>
      <label for="username">id: </label>
      <input id="username" type="text" v-model="username">
    </div>
    <div>
      <label for="password">pw: </label>
      <input id="password" type="password" v-model="password">
    </div>
    <button type="submit">login</button>
  </form>
</template>

<script>
export default {
  data: function() {
    return {
      username: '',
      password: '',
    }
  },
  methods: {
    submitForm: function(event) {
      event.preventDefault();
      console.log(this.username, this.password);      
    }
  }
}
</script>

<style>

</style>
~~~

다음 명령어를 수행하여 axios를 설치합니다.  
`npm i axios`  

기존 JQuery 방식으로 `event.preventDefault()` 를 하면 이벤트가 발생할 때 새로고침 현상을 방지할 수 있었습니다.  
Vue는 선언한 form 태그 내의 `v-on:submit.prevent`를 이용하여 새로고침 현상을 방지할 수 있습니다.  
그리고 axios를 이용하여 url 과 입력한 data를 전송하여 회신 받는 코드를 작성하겠습니다.  
~~~js
<template>
  <!-- vue-cli 프로젝트를 생성하고, 간단하게 mark-up 구조를 잡아봅니다. -->
  <form v-on:submit.prevent="submitForm">
    <div>
      <label for="username">id: </label>
      <input id="username" type="text" v-model="username">
    </div>
    <div>
      <label for="password">pw: </label>
      <input id="password" type="password" v-model="password">
    </div>
    <button type="submit">login</button>
  </form>
</template>

<script>
import axios from 'axios'

export default {
  data: function() {
    return {
      username: '',
      password: '',
    }
  },
  methods: {
    submitForm: function(event) {
      // event.preventDefault(); ->  Vue에서는 v-on:submit="submitForm" 으로 됨
      console.log(this.username, this.password);

      var url = 'https://jsonplaceholder.typicode.com/users';
      var data = {
        username: this.username, // 입력받은 이름과 비밀번호
        password: this.password
      }
      axios.post(url, data)
        .then(function(response) {
          console.log(response)
        })
        .catch(function(error) {
          console.log(error);          
        });
    }
  }
}
</script>

<style>

</style>
~~~

# 9. 최종 정리
> **Reactivity**  
Vue.js가 추구하는 중심 사상이자 핵심 기능입니다.  
Data의 변화를 Vue에서 감지해서 화면에서 반영합니다.   
또한, 화면 조작에 대한 API나 속성을 Vue에서 제공합니다.  
Web 개발에 대한 편리함을 제공합니다.  

> **Instance**  
Vue에서 개발할 때 필수적으로 필요한 요소 입니다.  

> **Component**  
Web Framework에서 가장 중요한 요소이며, 여러 개의 Component를 구성함으로써 재사용성 기반을 마련할 수 있습니다.  

> **Component 통신**  
데이터의 흐름을 예측하며 설계를 할 수 있도록 제공합니다.  
대표적으로 상위에서 하위로 전달하는 props와 하위에서 상위로 전달하는 event가 있습니다.  

> **HTTP 통신 라이브러리 Axios**  
Vue에서 제공하는 대표적인 통신 라이브러리  

> **템플릿 문법**  
화면을 조작하기 위한 문법입니다.  
데이터 바인딩은 데이터의 엮어서 변화를 수행합니다.  
뷰 디렉티브는 화면 조작을 위한 추가적인 Vue만의 문법입니다.  
v- 로 이루어진 HTML의 속성입니다.  

> **Vue CLI**  
Vue를 생성할 때 명령어를 이용한 방식을 제공합니다.  

> **파일 컴포넌트**  
.Vue 파일에 대한 실습으로 익혔습니다.  

공식문서의 [Learn] 메뉴에서 다음 항목은 보기를 권장합니다.  
`[Guide], [Style Guide], [Cook book]`
> **Guide**  
되도록이면 원문을 보아야 합니다.  
Core 담당자가 매일같이 업데이트를 하기 때문에 번역본은 최신 내용이 없을 수 있습니다.  

> **Style Guide**  
Vue 개발을 수행할 때 참고할 수 있는 코드가 많이 있습니다.  

> **Cook Book**  
Vue 개발을 수행할 때 부딪히는 많은 문제에 대해서 다루고 있습니다.  