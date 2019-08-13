---
layout: post
title: 'Vue.js 완벽가이드 - 12'
author: kjham.ham
date: 2019-08-08 10:00
tags: [swtech,java,vuejs,frontend]
image: /files/covers/VUE_JS.jpg
---

# 12. 컴포넌트 디자인 패턴  

컴포넌트 디자인 패턴
1. Common - 기본적인 컴폰언트 등록과 컴포너트 통신  
2. Slot - 마크업 확장이 가능한 컴포넌트  
3. Controlled - 결합력 높은 컴포넌트  
4. Renderless - 데이터 처리 컴포넌트  

## 12-1. Common 디자인 패턴  
End 단의 컴포넌트가 표현되도록 구성하고 상위 컴포넌트에서 호출하는 기본적인 방식을 말합니다.  

`/components/AppHeader.vue`를 작성하여 Header 역할의 Component를 작성합니다.  
~~~vue
<template>
  <header>
    <h1>{{ title }}</h1>
  </header>
</template>

<script>
export default {
  // 일반적인 props 선언 방식
  // props: ['title'],

  // 데이터 Validation 을 수행하도록 선언
  props: {
    title: String,
  }
}
</script>
~~~

`/components/AppContens.vue`로 내용을 표현하는 컴포넌트를 작성합니다.  
~~~vue
<template>
  <div>
    <ul>
      <li v-for="item in items">
        {{ item }}
      </li>
    </ul>
    <button @click="$emit('renew')">renew items</button>
  </div>
</template>

<script>
export default {
  props: {
    // Type과 Required의 Validation 설정
    items: {
      type: Array,
      required: true,
    },
  },
  // 기존이 아래처럼 emit을 구현했지만, 위 tag 안에서처럼 정의도 가능
  // methods: {
  //   sendRenew() {
  //     this.$emit('renew');
  //   }
  // }
}
</script>
~~~

`App.vue`에 하위 컴포넌트를 호출하여 화면에 표현합니다.  
~~~vue
<template>
  <div>
    <app-header :title="appTitle"></app-header>
    <app-content :items="items" @renew="renewItems"></app-content>
  </div>
</template>

<script>
import AppHeader from './components/AppHeader.vue';
import AppContent from './components/AppContent.vue';

export default {
  components: {
    AppHeader,
    AppContent,
  },
  data() {
    return {
      appTitle: 'Common Approach',
      items: [10, 20, 30],
    }
  },
  methods: {
    renewItems() {
      this.items = [40, 50, 60];
    },
  },
}
</script>
~~~

## 12-2. Slot 디자인 패턴  
마크업 확장 컴포넌트를 구성할 수 있는 패턴입니다.  

`slot`을 이용한 컴포넌트를 생성합니다.  
`Item.vue` 입니다.  
~~~vue
<template>
  <li>
    <slot>
      <!-- NOTE: 등록하는 곳에서 정의할 화면 영역 -->
    </slot>
  </li>
</template>
~~~

`App.vue`에서 `Item`컴포넌트의 slot를 이용하여 생성해봅니다.  
`slot`를 이용하면 다음과 같이 서로 다른 구성으로 구현할 수 있습니다.  
~~~vue
<template>
  <div>
    <ul>
      <!-- item 태그안에 구성된 것을 보고 slot임을 추측할 수 있습니다. -->
      <item>
        아이템 1
      </item>
      <item>
        아이템 2 <button>click me</button>
      </item>
      <item>
        <div>
          아이템 3
        </div>
        <img src="./assets/endgame.png" alt="어벤져스 엔드게임" width="50px" height="50px">
      </item>
      <item>
        <div style="color: blue; font-size: 20px;">아이템 4</div>  
      </item>
    </ul>
  </div>
</template>

<script>
import Item from './Item.vue';

export default {
  components: {
    Item,
  },
}
</script>
~~~

