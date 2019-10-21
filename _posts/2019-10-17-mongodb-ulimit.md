---
layout: post
title: 'MongoDB에서 Too Many Open File 현상'
author: kjham.ham
date: 2019-10-17 10:00
tags: [swtech,java,ulimit,mongodb]
---

현재 프로젝트에서 두 대의 서버로 MongoDB의 ReplicaSet를 구성하였습니다.

그런데, 운영 중 서버B에서 동작중인 RS1 Secondary가 죽기도 하고 ..
서버A에서 동작중인 RS1 Primary가 죽기도 하는 현상이 간헐적으로 발생하였습니다.

MongoDB 로그를 분석 중, 여러 에러 코드가 있었지만
Too many file opens와 함께 Prefix로 [Error] 를 발견하였습니다.

우선 1차적인 조치로 이 것을 해결하기 위해 살펴보던 중
Linux에서 허용하는 open files가 4096으로 되어 있는 것을 보았습니다.

우선 이 값을 최대치로 높여주기 위핸 조치를 취했습니다.

`ulimit -a` 로 현재 시스템에서의 수치 확인
~~~shell
...
open files          4096
max user processes  4096
...
~~~

`/etc/security/limits.conf` 를 `vi` 편집기로 열어봅시다.
~~~shell
...
*       soft    nofile  65535
*       hard    nofile  65535
## 아래와 같이 특정 계정별로도 지정 가능
root    soft    nofile  65535
root    hard    nofile  65535
...
~~~

그리고 `/etc/profile`도 다음과 같이 수정합시다.
~~~shell
...
## max user processes 값 수정
ulimit -u 65535
## open files 값 수정
ulimit -n 65535
...
~~~

이제 확인해봅시다. `ulimit -a`
정상적으로 바뀐 것을 볼 수 있습니다.

1차적인 조치는 이렇게 완료하였지만, 여전히 문제가 발생합니다.  

일부 프로젝트 간 HTTP Web Socket을 이용하여 과도하게 주고 받는 현상을 발견했습니다.  
시스템에 문제가 있을 때, Web Socket으로 실시간 공지를 날려주는 기능임을 확인했습니다.

실시간 공지를 너무 빈번하게 발생하도록 구현되어 있네요.
적절한 조치를 취하여 알림을 최소화 하고, 이틀간 MongoDB RS 서버의 Log를 지켜보았습니다.

안정적으로 HeartBeat가 발생한 것을 확인하고 이 문제는 완료처리가 되었네요.