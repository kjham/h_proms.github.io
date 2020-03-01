---
layout: post
title: 'Network&Cloud 용어집'
author: kjham.ham
date: 2020-02-11 13:00
create-date: 2020-02-04 17:35
tags: [swtech,network,cloud,aws,cdn]
##image: /files/covers/effectivejava.jpg
comments: true
---

# Cloud

## AWS (Amazon)

### AWS 용어집  
https://docs.aws.amazon.com/ko_kr/general/latest/gr/glos-chap.html  

### AZ (Availity Zone)  
AWS 서비스가 리전들의 집합이라면, Region은 AZ(가용역역)들의 집합입니다.  
AZ는 데이터 센터이며, 실제 물리저그로 완전히 돌립되어 있지만, AWS 콘솔 상에서 리소스별로 구분하지는 않습니다.  
하나의 Region안에 여러 개의 데이터 센터를 운영함으로써 가동률 혹은 가용성을 크게 높일 수 있습니다. 가용성(Availibility)은 서비스를 정상적으로 사용가능한 정도라고 볼 수 있습니다.  

### Cloud Front  
전세계에 위치한 Edge location을 이용하여 효율적인 컨텐츠 배포 구조를 제공하는 서비스  
#### 장점  
* 캐싱  
미리 캐시되어 있는 데이터를 속도가 빠른 곳에서 받을 수 있습니다.  
* 부하분산  
한 곳의 서버가 아니라 여러 곳으로 분산을 시킬 수 있습니다.  

### CNAME  
Cloud Front 에서 부르는 도메인 이름  

### CIDR  
(Classless Inter-Domain Routing)  
클래스 없는 도메인간의 라우팅 기법으로 1993년 도입되기 시작한, 최신의 IP 주소 할당법입니다.  
사이더는 기존의 IP 주소 할당 방식이었던 네트워크 클래스를 대체하였습니다.  

* 예 1) 192.168.32.0/26  
CIDR 26의 값으로 Subnet Mask를 구해보면 다음과 같습니다.  
11111111(255) . 111111111(255). 11111111(255). 11000000(192)  
Subnet Mask : 255.255.255.192  
따라서 `255-192`를 하면 `63`이 됩니다.  
BroadCast : 192.168.32.63  
IP주소 영역: 192.168.32.1 ~ 192.168.32.62  

### DevOps  
개발팀(Developmenet) 과 운영팀(Operation) 두 단어의 조합으로 두 팀 간의 소통이 원활히 이루어질 수 있도록 하는 것을 의미합니다.  
클라우드 서비스가 확장되면서 개발과 운영을 통합하는 데브옵스 개념이 정차 증가하고 있습니다.  

### Edge Cache Server  
캐시 서버는 각 컨텐츠에 대한 유효시간을 관리하고 있습니다.  
각 사용자가 컨텐츠를 요청했을 때, 유효시간에 따라 캐시서버 동작 방식이 다릅니다.  

* 컨텐츠가 없는 경우  
Origin Sever로 부터 요청하여 캐시서버에 저장합니다.  
* 컨텐츠가 있는 경우 (유효시간 20초 이상 (100초 중 20초 이상 남음))  
사용자게에 바로 전달합니다.
* 컨텐츠가 있는 경우 (유효시간 20초 미만 (100초 중 20초 미만 남음))  
캐시서버에 수정되었는지 체크하고, 사용자에게 전달합니다. 그리고 유효시간을 리셋합니다.  
만약 수정되었다면 캐시 데이터를 변경합니다.  

### GSLB  

