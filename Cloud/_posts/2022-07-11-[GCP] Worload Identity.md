---
layout: post
title: GCP Workload Identity
description: >
  Workload Identity에 대해 알아보자.
sitemap: true
hide_last_modified: false
---
1. 포스팅
{:toc}

GCP 외부에서 Cloud 리소스에 접근하거나 리소스를 이용하려면은 서비스 계정 키를 사용합니다. 하지만 서비스 계정의 키를 이용한다면 보안상 위험이 커질 수 있습니다. 서비스 계정의 키는 어디서든 서비스 계정을 이용할 수 있게므로 관리에 유의를 둬야 합니다.

# Workload Identity 사용 이유

Identity federation을 이용하면 IAM 역할을 외부 ID에서 부여할 수 있습니다. 이렇게 하면 단기 액세스 토큰을 사용하여 리소스에 직접 액세스할 수 있고 서비스 계정 키와 관련된 유지보수 및 보안 부담이 사라집니다.


# Workload Identity Pool

`Workload Identity Pool`을 사용하여 외부 ID를 구성 및 관리할 수 있습니다. 프로젝트 마다 여러 Wordload ID Pool이 포함될 수 있으며 각 풀은 다양한 리소스에 액세스할 수 있습니다. 이를 통해 동일한 풀에서 관련 ID를 그룹화한 후 리소스에 대한 세분화된 액세스 권한을 부여하여 최소 권한의 원칙을 따를 수 있습니다.

일반적으로 개발, 스테이징, 프로덕션 환경과 같은 Google Cloud 리소스에 액세스해야 하는 Google Cloud 이외의 환경마다 새로운 풀을 만드는 것이 좋습니다.

ex) gcloud 사용 예시
```shell
gcloud iam workload-identity-pools create-cred-config \
    projects/$PROJECT_NUMBER/locations/global/workloadIdentityPools/POOL/providers/$PROVIDER_NAME \
    --service-account=$SERVICE-ACCOUNT \
    --output-file=$OUTPUTFILE \
    --aws
```

# Workload Identity Pool Providers

- AWS
- Azure Active Directory
- 온프레미스 Active Directory
- Okta
- Kubernetes 클러스터

# 속성 매핑

속성 매핑은 외부 토큰의 속성을 google 토큰에 적용합니다. 이를 이용하면 iam이 외부 제공 업체(Workload Identity Pool Providers)의 테콘을 사용하여 google cloud 리소스에 대한 액세스를 승인할 수 있습니다.

속성 매핑은 CEL(Common Expression Language)을 지원하여 외부 사용자 인증 정보를 기반으로 새 커스텀 속성을 작성하거나 기존 속성을 더 쉽게 읽을 수 있도록 합니다. 


# 서비스 계정 가장


# Common Expression Language

[참고 사이트](https://chromium.googlesource.com/external/github.com/google/cel-go/+/refs/tags/v0.9.0/README.md)
{:.note}

CEL은 단순성, 속도, 안정성과 이식성을 위해 설계된 비 튜닝 언어입니다. 


```
resource.name.startsWith("/groups/" + auth.claims.group)
```

위 와 같은 형식을 이용합니다.