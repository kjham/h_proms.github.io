---
layout: post
title: 'VPC 구성 (기본편)'
author: kjham.ham
date: 2020-02-01 18:04
create-date: 2020-02-01 16:04
tags: [swtech,cloud,aws,vpc]
##image: /files/covers/effectivejava.jpg
comments: true
---

**`AWS Networking Cookbook`**의 2장(`사용자 맞춤형 VPC 구성`)을 읽고 VPC 설정을 위한 기본 구성요소를 살펴봅니다.  

다루는 주제들은 다음과 같습니다.  
> 1. VPC의 관리  
> 2. Public Subnet, Privacy Subnet 관리 요령  
> 3. NACL 관리 방법  
> 4. SG(보안 그룹) 관리 방법  
> 5. IGW(인터넷 게이트웨이) 관리 방법  
> 6. NAT 게이트웨이 관리 방법  
> 7. 라우팅 테이블 관리 방법  
> 8. EC2 인스턴스 관리 방법

## 1. VPC의 관리
AWS VPC는 AWS 클라우드 내에 있는 가상 네트워크로, 기업의 데이터센터 내에 있는 일반적인 네트워크처럼 구성되어 있습니다.

### VPC 조회 및 생성  
1. AWS 계정으로 로그인하면 Default VPC를 제공하고 있습니다.  
AWS Services 에서 `VPC`를 검색하여 선택하면 `VPC Dashboard`로 이동합니다.

2. `Your VPCs`를 클릭하면 Default VPC 항목이 `Yes`인 것을 하나 볼 수 있습니다.
이 VPC는 클라우드 내에서 호스트 서비스에 대한 모든 컴포넌트를 포함하고 있습니다.

3. `Create VPC`를 클릭하여 VPC를 하나 생성해봅니다.  
`IPv4 CIDR block`을 `10.0.0.0/16`를 입력했고, `No Ipv6 CIDR Block`를 선택했습니다.  

4. VPC 목록에 새로 만든 VPC가 추가된 것을 볼 수 있습니다.  
`라우팅 테이블`과 `NACL`도 함께 생성되어 VPC와 연관되어 있음을 알 수 있습니다.  

> **동작 원리**  
CIDR 대역을 통해 VPC에서 생성된 EC2 인스턴스의 내부 IP 주소가 몇 개가 될지를 결정하도록 되어 있습니다.  
기본적으로 같은 VPC 내에 있는 인스턴스들은 내부 IP 주소를 이용해 통신하도록 되어 있습니다.  
만약 Private Network를 통해 다른 네트워크/VPC와 통신하고 싶다면 해당 네트워크의 CIDR 대역은 현재 VPC에서 설정되어 있는 CIDR 대역과 절대로 겹쳐서는 안됩니다.  

## 2. Public Subnet, Privacy Subnet 관리 요령  
AWS의 Subnet은 데이터센터 내에 있는 `VLAN`과 유사합니다. 특정 IP 대역이 다른 VLAN/Subnet과 격리되어 있는 것처럼 말입니다.  
리전 내의 각 AZ는 요청에 맞춰 여러개의 서브넷으로 호스팅할 수 있습니다. 서브넷은 하나의 AZ 내에서만 유효해야 합니다.  
Subnet에는 크게 두 가지가 있습니다.  
`Public Subnet`은 VPC 외부로 엑세스가 가능한 것이고,  
`Private Subnet`은 생성된 인스턴스에 `Public IP 주소`가 할당되지 않습니다.  
모든 `Default Subnet`은 `Public Subnet` 입니다.

### Subnet 조회 및 생성  
1. `VPC Dashboard`에서 `Subnets`를 클릭합니다.  
이미 생성되어 있는 Subnet을 볼 수 있습니다.

2. `Create Subnet`을 클릭하여 `VPC`, `CIDR 대역`, `Availability Zone`을 적절하게 선택하여 생성합니다.  

3. Subnet이 생성될 때 라우팅 테이블과 NACL도 같으 생성됩니다.  
`Route Table` 탭을 보면 `Local VCP` 통신용 엔트리, `IGW`를 통핸 외부 통신용 엔트리가 있습니다.  
`Route Table`에 `IGW` 이 연동되어 있으면 `Public Subnet` 이고, 없다면 `Private Subnet`으로 볼 수 있습니다.

