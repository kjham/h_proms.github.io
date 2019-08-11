---
layout: post
title: 'Vue.js 완벽가이드 - 3'
author: kjham.ham
date: 2019-07-29 14:00
tags: [swtech,java,vue-js,front-end]
image: /files/covers/VUE_JS.jpg
---

# 3. API 구현  

## 3-1. axios를 이용한 API 호출
`npm i axios --save` 를 이용하여 axios를 설치하고 dependencies를 확인합니다.  

`NewsView.vue` 에서 axios를 이용하여 데이터를 수신하는 코드를 작성합니다.  
~~~js
<template>
    <div>
        <div v-for="user in users">{{ user }}</div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    // Business Logic은 별도의 Component로 작성하여 관리하는 것이 좋습니다.
    data() {
        return {
            users: []
        }
    },    
    created() {
        // var vm = this;
        // axios.get('https://api.hnpwa.com/v0/news/1.json')
        //     .then(function(response) {
        //         console.log(response);
        //         vm.users = response.data;                
        //     })
        //     .catch(function(error){
        //         console.log(error);                
        //     });
        // -> 위 코드를 아래와 같이 축약할 수 있습니다.
        axios.get('https://api.hnpwa.com/v0/news/1.json')
            .then(response => this.users = response.data)
            .catch();
    }
}
</script>

<style>
</style>
~~~

## 3-2. API함수 구조화 방법  
`./src/index.js`파일을 생성하여 API를 작성합니다.  
~~~js
import axios from 'axios';

// 1. HTTP Request & Response와 관련된 기본 설정
const config = {
    baseUrl: 'https://api.hnpwa.com/v0/',
}

// 2. API 함수들을 정리
function fetchNewsList() {
    // return axios.get(config.baseUrl + 'news/1.json');
    // ES6 스타일
    return axios.get(`${config.baseUrl}news/1.json`);
}

export {
    fetchNewsList
}
~~~

`NewsView.vue`에 API를 적용합니다.  
~~~js
<template>
    <div>        
        <div v-for="user in users">{{ user.title }}</div>
    </div>
</template>

<script>
import { fetchNewsList } from '../api/index.js';

export default {
    // Business Logic은 별도의 Component로 작성하여 관리하는 것이 좋습니다.
    data() {
        return {
            users: []
        }
    },    
    created() {      
        fetchNewsList()
            .then(response => this.users = response.data)
            .catch();
    }
}
</script>

<style>
</style>
~~~

`JobsView.vue`도 적용해봅시다.  
~~~js
<template>
    <div>
        <div v-for="job in jobs">{{ job.title }}</div>
    </div>
</template>

<script>
import { fetchJobsList } from '../api/index.js'

export default {    
    data() {
      return {
          jobs: []
      }  
    },
    // created: function({})
    // ES6 축약 문법
    created() {
        fetchJobsList()
            .then(response => this.jobs = response.data)
            .catch();
    }
    // beforeMount,
    // mounted, -> 마운트 된 후 데이터가 세팅되면 reactivity 속성 떄문에 다시 그려집니다.
}
</script>

<style>
</style>
~~~

## 3-3. 자바스크립트 this 네 가지와 화살표 함수의 this  
1. 첫번째 this  
~~~shell
int a = 10;
window.a
this
# -> 여기서의 this는 window 입니다.
~~~
2. 두번째 this  
~~~shell
function sum(a, b) {
	console.log(this);
	return a + b;
}
# -> 여기서의 this는 window 입니다.
~~~
3. 세번째 this  
~~~shell
function Vue(el) {
	console.log(this);
	this.el = el;
}
new Vue('#app');
# -> 여기서의 this는 함수를 가키립니다.
~~~
4. 네번째 this
~~~js
console.log('호출 전: ', this);
created() {
        fetchJobsList()
            .then(function(response) {
            	console.log('호출 후: ', this);
            })
            .catch();
    }
// 호출 전에는 Vue Component를 가키립니다.
// 호출 후에는 undefined를 나타냅니다.
// 비동기 호출이 발생하면서 Vue Component를 벗어났기 때문에 this가 undefiend 됩니다.
~~~

## 3-4. 자바스크립트 비동기 처리 - Callback  
다음과 같이 코드를 작성하였습니다.  
~~~js
function fetchData() {
	// 1. 변수 생성
	var result = [];
	// 2. 데이터 요청
	$.ajax({
		url: 'https://api.hnpwa.com/v0/news/1.json',
		success: function(data) {
			console.log('데이터 호출 결과', data);
			result = data;
		}	
	});
	// 3. 결과 출력
	console.log('함수 결과', result);
}
~~~
출력 결과는 다음과 같이 나옵니다..  
~~~shell
함수 결과: []
데이터 호출 결과 : [{ ... }]
~~~
이것은 `$.ajax`가 비동기로 처리되기 때문에 발생하는 현상입니다.  
따라서, CallBack 함수 내 데이터 처리 로직이 작성되면 코드가 복잡하게 됩니다.  
이에 대응하여 나온 것은 Promise 입니다.

## 3-5. 자바스크립트 비동기 처리 - Promise  
Promise 동작을 직접 구현하여 이해합니다.  
~~~js
function callAjax() {
	return new Promise(function(resolve, reject) {
		$.ajax({
			url: 'https://api.hnpwa.com/v0/news/1.json',
			success: function(data) {
				resolve(data);
			}
		});
	})
}
function fetchData() {
	// 1. 변수 생성
	var result = [];
	// 2. 데이터 요청
	callAjax()
		.then(function(data) {
			// 3. 결과 출력
			console.log('데이터 호출 결과', data);
			result = data;
			console.log('함수 결과', result);
		});
}
~~~