## 12-3. Controlled Component 디자인 패턴  
컴포넌트를 세밀하게 나누고자 할 때 도움이 되는 패턴입니다.  
다음과 같이 `check-box`에 `v-model`로 `checked`를 정의하면  
상위 컴포넌트에서 하위 컴포넌트의 데이터를 관리할 수 있습니다.  
그리고 하위에서 이벤트가 동작할 때 `emit`을 이용하여 상위 컴포넌트에 데이터의 변화를 전달하도록 구성할 수 있습니다.  
`CheckBox.vue`의 모습입니다.  
~~~vue
<template>
  <input type="checkbox" :value="value" @click="toggleCheckBox">
</template>

<script>
export default {
  // @input 이벤트
  // :value 값
  props: ['value'],
  methods: {
    toggleCheckBox() {
      this.$emit('input', !this.value)
    }
  }
}
</script>
~~~
`App.vue`의 모습입니다.  
~~~vue
<template>
  <check-box v-model="checked"></check-box>
</template>

<script>
import CheckBox from './components/CheckBox.vue';

export default {
  components: {
    CheckBox
  },
  data() {
    return {
      checked: false,
    }
  },
}
</script>
~~~

## 12-4. Renderless Component 디자인 패턴  
표현을 하지 않는 Component를 Renderless Component 라고 합니다.  
데이터를 하위에서 노출시키고, 상위에서 표현하는 방식을 말합니다.  
하위에서 정의 후 데이터를 변환해도 상위에도 적용이 가능하도록 합니다..  

`render.html`에서 방법 세 가지로 표현하는 방식을 먼저 살펴봅니다..  
(방법 1) 인스턴스 내 메시지를 직접 표현하던 것을 ..  
(방법 2) Vue 인스턴스 내 `template`를 이용해 표현할 수 있습니다.  
(방법3) render를 이용하여 직접 태그 및 메시지를 표현도 가능합니다.  
~~~html
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Render Function</title>
</head>
<body>
  <div id="app">
    <!-- 인스턴스 영역 -->
    <!-- 방법 1 -->
    <!-- <p>{{ message }}</p> -->
  </div>
  
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script>
    new Vue({
      el: '#app',
      data: {
        message: 'Hello Vue',
      },
      // 방법 2
      // template: '<p>{{ message }}</p>',

      // 방법 3
      render: function(createElement) {
        // return createElement('태그 이름', '태그 속성', '하위 태그 내용');
        return createElement('p',this.message);
      },
    })
  </script>
</body>
</html>
~~~

Vue Project에서는 다음과 같이 사용이 가능합니다.
`/components/FetchData.vue`에서 `response`와 `loading` 데이터를 생성하고,  
`render`의 `$scopedSlots`을 이용하여 데이터를 노출시킵니다.  
~~~vue
<script>
import axios from 'axios';

export default {
  props: ['url'],
  data() {
    return {
      response: null,
      loading: true,
    }
  },
  created() {
    axios.get(this.url)
      .then(response => {
        this.response = response.data;
        this.loading = false;
      })
      .catch(error => {
        alert('[ERROR] fetching the data', error);
        console.log(error);
      });
  },
  // render는 컴포넌트를 그리는 것입니다.
  render() {
    return this.$scopedSlots.default({
      // scopedSlot을 이용하여 데이터를 반환할 수 있습니다.
      response: this.response,
      loading: this.loading,
    });
  },
}
</script>
~~~

그리고 `App.vue`에서 `slot-scope`를 사용하여 데이터를 reactivity하게 반영할 수 있습니다.  
~~~vue
<template>
  <div>
    <fetch-data url="https://jsonplaceholder.typicode.com/users/1">
      <!-- slot scope를 이용하여 response, loading 데이터에 접근이 가능합니다. -->
      <div slot-scope="{ response, loading }">
        <div v-if="!loading">
          <p>name : {{ response.name }}</p>
          <p>email : {{ response.email }}</p>          
        </div>          
        <div v-if="loading">
          Loading...
        </div>
      </div>
    </fetch-data>
  </div>
</template>

<script>
import FetchData from './components/FetchData.vue'

export default {
  components: {
    FetchData
  },
}
</script>
~~~