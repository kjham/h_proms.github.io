---
layout: post
title: '참여 후기: Open Infrastructure & Cloud Native Days Korea 2019'
author: hunter.jo
date: 2019-07-23 10:00
tags: [infrastructure, cloud, openstack, kubernetes, ceph, ocp]
image: /files/covers/openinfradays19.png
---

지난 주 18일부터 19일까지 양일간 진행된 Open Infrastructure & Cloud Native Days Korea 2019 행사에 카카오가 후원했습니다. 일정상 행사장에 참여하지 못한 분들에게 카카오가 참여한 내용을 소개합니다.

이번 행사는 OpenStack, Kubernetes, Ceph, OCP 한국 커뮤니티가 힘을 합쳐서 개최하였고, Open Infrastructure 와 관련된 많은 분야를 어우르는 커뮤니티 연합 성격의 컨퍼런스로 진행됐습니다.

오픈 소스 커뮤니티에 참여하고 있는 저희들 입장에서도 하나의 분야가 아닌 서로 연관된 여러 분야의 기술들을 한 자리에서 보고, 서로 논의하고 공유할 수 있는 기회에 카카오가 키노트와 다양한 발표 세션을 포함해 기술 시연 부스에 참가할 수 있어 기뻤습니다.

## 행사 정보

* 웹사이트: <https://openinfradays.kr/>
* 일시: 2019년 7월 18~19일,. 9:00AM ~ 6:00PM
* 장소: 페럼타워, 삼화타워, T타워 (을지로입구)
* 주최: 국내 오픈소스 커뮤니티 (OpenStack, Kubernetes, Ceph, OCP)

## 부스

카카오의 스폰서 부스에는 카카오의 클라우드 플랫폼인 9rum, Krane 시연과 채용관련 Q&A 질의응답을 진행했습니다. QR 코드를 스캔하면 채용관련 설문으로 연결되고, 응답하신분들께는 라이언/어피치/튜브가 이쁘게 프린트된 유리컵 세트를 드렸습니다. 준비해간 기념품과 경품이 빠르게 소진되어 뒤늦게 방문해주신 분들께 전해드리지 못한 점 양해부탁드립니다.

![부스](/files/openinfradays19-booth.jpg)

![부스](/files/openinfradays19-booth2.jpg)

부스에서 받은 질문중에 가장 재밌었던 질문은 ‘아니 외부 사람은 써보지도 못 할걸 왜 전시하는거에요? 혹시 카카오가 클라우드 잘 하는거 자랑하시려구요?’ 였습니다. 네, 아직은 외부 분들은 사용하지 못하지만 혹시 또 언젠가는 외부에서도  쓸 수 있지 않을까요? 그리고 주요 제품들을 오픈소스화 할 계획을 가지고 있으니까, 꼭 허무 맹랑한 이야긴 아닌것 같습니다. 잘 준비해서 공개할 수 있도록 해보겠습니다. 

## 키노트 세션

### Cloud, Current Currents and Directions with open powers

![키노트](/files/openinfradays19-keynote.jpg)

클라우드의 현재 쟁점 이슈(AI 클라우드 서비스화, 이종(heterogeneous)클라우드, 오픈소스진영과 클라우드 벤더간의 라이센싱 이슈)들에 대해서 소개하고, 향후 클라우드가 진화해가는데 있어서 오픈소스와 오픈 인프라가 어떻게 도움을 주는지에 정리해봤습니다. 마지막으로, 카카오가 이런 오픈소스 흐름을 이용해서 앞으로의 클라우드에 적용할지에 대해서도 소개했습니다. 

