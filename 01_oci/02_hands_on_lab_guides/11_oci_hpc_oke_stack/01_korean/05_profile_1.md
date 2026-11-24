# 4.1 프로파일 1: CPU만 사용하는 OKE worker

GPU 리소스, RDMA 네트워킹, GPU 스케줄링 또는 MPI 워크로드 오퍼레이터가 필요하지 않은 최소 OKE 클러스터 및 CPU 워크로드에 이 프로파일을 사용합니다.

## 필수 활성화 항목

* System worker pool
    * 고객의 환경에 맞는 Availability Domain, Worker 노드 수, Shape, OCPU 수 등을 지정합니다.
* Worker Pool: CPU
    * Create CPU pool
* System과 CPU Pool에서 Custom image 선택
* Minimal Alloy base deployment (엄격한 최소 구성에서는 활성화 할 수 있습니다.)

## 필수 비활성화 항목

* Cluster Components
    * Install Node Feature Discovery add-on
    * Install NVIDIA GPU Operator add-on
    * Install NVIDIA Network Operator add-on
    * Install OCI HPC OKE Utils
    * Install Kueue
    * Install MPI Operator
* Worker Pool: GPU
    * Create GPU Pool
* Worker Pool: GPU with RDMA
    * Create GPU with RDMA Pool
* Workers: GPU Memory Cluster
    * Create GPU Memory Cluster Pool

> [!NOTE]
> 조건부 설정: OCIR Credential Providers, FSS, 모니터링, Grafana 및 OCI 매트릭은 선택사항입니다. 엄격한 최소 구성 프로파일에서는 해당 항목을 비활성화 상태로 유지합니다. 지원되는 서비스를 하나라도 활성화하는 경우, Minimal Alloy base deployment를 Disable로 설정하고 개별적으로 승인된 서비스 입력 항목만 활성화해야 합니다.

## Worker pool과 Custom Image

|입력 항목|프로파일 설정|설명|
|--------|-----------|---|
|System Worker Pool|Created|승인된 가용성 도메인, 노드 수, CPU 셰이프, 부트 볼륨, Kubernetes 버전 및 가져온 Custom Image를 구성합니다. 이 풀은 클러스터 서비스와 운영 워크로드를 실행합니다.|
|Create CPU pool|Enabled|범용 CPU Worker Pool을 생성하고 가용성 도메인, 노드 수, 셰이프, OCPU 수, 메모리, 부트 볼륨 및 Kubernetes를 구성합니다.|
|Create GPU pool|Disabled|선택하지 않은 상태로 둡니다. GPU 셰이프, GPU 노드 수, GPU 이미지 또는 기타 표준 GPU 노드 풀 입력 항목을 입력하지 마십시오.|
|Create GPU with RDMA pool|Disabled|선택하지 않은 상태로 둡니다. RDMA Worker Pool, 클러스터 네트워크 배치, RDMA 셰이프 또는 RDMA 이미지는 생성되지 않습니다.|
|Create GPU Memory Cluster pool|Disabled|별도의 자체 관리형 GPU Memory Cluster 구성이며, CPU 전용 프로파일에 포함되지 않습니다.|
|Image type|Custom|System 및 CPU Worker Pool에 Custom을 사용합니다. OCI 플랫폼 이미지 또는 사전 빌드 이미지를 사용하지 마십시오.|
|Import custom image from URI|Disabled|승인된 이미지는 2장에서 수동으로 가져왔습니다. Terraform Apply 중에 이미지를 다시 가져오지 말고, 해당 리전에 있는 기존 Custom Image를 선택합니다.|
|Image compartment and image|Required|System 및 CPU 노드 풀에 대해 고객 이미지 컴파트먼트와 Section 2에서 기록한 정확한 리전별 이미지 OCID를 선택합니다.|

## Cluster components와 GPU/RDMA 애드온

|입력 항목|프로파일 설정|설명|
|--------|-----------|---|
|Enable optional Alloy features after base validation|Disabled|false로 유지합니다. CPU 전용 프로파일에서는 관리형 NFD, NVIDIA GPU Operator 및 NVIDIA Network Operator를 설치하지 않으므로 false로 유지합니다.|
|Install Node Feature Discovery add-on|Disabled|CPU 전용 Worker에는 필요하지 않으며, 검증된 Alloy 프로파일에서도 활성화하지 않습니다.|
|Install NVIDIA GPU Operator add-on|Disabled|GPU 노드 풀이 없으며, 관리형 GPU Operator 구성 경로는 비활성화 상태로 유지되는 관리형 NFD 구성 경로에 종속됩니다.|
|Install NVIDIA Network Operator add-on|Disabled|RDMA·SR-IOV·Multus 등 고성능 네트워크 구성요소를 관리하지 않으므로, 활성화하지 않습니다.|
|Install Image Credential Provider for OCIR|Conditional|Worker 노드가 인스턴스 주체(Instance Principal)를 사용하여 OCIR에서 비공개 이미지를 가져와야 하는 경우에만 활성화합니다. Create Policies가 비활성화된 경우, 필요한 저장소 읽기 정책을 별도로 구성해야 합니다.|
|Install OCI HPC OKE Utils|Disabled|엄격한 CPU 전용 프로파일에서는 RDMA 토폴로지 레이블러, 이미지 사전 가져오기 도구, 호스트 실행 도구 또는 관련 HPC 유틸리티를 설치하지 마십시오.|
|Install Kueue|Disabled|워크로드 큐, ResourceFlavors 또는 토폴로지 인식 스케줄링 리소스를 생성하지 마십시오. 엄격한 CPU 전용 프로파일에서는 Kubernetes 기본 스케줄링을 사용합니다.|
|Install MPI Operator|Disabled|이 프로파일에서는 MPIJob 워크로드를 배포하지 않으므로 MPI Operator를 설치하지 마십시오.|
|Install Slurm Operator|Disabled|Slurm/Slinky는 미리 보기 기능이며, CPU 전용 고객 프로파일에 포함되지 않습니다.|

