# 4.2 프로파일 2: CPU 및 표준 GPU worker를 사용하는 OKE 구성, RDMA 미사용

표준 OKE 관리형 GPU 노드 풀과 VCN 네트워킹을 사용하는 GPU 가속 워크로드에 이 프로파일을 사용합니다. 이 프로파일에서는 RDMA 클러스터 네트워크 또는 RDMA Worker Pool을 생성하지 않습니다.

## 필수 활성화 항목

* System worker pool
    * 고객의 환경에 맞는 Availability Domain, Worker 노드 수, Shape, OCPU 수 등을 지정합니다.
* Worker Pool: CPU
    * Create CPU pool
* Worker Pool: GPU
    * Create GPU pool
* System, CPU 및 표준 GPU Pool에서 각 역할과 Shape에 맞는 승인된 Custom Image 선택

## 필수 비활성화 항목

* Cluster Components
    * Install NVIDIA Network Operator add-on
* Worker Pool: GPU with RDMA
    * Create GPU with RDMA Pool
* Workers: GPU Memory Cluster
    * Create GPU Memory Cluster Pool
* Preview Features (not for production use)
    * 미리보기 기능이 별도로 승인되지 않은 경우 Install Slurm Operator

> [!NOTE]
> Conditional settings: Kueue, MPI Operator, OCI HPC OKE utils, 모니터링, FSS, Lustre 및 OCIR Credential provider는 워크로드 운영 또는 운영 설계에서 명시적으로 요구하는 경우에만 활성화하며, 그 외에는 비활성화 상태로 유지합니다. 모니터링은 GPU 상태 확인과 워크로드 검증을 위해 권장하며, 운영 요구사항에 따라 설치 여부를 결정합니다.
> GPU Operator로 GPU를 관리할 경우 Install NVIDIA GPU Operator add-on 항목을 활성화 할 수 있습니다. 이 경우, Enable optional Alloy features after base validation도 켜야 합니다.

## Worker Pool과 custom images

|입력 항목|프로파일 설정|설명|
|--------|-----------|---|
|System worker pool|Created|승인된 System pool의 가용성 도메인, 노드 수, Shape, 부트 볼륨, Kubernetes 버전 및 가져온 Custom Image를 구성합니다.|
|Create CPU pool|Enabled|시스템 지원 워크로드와 GPU를 사용하지 않는 애플리케이션 워크로드를 위한 범용 CPU Worker Pool을 생성하고 규모를 설정합니다.|
|Create GPU pool|Enabled|표준 OKE 관리형 GPU Worker pool을 생성합니다. 일반적인 Profile 2 구성에서는 이 GPU 노드 풀만 활성화합니다.|
|GPU availability domain|Required|선택한 표준 GPU Shape에 대해 리소스 가용 용량(Capacity)이 확인된 가용성 도메인을 선택합니다.|
|Number of GPU worker nodes|Required|워크로드와 가용성 설계에 필요한 노드 수를 설정합니다. 기본 프로비저닝 및 단일 노드 검증에는 노드 한 개를 사용할 수 있습니다.|
|GPU Shape|Required|배포 패키지에서 지원하고 대상 리전에서 사용할 수 있는, 고객이 승인한 비 RDMA GPU 노드 풀 Shape를 선택합니다.|
|GPU boot volume과 Kubernetes version|Required|부트 볼륨 크기를 승인된 이미지 및 워크로드의 최소 요구사항에 맞게 설정합니다. 지원되는 노드 풀별 버전이 필요한 경우를 제외하고, 클러스터와 동일한 승인된 Kubernetes 버전을 사용합니다.|
|GPU image type|Custom|GPU Shape, Ubuntu 릴리스, 커널, GPU 드라이버 및 CUDA 릴리스와 일치하는 가져온 Custom Image를 사용합니다. 플랫폼 이미지 또는 사전 빌드 이미지를 사용하지 마십시오.|
|GPU image compartment과 Image|Required|이미지 컴파트먼트와 2장의 이미지 가져오기 후 기록한 정확한 리전별 GPU 이미지 OCID를 선택합니다.|
|Create GPU with RDMA pool|Disabled|선택하지 않은 상태로 둡니다. RDMA 노드 수, RDMA Shape, 클러스터 네트워크 배치 또는 RDMA 이미지를 구성하지 마십시오.|
|Create GPU Memory Cluster pool|Disabled|GPU Memory Cluster는 별도의 구성이며, 이 프로파일에서는 사용하지 않습니다.|

## Cluster components와 GPU/RDMA add-ons

