---
layout: post
title: GCP Shared VPC
description: >
  Shared VPC
sitemap: true
hide_last_modified: false
---
1. 포스팅
{:toc}

# VPC 란?

클라우드는 `Public Cloud`에 존재하고 있습니다. 단어 그래도 직역을 하면 `공공 클라우드`죠 누구나 접근이 가능하고 누구나 사용이 가능한 단어로 나와 있습니다. 당연히 클라우드 서비스를 이용하려면 `Public Cloud`여야 하지만 서비스를 관리하거나 운용하는 입장에서는 모든 리소스들이 Public한다면 문제가 생깁니다.

그래서 Cloud에도 가상 네트워크를 생성하는데 그게 바로 VPC `Virtual Private Cloue`입니다.

GCP의 `VPC`는 Andromeda를 이용하여 Google의 프로덕션 네트워크 내에서 구현되는 물리적 네트워크의 가상 버전입니다.

VPC를 이용하여 관리에 맞게 네트워크 망을 분리하고 합치고 관리자가 구성하는데로 이뤄지게 됩니다.
프로젝트가 생성되면 프로젝트에는 기본적으로 각 리전에 하나의 서브넷을 가지고 있는 기본 네트워크로 시작 됩니다.

## 특징

- 전역 리소스
- Shared VPC 이용 가능
- VPC Network Peering을 사용하여 다른 프로젝트 또는 조직의 VPC 네트워크에 연결 가능
- 새 프로젝트 생성시 기본 VPC 네트워크가 같이 생성되지만 조직 정채 제약조건을 만들면 기본 네트워크 생성을 중지 할 수 있습니다. 


# Shared VPC

공유 VPC를 사용하는 조직은 여러 프로젝트의 리소스를 공통 Virtual Private Cloud(VPC) 네트워크에 연결할 수 있으므로 해당 네트워크의 내부 IP를 사용하여 서로 안전하고 효율적으로 통신할 수 있습니다.

공유 VPC를 사용할 때는 프로젝트 하나를 호스트 프로젝트로 지정하고 여기에 하나 이상의 다른 서비스 프로젝트를 연결합니다. 호스트 프로젝트의 VPC 네트워크를 공유 VPC 네트워크라고 합니다. 서비스 프로젝트의 운영 가능 리소스는 공유 VPC 네트워크의 서브넷을 사용할 수 있습니다.

## 개념

Shared VPC는 동일한 조직내에 있는 프로젝트는 모두 연결이 가능합니다.(폴더가 다르면 관리자에게 두 폴더에 대항 [공유VPC관리자 권한](https://cloud.google.com/vpc/docs/shared-vpc#iam_in_shared_vpc)이 있어야합니다.)

> 공유 VPC 관리자(compute.xpnAdmin 및resourcemanager.projectIamAdmin)
> <br/>• 조직의 IAM 주 구성원 또는
> <br/>• 폴더의 IAM 주 구성원(베타)

참여하는 호스트 및 서비스 프로젝트는 다른 조직에 속할 수 없습니다. 유일한 예외는 프로젝트를 한 조직에서 다른 조직으로 마이그레이션하는 동안입니다.


### 구조

Shared VPC에 포함되는 프로젝트는 `Host Project`와 `Service Project`입니다.

Host Project에 하나 이상의 공유 VPC 네트워크를 생성해야하며 관리자가 해당 프로젝트를 호스트 프로젝트로 설정해야 합니다. 그 다음으로 Service Project를 연결하여 Shared VPC를 구성합니다.

### 네트워크

> 공유 VPC 네트워크는 호스트 프로젝트에 정의된 VPC 네트워크이며 서비스 프로젝트에서 실행되는 리소스의 중앙 공유 네트워크로 사용할 수 있습니다. 

- 모든 호스트 프로젝트 서브넷을 공유할 수 있습니다. 이 옵션을 선택하면 새 네트워크의 서브넷을 포함하여 호스트 프로젝트에서 만든 모든 새 서브넷도 공유됩니다.
- 공유할 개별 서브넷을 지정할 수 있습니다. 개별적으로 서브넷을 공유하는 경우 수동으로 목록을 변경하지 않는 한 해당 서브넷만 공유됩니다.

# Reference
- [GCP shread-vpc](https://cloud.google.com/vpc/docs/shared-vpc)
- [GCP vpc](https://cloud.google.com/vpc/docs/vpc)