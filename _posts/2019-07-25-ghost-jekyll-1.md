---
layout: post
title: 'Ghost-Jekyll 적용하기 - 1'
author: kjham.ham
date: 2019-09-01 23:52
tags: [swtech,ghost,jekyll]
image: /files/covers/VUE_JS.jpg
createdate: 2019-07-24 15:38
comments: true
---

# Github Page & Jekyll 적용

# 1. Github Page 생성

다음 주소로 접속 합니다.
https://pages.github.com/

Github에 회원가입 후, 페이지 생성 절차를 진행합니다.

# 2. Jekyll 구축

저는 Windows 환경에서 구축을 진행하였습니다.

## 2-1. 루비 설치

다음 주소로 접속하여 루비를 다운로드 합니다.  
https://rubyinstaller.org/downloads/

저는 Ruby+Devkit 2.5.5-1 (x64) 를 다운로드 하였습니다.  
참고로 Jekyll은 Ruby 2.x 이상의 버전에서만 호환됩니다.  
그리고 별도로 DevKit 를 설치할 필요 없는 통합 버전 입니다.  

## 2-2. Jekyll 설치
설치 진행 중 참고 사항  
- ' Add Ruby executables to your PATH' 는 체크합시다. 환경 변수에 path를 추가합니다.  

Windows 10 기준, PowerShell 를 실행합니다. (Command Prompt 와 동일)  

다음 명령어로 하나하나 설치합니다.
~~~shell
# jekyll bundler 설치
$ gem install jekyll bundler
$ gem install rouge
$ gem install wdm
~~~

# 3. Jekyll 실행
jekyll 블로그 생성을 위한 폴더로 이동합니다.

다음 명령어를 실행합니다.
~~~shell
$ jekyll new myblog
$ cd myblog
$ jekyll serve
# 다음과 같이 뜨면 성공
Configuration file: C:/Ruby25-x64/user/myblog/_config.yml
            Source: C:/Ruby25-x64/user/myblog
       Destination: C:/Ruby25-x64/user/myblog/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
       Jekyll Feed: Generating feed for posts
                    done in 0.688 seconds.
 Auto-regeneration: enabled for 'C:/Ruby25-x64/user/myblog'
    Server address: http://127.0.0.1:4000/
~~~

# 4. 이슈 사항

## 4-1. Jekyll 버전 에러  
Jekyll 버전이 맞지 않을 경우 build가 안될 때가 있습니다.  
jekyll 소스 폴더 내 Gemfile 을 삭제 후 다음과 같이 실행합니다.
~~~shell
$ jekyll -v
$ bundle install 
...
...
$ jekyll -v
~~~
버전이 갱신된 것을 확인할 수 있습니다.

## 4-2. Liquid Exception 에러  
Local 에서 Jekyll을 실행할 때  
`Liquid Exception: incompatible character encodings: UTF-8` 와 같은 에러가 발생할 떄가 있습니다.  
~~~html
If you use UTF-8 encoding, make sure that no BOM header characters exist in your files or very, very bad things will happen to Jekyll. This is especially relevant when you’re running Jekyll on Windows.

Additionally, you might need to change the code page of the console window to UTF-8 in case you get a Liquid Exception: Incompatible character encoding error during the site generation process. It can be done with the following command:
~~~

즉, Terminal에서 `chcp 65001`를 입력하 `jekyll serve`를 실행합니다.  