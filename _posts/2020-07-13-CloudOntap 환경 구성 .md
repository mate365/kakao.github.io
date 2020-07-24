---
layout: post
title: "Cloud Volume Ontap으로 AWS-Azure data migration 하기 (2편 AWS-Azure 환경구성 및 설치)"
author: wooyeoung ahn
date: 2020-07-13 19:23
tags: [AWS, Azure, NetApp, CVO]
---

CVO 라이선스 발급이 늦어져서 어쩔수 없이 Azure-AWS 네트워크 환경구성부터 포스팅하게 되었습니다.

## CVO를 배포하기위한 네트워크 환경 만들기

Azure와 AWS console을 양쪽에 켜두고 작업해야 하기떄문에 복잡할 수 있습니다.

네트워크 구성에 고민하고 싶지 않다고 생각하시는 분은 김세준 차장님이 작성한 AWS-Azure VPN 테라폼으로 자동생성하기 게시물을 참조하여 기본설정으로 진행하시기 바랍니다.

아무래도 스토리지관련 서비스이다보니 네트워크가 빠르고 안정적인것이 필요합니다. 
AWS DX와 같이 전용선 서비스를 사용하여 AWS 와 Azure 사이에 대역폭을 확보한다면 좀 더 강력한 성능을 확인 하실 수 있습니다. 

본 게시글에서는 VPN으로 진행합니다. 

## 환경 정보

![AWS-Azure Arch](/files\blog\CVO\CVO-Architechure.png)

구성할 VPN 
BGP-ASN
AWS : 64512( 기본 ) IP : 172.17.0.0/16
Azure : 65515 ( 기본 ) IP : 10.1.0.0/16
 
서브넷
Azure 
- 10.1.0.0/24 default
- 10.1.1.0/24 gatewaysubnet

AWS
|Name|IPv4| CIDR|라우팅 테이블|
|---|---|---|---|
|wyahn-vpc-private-2c |172.17.10.0/24 |rtb-0e174629b72f9971b | wyahn-private-route|
|wyahn-vpc-private-2b |172.17.8.0/24 |rtb-0e174629b72f9971b | wyahn-private-route|
|wyahn-vpc-private-2a |172.17.9.0/24 |rtb-0e174629b72f9971b | wyahn-private-route|




## AZure

가상네트워크게이트웨이 생성

Vnet은 이미 생성하였다는 가정하에 진행합니다. ( Cloudmanager 생성 단원에서 생성한 Vnet을 활용합니다.)

에저 포탈에 "가상 네트워크 게이트웨이 서비스"를 검색합니다.

![VNG1](/files\blog\CVO\VNG1.png)

가상 네트워크 게이트웨이를 생성합니다.
![VNG2](/files\blog\CVO\VNG2.png)

VPN 설정에서 정책기반과 경로기반이 있습니다. AWS는 경로기반으로 만 설정할 수 있습니다.
한쪽이 정책기반이라고 하더라고 연결이 안되는것은 아니지만 SA가 하나밖에 안되기 때문에
경로기반을 선택합니다.

### AWS Q&A 문서

Q: 현재 터널당 설정할 수 있는 IPsec 보안 연결(SA)의 수는 어떻게 됩니까?

A: AWS VPN 서비스는 라우팅 기반 솔루션입니다. 따라서 라우팅 기반 구성을 사용하는 경우에는 SA 한도가 적용되지 않습니다. 또한, AWS VPN 서비스가 라우팅 기반 솔루션이므로, 정책 기반 솔루션을 사용하는 경우에는 단일 SA로 제한해야 합니다.

![VNG3](/files\blog\CVO\VNG3.png)

### 주의 스크린샷은 BGP 구성이 사용으로 되어 있지만 사용하지 않음으로 변경해야 합니다.
(사유 : AWS-Azure VPN 구성에서는 169.254.X.X 대역 제약사항 이슈로 BGP를 맺을 수 없습니다. 자세한 내용은 AWS와 Azure의 VPN 문서를 참고하세요)

![VNG6](/files\blog\CVO\VNG6.png)

작성중...