#### GSLB 장점  
(장점 : https://www.netmanias.com/ko/?m=view&id=blog&no=5620)  
(Global Server Load Balancing)  
지역적으로 서버가 분산된 환경에서 GSLB를 이용하여 아래와 같은 이점이 있습니다.  
(1) 서비스 가용성 제공 (Disaster Recovery)  
(2) 서버 부하 분산 (Server/Site Load Balancing)  
(3) 빠른 서비스 응답 (Low Latency by Network Proximity)  
(4) 가까운 곳으로 접속 (Nearest by Geographic Proximity)  

#### GSLB 정책  
(참고1 : https://www.netmanias.com/ko/?m=view&id=blog&no=5622)  
(참고2 : https://www.netmanias.com/ko/?m=view&id=blog&no=5624)  
① Server Health -  살아있는 사이트 선택  
② SLB Session & Network Capacity Threshold - 과부하 상태가 아닌 사이트 선택  
③ Network Proximity - 응답 속도가 빠른(Low Latency) 사이트 선택  
④ Geographic Proximity - 지리적으로 가까운 사이트 선택  
⑤ SLB Connection Load - 새로운 연결 요청이 적은 사이트 선택  
⑥ Site Preference - 운영자의 정책에 의해 특정 사이트 선택  
⑦ Least Selected - 균등하게 사이트 선택  
⑧ Static Load Balancing - 균등하게 사이트 선택  

### OAI  
(Origin Access ID) Amazon S3 Bucket와 CloudFront를 함께 사용하면, OAI를 통해 쉽게 접근을 제한할 수 있습니다.

### Region  
AWS는 물리적으로 떨어진 지역에 여러 개의 클라우드 인프라를 운영하는데, 이 지역을 어원 그대로 리전(region)이라고 부릅니다.  
물리적으로 완전히 물리되어 있으며, AWS 콘솔 상에서도 완전히 다른 리소스로 구분합니다.  
전 세계에 뻗어있는 AWS 인프라는 크게 언급한 리전과 가용 영역(AZ) 그리고 에지 로케이션(Edge Location)으로 나눌 수 있습니다.  

## GCP (Google)  

### GCP CDN  
CDN 서비스

## Azure (Microsoft)  

### Azure CDN  
CDN 서비스  

## NCP (Naver)  

# Local Network  

## APN 이란?  
(참고 : https://www.netmanias.com/ko/?m=view&id=blog&no=5647)  
(Application Delivery Network)  
ADN도 CDN과 같이 사용자와 Origin Server간에 지리적인 거리로 인해 발생하는 "느린 응답속도/다운로딩 타임"를 해결하는 기술이지만 CDN 처럼 Content를 캐싱하지 않습니다. 대신 망 지연(Latency)이 가장 큰 구간(인터넷)에 위치한 서버들(ADN 서비스용 서버) 간에 다양한 기술들을 적용하여 트래픽을 빨리 전달 할 수 있도록 합니다. ADN은 Content 캐싱이 불가능한 dynamic content(예. 옥션 장바구니, 사이트 검색 결과 등과 같이 개인별로 다른 content가 전달되는 경우)에 적용하여 응답 속도를 향상시켜 줄 수 있습니다.  

### ADN 핵심기술  
* Route (Path) Optimization: 사용자와 Origin Server 사이의 라우팅 경로 최적화 (BGP 한계 극복)  
* Packet Redundancy (FEC: Forward Error Correction): 동일 패킷을 서로 다른 경로를 통해 중복 전달하여 패킷 손실 시 재전송 없이 데이터를 복구할 수 있도록 함  
* Data Compression: 데이터 압축을 통해 전송량 최소화  
* Data De-duplication: 동일 byte-stream에 대해서는 중복 전송을 하지 않아 전송량 최소화  
* Application Optimization: 응용 레이어(예. HTTP) 프로토콜 최적화 (예. Web Objects Prefetching, Pipelining)  
* TCP Optimization: TCP 프로토콜 최적화 (예. Fast Start, Advanced Congestion Avoidance, Large Window Size)
즉, CDN은 `캐싱기술`, ADN은 `가속기술` 이라고 할 수 있습니다.  

## CDN 이란?  
(참고 : https://www.netmanias.com/ko/?m=view&id=blog&no=5654)  
### Static Caching  
* 사용자의 요청이 없어도 Origin Server에 있는 Content를 운영자가 미리 Cache Server에 복사함  
* 따라서 사용자가 Cache Server에 접속하여 Content를 요청하면 무조건 그 Content는 Cache Server에 있음! (100% Cache Hit)  
* 대부분의 국내 CDN에서 이 방식을 사용함 (예. Pooq 동영상 스트리밍/다운로드, NCSOFT 게임파일 다운로드 등)  

### Dynamic Caching  
* 최초 Cache Server에는 Content가 없음  
* 사용자가 Content를 요청하면 해당 Content가 있는지 확인하고, 없으면(Cache Miss) Origin Server로 부터 다운로드 받아(Cache Fill) 사용자에게 전달해 줌  
* 이후 동일 Content를 요청 받으면 저장(캐싱)된 Content를 사용자에게 전달(Cache Hit)  
* 각 Content는 일정 시간(TTL)이 지나면 Cache Server에서 삭제될 수 있고, 혹은 Origin Server를 통해 Content Freshness 확인 후에 계속 가지고 있을 수 있음  
* Akamai, Amazon과 같은 Global CDN 업체, 그리고 Cisco나 ALU의 통신사업자향 CDN 장비 솔루션에서 이 방식을 지원함

## IPv6  
* IP 주소의 길이가 128비트 체계로 확장 (거의 무한대로 주소 부여 가능)  
* 네트워크 속도 증가  
* 높은 품질의 서비스 제공  
* 데이터 무결성 및 비밀 보장  

## OSI Layer 7 Model 
* (1계층) Physical Layer  
* (2계층) DataLink Layer  
* (3계층) Network Layer  
* (4계층) Transport Layer  
* (5계층) Session Layer  
* (6계층) Presentation Layer  
* (7계층)  Application Layer  

## Request Routing  
(참고 : https://www.netmanias.com/ko/?m=view&id=blog&no=5630)  
Request Routing은 사용자의 Content Request에 대해 최적의 Cache Server를 선택해 주는 기능입니다.  
이를 위해 CDN 망에는 Request Router가 존재합니다. (이름에 Router가 들어갔다고 IP Routing의 OSPF, BGP와 같은 라우팅 기능을 수행하는 것은 아닙니다)  
Request Router는 DNS 기능을 지원하며 사용자의 Content 요청을 Cache Server로 Redirection해 주는 방식에 따라 DNS-based Request Routing과 Service-based Request Routing으로 나눌 수 있습니다.  

* DNS-based Request Routing  
1) 사용자 단말이 `orgin.example.com`를 요청했을 때, 이에 대한 `example.com DNS 서버`는 CNAME으로 `csp123.cdn.kt.com`를 응답합니다.  
2) 그리고 `Local DNS 서버`는 `Request Router`로 `DNS Query`를 전달하여 `Host Name = csp123.cdn.kt.com`에 대한 IP 주소를 요청하고 최종적으로 가장 빠른 `캐시 서버`의 IP 주소를 응답합니다. 
3) 그리고 사용자 단말은 응답받은 `캐시 서버`로 부터 데이터를 수신 받습니다.  
이 방식에서 Request Router는 사용자 단말과의 통신이 없고 또 Local DNS 서버가 단말의 IP 주소를 Request Router로 전달하지 않기 때문에 Cache Server를 선택할 때 사용자의 위치 정보는 활용되지 못하며 또한 사용자가 요청한 Content의 서비스 유형(예. RTMP, HLS 등)을 Request Router가 알 수 없으므로 "서비스 기반의 라우팅(Cache Server 선택)이 불가능"하다는 단점을 가집니다.  
* Service-based Request Routing  
1) 사용자 단말이 `orgin.example.com`를 요청했을 때, 이에 대한 `example.com DNS 서버`는 CNAME으로 `csp123.cdn.kt.com`를 응답합니다.  
2) 그리고 `Local DNS 서버`는 `Request Router`로 `DNS Query`를 전달하여 `Host Name = csp123.cdn.kt.com`에 대한 IP 주소를 요청하고 `Request Router`는 `1.1.1.1`로 응답합니다.  
3) 사용자 단말은 Redirection으로 `Request Router`에 `캐시 서버`의 IP 주소를 요청하고, 응답받은 `캐시 서버`의 IP 주소와 통신하여 데이터를 수신 받습니다.  