(키노트를 소개한 기사: <https://news.v.daum.net/v/20190721094650099>)

**공용준 / KAKAO**

<img src="/files/oid-andrew.jpeg" style="width:120px; border-radius:50%; border: 1px solid #ccc; margin-left: 20px;" class="pull-right" /> 공용준(Andrew)은 카카오에서 클라우드 컴퓨팅 파트에서 클라우드 기술 리딩을 맡고 있으며, 프라이빗/퍼블릭 클라우드 서비스와 그에 필요한 기술들을 연구, 개발해서 실제 서비스에 적용하고 있다. 중소기업 발전을 위해 한국정보화진흥원에서 중소기업 기술 자문위원, 한국 데이터베이스 진흥원 자문위원으로도 활동하고 있다. 2011년에 정통부 산하의 클라우드 정책 연구단 기술고문을 역임했으며, 주요 저서로는 『카프카: 데이터 플랫폼의 최강자』(책만,2018),『클라우드 API를 활용한 빅데이터분석』(에이콘,2015), 『실전 클라우드 인프라 구축기술(한빛, 2014)』이 있다.

***

## 발표 세션 

*DAY-1, 15:00 ~ 15:40 @ Track 3*

### kubernetes scheduler deep dive

Kubernetes scheduler component의 동작방식 및 알고리즘에 대해 알아보고 scheduler 확장에 대해서 알아 봅니다.

**<img src="/files/oid-dj.jpg" style="width:120px; margin-left:20px; border-radius:50%; border: 1px solid #ccc;" class="pull-right" />김동진 / 카카오뱅크**

카카오뱅크에서 cloud native 사업의 일환으로 퍼블릭 클라우드 도입을 준비중입니다.특히 컨테이너 서비스를 위한 Kubernetes 플랫폼 구축 및 설계를 담당하고 있습니다.

***

*DAY-1, 17:00 ~ 17:40 @ Track 3*

### Source to URL without Dockerfile

Knative와 buildpack을 이용해서 dockerfile이 없는 소스의 코드를 분석해서 컨테이너화 한 다음에 Kubernetes 클러스터에 실행하는 방법을 알아봅니다. 서버리스 플랫폼인 knative의 전반적인 내용과 buildpack.io의 내용을 살펴봅니다. 간단한 시연을 포함해서 발표를 보신 분들이 knative와 buildpack을 사용해 보실 수 있는 방법을 안내합니다.

**정원천 / KAKAO**

<img src="/files/oid-hardy.jpeg" style="width:120px; margin-left: 20px; border-radius:50%; border: 1px solid #ccc;" class="pull-right" /> 카카오의 내부 컨테이너 클라우드 플랫폼을 개발하고 있습니다. 최근에는 어떻게하면 유용한 컨테이너 플랫폼을 만들고 효율적으로 안정적인 운영을 할 수 있을지를 많이 생각하고 있습니다.

* 카카오 클라우드디플로이셀 셀장
* Certified Kubernetes Administrator

***

*DAY-2, 12:00 ~ 12:40 @ Track 2*

### 카카오 T 택시 사례를 통해 살펴보는 카카오 클라우드의 Kubernetes as a Service

카카오의 프라이빗 클라우드 환경에서 KaaS(Kubernetes as a service) 를 개발/운영하며 겪어온 시행착오와 아키텍처 개선 작업들, 수 백 개의 쿠버네티스 클러스터 들을 서비스로서 제공하기 위해 개발한 유용한 기능들을 소개합니다. 그리고 ‘카카오 T 택시’ 에 적용된 쿠버네티스 기반 서비스 사례를 통해 실제 서비스에서의 적용 과정과 운영은 어떻게 되고 있는지 공유합니다.

**홍석용 / KAKAO**

* <img src="/files/oid-dennis.jpeg" style="width:120px; border-radius:50%; border: 1px solid #ccc;" class="pull-right" /> Works- OpenStack 기반 LG전자 및 LG 그룹사 Private Cloud 구축/운영
* OpenStack Ironic(bare Metal) 구축 및 Inhouse Driver 개발
* 카카오 클라우드 Mesos as a service, DKOSv2 운영
* 카카오 클라우드 Kubernetes as a service, DKOSv3 개발/운영Community Activity
* 2017 OpenStack Day: 하이브리드 클라우드를 위한 Horizon AWS Plugin
* 2018 OpenInfra Day: Mesos to Kubernetes, Cloud Native 서비스를 위한 여정
* 쿠버네티스 코믹스 한글화: https://goo.gl/5mTvtm

**<img src="/files/oid-theodore.jpeg" style="width:120px; height:120px; border-radius:50%; border: 1px solid #ccc;" class="pull-right" />박광열 / KAKAO**

* 티몬 물류 시스템 개발/운영
* 카카오 T for business 플랫폼 개발/운영
* 카카오 T 택시 서버 개발/운영

***

*DAY-2, 14:00 ~ 14:40 @ Track 2*

### KOCOON – KAKAO Automatic K8S Monitoring

- 기존 모니터링 시스템(KEMI)에서 KOCOON을 진행한 이유
- 대용량 K8S Cluster 모니터링 자동화를 진행하면서 겪었던 내용들에 대해 공유
- (#kocoon #k8s #prometheus #helm #chart #OOM)

**임성국 / KAKAO**

* <img src="/files/oid-issac.jpg" style="width:120px; height:120px;border-radius:50%; border: 1px solid #ccc;" class="pull-right" /> 웹공학 석사 @ KAIST
* 공통플랫폼 개발 @ ETRI
* Open API GW 개발 @ KT
* KAKAO Monitoring Cloud 개발 @ KAKAO

***

앞으로도 카카오는 개발자 커뮤니티에 대한 지속적인 후원과 기술 발표 및 강연에 적극 참여하도록 하겠습니다. 많은 관심과 응원 부탁드립니다.