## Storage, Monitoring과 Stack Behavior

|입력 항목|프로파일 설정|설명|
|--------|-----------|---|
|Create RAID array using local NVMe drives|기본적으로 Disabled|선택한 CPU 셰이프에 로컬 NVMe가 있고, 고객이 임시 컨테이너 스토리지, Kubelet 또는 Pod 로그의 성능 향상을 명시적으로 요구하는 경우에만 활성화합니다. 그 외에는 비활성화 상태로 유지합니다.|
|Create a File Storage Service (FSS) file system|Conditional|CPU 워크로드에 공유 영구 스토리지가 필요한 경우에만 활성화합니다. 승인된 가용성 도메인과 마운트 지점을 구성합니다.|
|Create a File Storage with Lustre file system|기본적으로 Disabled|일반적인 CPU 워크로드에서는 비활성화 상태로 유지합니다. 승인된 CPU HPC 구성이고 선택한 Custom Image에 Lustre 클라이언트가 포함된 경우에만 활성화합니다.|
|Install Monitoring Stack|Optional|엄격한 최소 구성 프로파일에서는 비활성화 상태로 유지합니다. Prometheus, 노드 상태 모니터링 또는 Grafana가 명시적인 운영 요구사항인 경우 활성화합니다.|
|Advanced options / Install AMD Device Metrics Exporter|Conditional|모니터링을 활성화한 경우, Advanced options에서 표시되는 모니터링 입력 항목을 구성합니다. CPU 전용 프로파일에서는 Install AMD Device Metrics Exporter를 비활성화 상태로 유지합니다. Grafana와 Grafana 대시보드는 필요한 경우에만 활성화합니다.|
|Grafana, dashboards, OCI Metrics, TLS, and alerting|Conditional|고객이 DNS, 인증서, 대시보드, OCI 메트릭 수집 및 알림 대상을 승인한 경우에만 활성화합니다.|
|Securely deploy the Kubernetes resources via ORM Private Endpoint|Disabled|이 가이드에서는 배포 과정에서 Public OKE Endpoint를 사용합니다. 별도로 검증된 Private Control Plane 구성에 대해서만 활성화합니다.|
|Minimal Alloy base deployment|엄격한 최소 구성에서는 활성화합니다. 선택적 서비스를 사용하는 경우에는 아래 조건부 설정에 따라 비활성화합니다.|모든 선택적 서비스가 비활성화 상태인 경우에만 활성화합니다. 모니터링, Kueue, MPI, OCI HPC OKE Utils 또는 기타 지원되는 서비스를 선택한 경우, 기본 모드를 비활성화하고 승인된 각 서비스를 개별적으로 제어합니다.|

> [!IMPORTANT]
> Terraform Plan에는 System 및 CPU Worker Pool만 포함되어야 합니다. 표준 GPU 노드 풀, GPU with RDMA 노드 풀, GPU Memory Cluster 노드 풀, OCI HPC OKE Utils, Kueue, MPI Operator 또는 Slurm 리소스가 포함되어서는 안됩니다. Install Node Feature Discovery, NVIDIA GPU Operator, NVIDIA Network Operator는 비활성화 상태로 유지되어야 합니다.

## Terraform Apply 후 검증:

* System 및 CPU 노드의 상태가 Ready이고, 기록된 Custom Image OCID를 사용하는지 확인합니다.
* Kubernetes에 GPU 리소스가 표시되지 않는지 확인합니다. 이 배포에서 GPU/RDMA용 인터페이스와 RDMA 전용 토폴로지 레이블이 구성되지 않았는지 확인합니다.
* 승인된 CPU 워크로드를 실행하고, 스토리지 및 모니터링 옵션을 활성화한 경우에만 해당 기능이 요구사항을 충족하는지 확인합니다.

---

[이전: 4. 배포 프로파일 선택](./04_select_deployment_profile.md) | [다음: 프로파일 2: CPU 및 표준 GPU worker를 사용하는 OKE 구성, RDMA 미사용](./06_profile_2.md)