이 방식은 사용자 단말이 직접 Request Router로 Content Request를 보내게 함으로써,
* Request Router가 사용자 단말의 IP 주소를 알 수 있어 사용자에서 지리적으로 가장 가까운 Cache Server를 선택해 줄 수 있습니다.
* Content Request의 URI 정보를 통해 서비스 요청 타입(RTMP streaming, Microsoft Smooth Streaming, Progressive Download 등)을 알 수 있어 해당 서비스를 지원하는 Cache Server를 선택할 수 있습니다.
* Cache Server 선택 후 Content의 URI를 hash하여 그 hash 값을 Request Router가 관리함으로써 이후 동일 Content 요청이 왔을 때 동일 Cache Server를 선택(redirection)함으로써 Cache Hit 율을 높일 수 있게 해 줍니다.

Redirection 하는 방법 : HTTP 302 Redirection, HTTP ASX Redirection, RTSP 302 Redirection, RTMP 302 Redirection  

## Router  
서로 다른 지역의 컴퓨터를 연결하는 기기입니다. 2개 이상의 네트워크 간 데이터 전송을 위해 최적 경로를 설정해주며, 데이터를 해당 경로를 따라 한 통신망에서 다른 통신망으로 통신 가능하게 도와주는 인터넷 접속 장비입니다.  
내부 네트워크는 컴퓨터 기종이나 운영체제, 프로토콜 등을 확실히 알 수 있어서 네트워크의 최적화를 이룰 수 있지만, 외부와 연결할 때는 이러한 정보를 알 수 없습니다. 이러한 임의의 네트워크와 내부 네트워크를 연결하기 위한 네트워크 장비가 바로 라우터입니다.  

