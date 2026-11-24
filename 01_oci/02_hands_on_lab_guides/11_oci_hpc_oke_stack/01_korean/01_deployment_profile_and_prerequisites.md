# 1. 배포 프로파일과 사전 요구사항

이 가이드는 Softbank Alloy에서 HPC OKE 구성의 3가지 시나리오에 따른 3개의 프로파일을 사용한 배포 방법을 설명합니다. 

* Profile 1: CPU로만 구성되는 OKE Cluster
* Profile 2: CPU와 GPU를 사용하는 OKE Cluster
* Profile 3: CPU와 RDMA 네트워크를 구성하여 사용하는 GPU Cluster

> [!NOTE]
> Deployment Profile은 OCIE Resource Manager(Terraform)를 통해 OKE 클러스터를 생성할 때, 어떤 종류의 Worker Node와 GPU/HPC 인프라를 배포할지 결정하는 사전 정의된 구성 옵션을 말합니다.

## 1.1 검증된 프로파일

|영역|배포 가이드|
|---|----------|
|Access model|배포 과정에서 OKE API Endpoint와 Kubernetes Load Balancerㄹ르 Public으로 사용하는 경우, 두 엔드포인트 모두 승인된 고객 네트워크에서만 접근할 수 있도록 제한|
|Node pools|기본 시스템 노드 풀(System Pool)에 더해, 표준 GPU 노드 풀을 사용하는 GPU 구성 또는 GPU/RDMA 노드 풀을 포함하는 CPU 구성 중 하나를 선택|
|Storage|공유 스토리지는 FSS를 사용하고, NVMe RAID를 선택적으로 구성함. Lustre 파일 시스템은 명시적인 요구사항이 있을때만 사용|
|선택할 수 있는 서비스|OCIR(Oracle Cloud Infrastructure Registry) Credential provider, OCI HPC OKE 유틸리티, Kueue 작업 스케줄러, MPI Operator 및 모니터링 도구 구성|
|GPU/RDMA 네트워크를 위한 관리되는 애드온|NDF(Node Feature Discovery), NVIDIA GPU Operator 및 NVIDIA Network Operator를 배포 단계에서 구성할 수 있음|
|기본 모델|선택적 서비스가 없는 기본 OKE 클러스터에 대해서만 `최소 Alloy 기반 배포`를 사용|

## 1.2 사전 요구사항

* 선택한 배포 프로파일에 포함된 모든 Compute Shape에 대해 서비스 제공여부와 리소스 가용 용량(Capacity)를 확인해야 합니다. 또한, RDMA 배포 프로파일을 사용하는 경우 대상 가용성 도메인에서 BM.GPU.B200.8 인스턴스를 생성할 수 있는 충분한 Capacity가 있는지 확인해야 합니다.
* 고객이 배포에 사용할 컴파트먼트, 관리자 및 VPN 네트워크의 CIDR 대역, SSH 공개 키(Public Key), 그리고 서로 중복되지 않는 VCN 및 Kubernetes Service CIDR 대역을 확보해야 합니다.
* [2장, URL을 사용하여 Custom Image를 준비하고 가져오기](02_prepare_custom_image.md)에서 설명된 Direct URL 방식으로 해당 릴리스에서 승인된 Bastion, Operator 및 Worker 이미지를 대상 OCI 리전의 `Compute Custom Image`로 가져와야 합니다.
* 배포를 수행하는 사용자가 필요한 IAM 정책(IAM Policies)과 Dynamic Group을 생성하거나 기존에 구성된 정책 및 그룹을 사용할 수 있는 권한이 있는지 확인해야 합니다.
* 선택적 구성요소의 사용 여부, Public DNS 구성, TLS 인증서 및 Grafana 알림 기능에 대한 고객의 결정을 확인해야 합니다.

> [!WARNING]
> Bastion 서버의 SSH 접근이나 Kubernetes API Endpoint에 0.0.0.0/0을 사용하지 마십시오. 고객이 승인한 최소한의 CIDR(IP 주소 대역)만 허용하고, 검증 작업이 완료되면 임시로 허용한 접근 권한을 제거해야 합니다.

## 1.3 배포 단계

1. Worker Image Catalog에서 승인된 `.oci` URL을 복사하고, [2장, 'URL을 사용하여 Custom Image를 준비하고 가져오기'](./02_prepare_custom_image.md)에 설명된 절차에 따라 해당 이미지 파일을 OCI Compute Image로 가져옵니다.
2. [3장, 'Resource Manager 스택을 생성하고 로드'](./03_create_resource_manager_stack_load.md)의 절차에 따라 OCI Resource Manager Stack을 생성하고 필요한 구성 파일을 로드합니다. 이후 [4장' '배포 프로파일 선택'](./04_select_deployment_profile.md)에서 배포 프로파일을 선택하고, [5장' 'Identity와 network'](./08_identity_network.md) 부터 [17장. Stack 동작, 미리보기 기능 및 기본 항목](./20_stack_behavior.md)까지 설명된 설정 항목을 구성합니다.
3. Terraform Plan을 실행하고, 생성 또는 변경될 리소스, CIDR 주소 대역, 선택된 Custom Image OCID, IAM 변경 사항 및 활성화된 구성 요소를 검토합니다.
4. Terraform Apply를 실행하여 리소스를 실제로 배포합니다. 이후 [18장, 'Plan, Apply, 검증'](./21_plan_apply_validation.md)에 정의된 검증 절차를 완료하고, 고객의 보안 강화 정책과 DNS 통제를 적용합니다.

---

다음: [2. URL을 사용하여 Custom Image를 준비하고 가져오기](./02_prepare_custom_image.md)