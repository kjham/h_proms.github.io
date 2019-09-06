---
layout: post
title: 'IntelliJ 설치 및 세팅'
author: kjham.ham
date: 2019-09-04 10:00
createdate: 2019-08-12 10:00
tags: [swtech,ide,intellij]
---

# 1. IntelliJ 설치  
`https://www.jetbrains.com/` 로 접속하여 IntelliJ 를 다운로드 합니다.  
저는 Ultimate Edtion으로 설치하려 합니다.  

설치 후 실행합니다.  
최초 실행 시 라이선스 인증을 수행합니다.  

다음 파일을 설치합니다.  
- sliksvn 세팅  
`https://sliksvn.com/download/`  

## lombok 설치  
`Ctrl + Alt + S` 를 누른 후, `Plugins` 로 들어가서  
Marketplace를 이용하여 원하는 플러그인을 설치해도 됩니다.  
`lombok`을 검색하여 설치합니다.  
`Ctrl + Alt + S` 를 누른 후, 'Annotation Processings '에서 'Enable annotation processing' 체크  

# 2. 실행 및 세팅  

## Terminal 실행  
`npm install -g vue-cli`로 vue-cli를 설치  
그러나, `vue-cli-service`를 찾을 수 없다는 오류 발생 ..  

`npm install -g @vue/cli` 로 vue-cli-service 설치  

그러나 vue-cli-service 로 등록된 명령어가 없다면서 실행이 안되는 현상이 발생했습니다.  
(다른 사람 PC에서는 이 문제가 없이 잘 되었음)  
따라서 개발망에서 관리하는 리파지토리 주소를 수동으로 등록 했습니다.  
`npm config set registry [리파지토리 주소]`  

그리고 다시 `npm install` 실행  

`npm run serve`를 이용하여 서비스를 구동하는데 성공하였습니다.  
