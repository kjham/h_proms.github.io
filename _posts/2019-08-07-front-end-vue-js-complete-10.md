---
layout: post
title: 'Vue.js 완벽가이드 - 10'
author: kjham.ham
date: 2019-08-07 15:00
tags: [swtech,java,vue-js,front-end]
image: assets/images/VUE_JS.jpg
---

# 10. 리팩토링 : 비동기 처리  

비동기 처리 패턴으로 가장 많이 사용하는 async & await 방식을 이용합니다.  
비동기 처리 패턴을 이용하면 함수도 많이 줄어들고 이용하기 더 쉽도록 구성할 수 있습니다.  

~~~js
<!-- 기존의 JavaScript는 다음과 같이 CallBack을 이용하여 구현하였습니다. -->
var id = $.get('domain.com/id', function(id){
    if (id === 'jhon') {
        var products = $.get('domain.com/products', function(products){
        	console.log(products);
        });
    }
});

<!-- Vue에서 Promise를 이용하면 다음과 같이 할 수 있습니다. -->
function getId() {
	return new Promise(function(resolve, reject){
		$.get('domain.com/id', function(id){
			resolve(id);
		})
	})
}
getId()
	.then(function(id){
		if (id === 'jhon'{
			$.get('domain.com/products', function(products) {
				return new Promise(..)
			})
		})
	})
	.catch() { };

<!-- 다음과 같이 함수로 작성한다면 더 명확하게 표현할 수 있습니다. -->
getId()
	.then(getProduct())
	.catch(getLogProduct());	
~~~

## 10-1.  Async Await 소개
어싱크 어웨이트는 자바스크립트 비동기 처리 패턴의 최신 문법입니다.  
Promise와 Callback에서 주는 단점들을 해결하고 자바스크립트의 비동기적 사고 방식에서 벗어나 동기적(절차적)으로 코드를 작성할 수 있게 도와줍니다.  

async 함수의 기본 문법은 다음과 같습니다.  
~~~vue
async function fetchData() {
	await getUserList();
}
~~~

다음과 같이 사용자를 조회하고 사용자의 아이템을 최종적으로 변수에 담는 코드를 작성하였습니다.  
~~~js
loginUser() {
	axios.get('사용자 조회 주소')
		.then(response => {
			if(response.data.id === 1) {
				console.log('인증이 완료되었습니다.');
				axios.get('사용자의 아이템 조회 주소')
					.then(response => {
						this.items = response.data;
					})
			}
		})
		.catch(error => console.log(error));
}
~~~

async & await를 이용하면 다음과 같이 코드를 작성할 수 있습니다.  
코드가 간결해졌고, 명확해진 것을 볼 수 있습니다.  
~~~js
async function loginUser() {
	var response = await axios.get('사용자 조회 주소');
	if(response.data.id == 1) {
		var list = await axios.get('사용자의 아이템 조회 주소');
		this.items = list.data;
	}
}
~~~

## 10-2. Async Await 에러 처리 방법  
기존 Promise의 예외처리는 네트워크 전송과 관련된 에러만 처리가 가능했습니다.  
다음과 같이 async await에서의 try-catch문을 사용하면 모든 에러에 대한 처리가 가능합니다.  
~~~js
async function loginUser() {
	try {
        var response = await axios.get('사용자 조회 주소');
        if(response.data.id == 1) {
            var list = await axios.get('사용자의 아이템 조회 주소');
            this.items = list.data;
        }
	} catch(error) {
		console.log(error);
	}
}
~~~

## 10-3. Async Await 적용  
기존 작업하던 프로젝트에 async await를 적용해봅니다.  

`/store/actions.js`에 먼저 async await를 적용합니다.
`try-catch`문은 actions보다는 일반적으로 api 단에서 작업하는 것을 권장합니다.  
~~~js
import { fetchNewsList, fetchAskList, fetchJobsList, fetchUserInfo, fetchCommentItemInfo, fetchList } from '../api/index.js';

export default {
    // Promise
    // FETCH_NEWS(context) {
    //     return fetchNewsList()
    //         .then(response => {
    //             context.commit('SET_LIST', response.data);
    //             return response;
    //         })
    //         .catch(function (error) {
    //             console.log(error);
    //         });
    // },
    // async await
    async FETCH_NEWS(context) {        
        const response = await fetchNewsList();
        context.commit('SET_LIST', response.data);
        return response;
    },
    async FETCH_JOBS(context) {
        try {
            const response = await fetchJobsList();
            context.commit('SET_LIST', response.data);
            return response;
        } catch (error) {
            console.log(error);
        }
    },
    async FETCH_ASK(context) {
        // Fetch Ask는 Api단에서 error 처리를 하였습니다.
        const response = await fetchAskList();
        context.commit('SET_LIST', response.data);
        return response;
    },
    async FETCH_USER({ commit }, name) {
        const response = await fetchUserInfo(name);
        commit('SET_USER', response.data);
        return response;
    },
    async FETCH_ITEM({ commit }, itemId) {
        const response = await fetchCommentItemInfo(itemId);
        commit('SET_ITEM', response.data);
        return response;
    },
    async FETCH_LIST({ commit }, pageName) {
        const response = await fetchList(pageName);
        commit('SET_LIST', response.data);
        return response;
    },
}
~~~

`/api/index.js`에 async await와 try catch문을 일부 적용해봅니다.  
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

async function fetchAskList() {
    try {
        const response = axios.get(`${config.baseUrl}ask/1.json`);    
        return response;
    } catch (error) {
        console.log(error);        
    }
}

function fetchList(pageName) {
    try {
        return axios.get(`${config.baseUrl}${pageName}/1.json`);    
    } catch (error) {
        console.log(error);        
    }
}

function fetchUserInfo(name) {
    return axios.get(`${config.baseUrl}user/${name}.json`);
}

function fetchCommentItemInfo(id) {
    return axios.get(`${config.baseUrl}item/${id}.json`);
}

export {
    fetchNewsList,
    fetchJobsList,
    fetchAskList,
    fetchList,
    fetchUserInfo,
    fetchCommentItemInfo,    
}
~~~

async await는 굉장히 자주 사용되는 패턴이니 잘 익혀두면 좋을 것 같습니다.  