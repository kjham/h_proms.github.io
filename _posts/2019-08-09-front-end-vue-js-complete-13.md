---
layout: post
title: 'Vue.js 완벽가이드 - 13'
author: kjham.ham
date: 2019-08-09 10:00
tags: [swtech,java,vuejs,frontend]
image: /files/covers/VUE_JS.jpg
---

# 13. 서비스 배포 환경 구성  

## 13-1. CLI로 생성한 프로젝트 배포  
`npm run build`는 배포 명령어 입니다.  
그 동안 만든 `vue-news` 폴더의 터미널을 실행합니다.  
그리고 `npm run build`를 실행하면, `dist`라는 폴더와 파일이 생성됩니다.  
`dist`내 파일을 업로드하여 서비스를 구동할 수 있습니다.  

## 13-2. Netlify를 이용한 배포 실습  
`netlify` 사이트를 접속하여 `sign-up`을 하고 `github`를 선택합니다.  
그리고 github의 `repository`를 선택합니다.

`build command`에 `npm run build`를 입력하고,  
`publish directory`에 `dist`를 입력합니다.  
그리고 `deploy`를 실행합니다.  

`preview`를 클릭하면 사이트를 미리 확인할 수 있습니다.  

## 13-3. 환경변수 파일  
`env`파일을 생성하여 환경 변수를 선언할 수 있습니다.  
`src` 폴더 내 `.env` 파일을 생성합니다.  
`APP_TITLE`라는 변수명와 값을 선언하고 `App.vue`에서 바로 접근할 수 없습니다.  
그러나 vue-cli 3.0부터는 환경변수명에 `VUE_`라는 prefix를 선언하면 바로 접근이 가능합니다.  
~~~js
# Temp
변수=값
APP_TITLE=HELLO
# VUE_ 라는 prefix를 두면 Application에서 바로 접근할 수 있습니다.
VUE_APP_TITLE=HELLO
~~~

그리고 `App.vue`에서 호출해봅니다.  
~~~vue
<!-- 코드 생략 -->
  created() {
    // 오류 발생
    // console.log(process.env.APP_TITLE);    
    console.log(process.env.VUE_APP_TITLE);    
  },
<!-- 코드 생략 -->
~~~