## Switch  
여러 컴퓨터를 연결하는 네트워크 분배기 역할을 합니다.  하나의 네트워크 라인에 여러 대의 컴퓨터에 랜 케이블을 꽂을 수 있도록 분배하는 장비입니다.  

## TCP/IP 모델  
- (1계층) Network Interface  
- (2계층) Internet Layer  
- (3계층) Transport Layer  
- (4계층) Application Layer  

## VPN  
(Virtual Private Network) 가상사설망이라고 부릅니다.  
`A 컴퓨터(210.*.*.*)`가 `B네트워크(=VNP서버, 168.*.*.*)`에 접속할 때, `A 컴퓨터`는 더 이상 `210.*.*.*`이 아니라 `168.*.*.*`으로 보여지게 됩니다.  
즉, 네트워크 통신 절차를 볼 때 .. `A -> 외부 -> B -> 외부` 순으로 데이터를 요청합니다.  

### VPN 방식  
* VPN 장비  
* SW 사용  

### 장점  
* 외부 통신 장비가 VPN 사설망에 접속하기 때문에, 내부 네트워크에 포함된 장비처럼 보입니다.  
* 보안을 해치지 않고, 외부 장비가 VPN을 통해 통신을 할 수 있습니다.  

### 단점  
* VPN 서버와 암호화 통신을 해야하기 떄문에 매우 느립니다.  


# 기업

## Akamai  
CDN 관련하여 업계 1위 기업. CDN 개념을 창시한 사람이 설립한 회사

## LimeLight

## EdgeCast


# 참고 사이트

## netmanias  
네트워크 IT 기술 관련 지식 교류 사이트  