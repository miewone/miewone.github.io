---
layout: post
title: GCP VPC SC 안전한 데이터 교환
description: >
  AWS에서 IAM 역할을 가장하기 위한 방법
sitemap: true
hide_last_modified: false
---
1. 포스팅
{:toc}

# 타 조직 서비스를 이용하기위한 VPC SC 조정

![vpcsc](https://cloud.google.com/static/vpc-service-controls/images/directional_policy_egress_to_resource_outside_perimeter.png)

```yaml
name: accessPolicies/222/servicePerimeters/Example 
status:
  resources: # 관리하는 프로젝트 Numbers
  - projects/111
  restrictedServices: # 제산하는 리소스들
  - bigquery.googleapis.com # bigduqry
  - containerregistry.googleapis.com # GCR
  - storage.googleapis.com # GCS
title: Example
```

다른 조직에 있는 Project 999 의 GCS 버킷에 대해 읽기 액세스 권한을 부여해야합니다.

```yaml
#echo """
- egressTo:
    operations:
      - serviceName: storage.googleapis.com 
      # 타 조직에서 사용할 서비스 , restrictedServices에 포함된 서비스여야합니다.
        methodSelectors:
        - method: google.storage.objects.get # 이용할 서비스의 메서드
    resources:
    - projects/999 # 타 조직 설정
  egressFrom:
    identityType: ANY_IDENTITY
#""" > gcs.yaml
```
[ACM json 형식](https://cloud.google.com/access-context-manager/docs/reference/rest/v1alpha/accessPolicies.servicePerimeters)

방화벽의 egress와 같이 저희 조직에서 타 조직으로 나가는 리소스의 기능과 범위를 컨트롤할 수 있습니다. 

```shell
$ gcloud access-context-manager perimeters update Example --set-egress-policies=gcs.yaml
```

# 경계 외부의 VPC 네트워크에서 비공개 액세스를 허용하여 BigQuery 데이터 세트 읽기

![bqread](https://cloud.google.com/static/vpc-service-controls/images/directional_policy_bigquery_ingress.png)

```yaml
name: accessPolicies/222/servicePerimeters/Example
status:
  resources:
  - projects/111
  restrictedServices:
  - bigquery.googleapis.com
  - containerregistry.googleapis.com
title: Example
```

목표로는 VPC Shared나 다양한 파트너의 경계 외부에 있는 VPC 네트워크에서 읽기 액세서를 허용하는 것입니다.

```yaml
#echo """
- ingressFrom:
    identityType: ANY_IDENTITY
    sources:
    - resource: projects/888
    - resource: projects/999
  ingressTo:
    operations:
    - serviceName: bigquery.googleapis.com
      methodSelectors:
      - permission: bigquery.datasets.get
      - permission: bigquery.tables.list
      - permission: bigquery.tables.get
      - permission: bigquery.tables.getData
      - permission: bigquery.jobs.create
    resources:
    - \"*\"

#""" > partneringress.yaml
```
888,999 라는 프로젝트 넘버를 가진 곳에서 bigquery dataset get, tables list get get data, jobs create 메소드를 우리 프로젝트의 모든 빅쿼리 리소스에서 이용할 수 있습니다.

```shell
gcloud access-context-manager perimeters update Example --set-ingress-policies=partneringress.yaml
```

상기 내용들의 방식으로 VPC Sc ingress, egress 규칙을 이용하면 안전하게 데이터를 교환할 수 있습니다.