|입력 항목|프로파일 설정|설명|
|--------|-----------|---|
|Enable optional Alloy features after base validation|Conditional|이 항목은 관리형 NFD, 관리형 GPU Operator, 관리형 Network Operator 구성 경로를 제어하는 용도로 사용됩니다. NFD와 GPU Operator를 사용하고자 하는 경우에 활성화합니다.|
|Install Node Feature Discovery add-on|Conditional|관리형 NFD(Node Feature Discovery)를 사용하고자 하는 경우 활성화합니다.|
|Install NVIDIA GPU Operator add-on|Conditional|관리형 GPU Operator를 사용하는 경우 활성화합니다. 이 패키지의 설치 조건에 따라 Enable optional Alloy features after base validation과 Install Node Feature Discovery add-on도 활성화합니다.|
|Install NVIDIA Network Operator add-on|Disabled|비활성화 상태로 유지합니다.|
|Install Image Credential Provider for OCIR|Conditional|워크로드가 인스턴스 주체(Instance Principal)를 사용하여 OCIR에서 비공개 이미지를 가져오는 경우 활성화합니다. 그 외에는 비활성화 상태로 유지합니다.|
|Install OCI HPC OKE Utils|기본적으로 Disabled|RDMA 토폴로지 레이블 지정은 필요하지 않습니다. 이미지 사전 가져오기 도구 또는 호스트 실행 도구와 같은 비 RDMA 유틸리티 기능을 명시적으로 요구하는 경우에만 활성화합니다.|
|Install Kueue|Conditional|표준 GPU 노드 풀에 대한 큐잉 및 토폴로지 인식 스케줄링을 요구하는 경우에만 활성화합니다. 그 외에는 비활성화 상태로 유지합니다.|
|Install MPI Operator|Conditional|MPIJob 워크로드에 대해서만 활성화합니다. Profile 2에서는 표준 네트워킹을 통해 MPI를 실행할 수 있지만, RDMA 가속은 제공하지 않습니다.|
|Install Slurm Operator|Disabled|고객이 별도로 승인된 미리 보기 배포를 사용하는 경우를 제외하고, 미리 보기 기능인 Slinky/Slurm 구성 경로를 비활성화 상태로 유지합니다.|

## Storage, monitoring과 stack behavior

|입력 항목|프로파일 설정|설명|
|--------|-----------|---|
|Create RAID array using local NVMe drives|Conditional|선택한 GPU Shape에서 로컬 NVMe를 사용할 수 있고, 워크로드에 컨테이너, Kubelet 또는 Pod 로그용 고처리량 임시 스토리지가 필요한 경우에만 활성화합니다.|
|Create a File Storage Service (FSS) file system|Conditional|워크로드에 공유 영구 스토리지가(Shared persistent storage) 필요한 경우 활성화합니다. 승인된 가용성 도메인과 마운트 지점을 구성합니다.|
|Create a File Storage with Lustre file system|Conditional|병렬 파일 시스템에 대한 승인된 요구사항이 있고, 선택한 Custom Image에 Lustre 클라이언트가 포함된 경우에만 활성화합니다.|
|Install Monitoring Stack|Recommended|GPU 노드 상태 확인, Prometheus 메트릭, NVIDIA DCGM 메트릭 및 워크로드 검증을 위해 사용을 권장합니다. GPU 노드 풀을 생성하기 위한 사전 요구사항은 아닙니다.|
|Install Node Problem Detector & Kube Prometheus Stack|Recommended|모니터링 Stack을 사용하는 경우, 노드 상태와 클러스터 메트릭을 수집할 수 있도록 활성화합니다.|
|OCI Metrics Exporter, TLS, and Grafana alerting|Conditional|고객이 OCI 메트릭 수집, DNS 및 인증서 구성, 알림 대상을 승인한 후에만 활성화합니다.|
|Securely deploy the Kubernetes resources via ORM Private Endpoint|Disabled|이 가이드에서는 Public OKE Endpoint를 사용합니다. 별도로 검증된 Private Control Plane 배포에 대해서만 활성화합니다.|
|Minimal Alloy base deployment|Disabled|표준 GPU 프로파일에서는 서비스를 활성화할 수 있는 모드를 사용합니다. 특히 모니터링, Kueue, MPI 또는 기타 선택한 정상 동작 서비스가 활성화된 경우 이 모드를 사용합니다.|

> [!IMPORTANT]
> Profile 2 Plan 확인: Terraform Plan에는 System, CPU 및 표준 GPU Worker Pool이 포함되어야 합니다. GPU with RDMA 노드 풀, 클러스터 네트워크 또는 Compute Cluster의 RDMA 리소스, GPU Memory Cluster 노드 풀이 포함되어서는 안 됩니다. Install Slurm Operator도 미리 보기 기능 사용이 별도로 승인된 경우를 제외하고 비활성화 상태로 유지해야 합니다.

## Terraform Apply 후 검증:

* System, CPU 및 표준 GPU 노드의 상태가 Ready이고, 승인된 이미지 OCID를 사용하는지 확인합니다.
* NVIDIA 드라이버와 CUDA 소프트웨어 스택이 정상적으로 로드되고, Kubernetes에 예상한 할당 가능 GPU 리소스가 표시되며, 승인된 GPU 워크로드가 완료되는지 확인합니다. RDMA 인터페이스와 RDMA 토폴로지 레이블은 존재하지 않아야 하며, 다중 노드 RDMA 검증은 수행하지 않습니다.

---

[이전: 4.1 프로파일 1: CPU만 사용하는 OKE worker](./05_profile_1.md) | [다음: 4.3 프로파일 3:CPU 및 RDMA 지원 GPU Worker를 사용하는 OKE 구성](./07_profile_3.md)