> **추가 정보**  
Subnet의 CIDR 대역은 Subnet 내에서 생성될 수 있는 인스턴스의 개수와 인스턴스 내부 IP 대역을 결정합니다.  
그리고 Subnet의 CIDR 대역은 VPC CIDR 대역 내에 있어야 합니다.  
VCP 내에 두 개의 Subnet이 있다고 할 때, 이들의 CIDR 대역은 겹쳐서는 안됩니다.

## 3. 네트워크 ACL (NACL) 관리 방법  
AWS의 `NACL(Network Access Control List)`은 VPC 수준에서 방화벽 역할을 합니다.  
즉, VPC에 연동되어 있는 하나 이상의 Subnet으로 들어오거나 나가는 요청을 제어합니다.  
`NACL`은 AWS의 트래픽을 제어할 때 `SG(Security Group)`과 함께 사용합니다.  
VPC를 생성했을 때 Default NACL은 모든 IP 주소, Port에 대해 열려 있습니다.  
Subnet이 생성되는 동안에는 Default NACL과 연동되어 있습니다.  
이 후, 사용자가 `NACL`을 생성후 교체해야 합니다.  

### NACL 조회 및 생성  
1. `VPC Dashboard`에서 `Security` 메뉴 하위의 `네트워크 ACLs`를 클릭합니다.  
Default NACL 목록을 볼 수 있고, 방화벽이 모두 개방되어 어떠한 소스, 프로토콜, 포트 번호를 통한 트래픽이 리소스와 연결이 가능하게 되어 있습니다.  

2. `Create Network ACL`를 클릭한 후, `VPC` 목록 중 하나를 선택하여 생성합니다.  

3. 생성된 `NACL`를 클릭하고, `Inbound Rules`, `Outbound Rules`를 입력합니다.  

4. `Subnet Associations` 탭을 클릭하여 Subnet와 연결합니다.  
그리고 `Subnets` 메뉴를 클릭하여 `NACL`이 바뀐 것을 확인합니다.  

> **동작 원리**  
`NACL`은 VPC 내에서 하나 이상의 Subnet에 연동할 수 있습니다.  
`Inbound Rules`, `Outbound Rules`에 여러개의 룰을 생성하여 특정 요청을 허용하거나 막을 수 있습니다.  
각 룰은 번호를 갖는데, 가장 낮은 번호를 지닌 룰부터 점차 높은 번호의 룰들이 순서대로 적용됩니다.  
따라서 가장 낮은 번호를 가진 룰이 모든 트래픽을 허용한다면, 다른 룰들은 아무것도 적용되지 못합니다.  
`NACL`은 `상태 비저장 (Stateless)` 라고 하는데..  
임의의 네트워크 요청이 `Inbound Rule`에서 허용되었을 때 `Outbound Rule`에서 이에 대한 허용이 되어 있지 않으면 응답이 나갈 수 없습니다.

> **추가 정보**  
HTTP 요청처럼 리소스에 대한 요청을 받았을 때, IP에 대한 짧은 시간 동안 유효한 트랜스포트 프로토콜 포트 통신이 자동으로 IP 스택 소프트웨어에서 사전에 정의되 있는 대역으로 할당됩니다.   
그리고 한시적으로 사용 가능한 임시 포트 번호 (Ephemeral Port)를 호툴합니다.  
따라서, 통신이 잘 이뤄질 수 있도록 `NACL`에서 임시 포트 번호를 잘 성정해야 합니다.  

`운영체제`별 `임시 포트 번호`는 다음과 같습니다.  
* Amazon Linux  
32768 ~ 61000  
* Windows Server 2003/Windows XP  
1025 ~ 5000  
* Windows Server 2008  
49152 ~ 65535  
* AWS ELB/AWS NAT  
1024 ~ 65535

## 4. SG(보안 그룹) 관리  

... 정리 중 ...

## 5. IGW(인터넷 게이트웨이) 관리  

... 정리 중 ...

## 6. NAT 게이트웨이 관리  

... 정리 중 ...

## 7. 라우팅 테이블 관리   

... 정리 중 ...

## 8. EC2 인스턴스 관리  

... 정리 중 ...