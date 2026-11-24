# 4.3 CPU 및 RDMA 지원 GPU Worker를 사용하는 OKE 구성

RDMA 연결이 필요한 분산 GPU 워크로드에 이 프로파일을 사용합니다. 검증된 Alloy 구성 예시에서는 BM.GPU.B200.8 Worker와 해당 릴리스에서 승인된 Ubuntu Custom Image를 사용합니다.

## 필수 활성화 항목

* System worker pool
    * 고객의 환경에 맞는 Availability Domain, Worker 노드 수, Shape, OCPU 수 등을 지정합니다.
* Worker Pool: CPU
    * Create CPU pool
* Worker Pool: GPU with RDMA
    * Create GPU with RDMA pool
* B200 사용자 지정 이미지 선택 검증
* 로컬 NVMe RAID 및 FSS 설정 검증

## 필수 비활성화 항목

* Worker Pool: GPU
    * Create GPU pool: RDMA GPU 풀 외에 일반 GPU 풀도 추가하도록 명시적으로 승인된 경우가 아니라면, Create GPU pool은 선택하지 마십시오.
* Workers: GPU Memory Cluster
    * Create GPU Memory Cluster pool
* Preview Features (not for production use)
    * 기능을 미리 사용해보는 것이 별도로 승인되지 않은 경우 Install Slurm Operator

> [!NOTE]
> 검증된 RDMA 프로파일에서는 OCI HPC OKE Utils와 모니터링 사용을 권장합니다. Kueue, MPI Operator 및 OCIR Credential Provider는 승인된 워크로드와 운영 설계에서 필요한 경우에만 활성화합니다.

## Worker Pool과 custom images

|입력 항목|프로파일 설정|설명|
|--------|-----------|---|
|System worker pool|Created|승인된 System Pool의 가용성 도메인, 규모, Shape, 부트 볼륨, Kubernetes 버전 및 가져온 Custom Image를 구성합니다.|
|Create CPU pool|Enabled|컨트롤러, 서비스 및 GPU를 사용하지 않는 애플리케이션 구성요소를 위한 CPU Worker Pool을 생성합니다.|
|Create GPU pool|Disabled|표준 GPU 노드 풀은 선택하지 않은 상태로 둡니다. 고객에게 별도로 승인된 비 RDMA GPU 요구사항이 있고, 두 가지 GPU 노드 풀 유형을 모두 배포할 수 있는 리소스 가용 용량(Capacity)이 확보된 경우에만 활성화합니다.|
|Create GPU with RDMA pool|Enabled|검증된 Alloy 클러스터 네트워크 배포 방식을 사용하여 RDMA 지원 GPU Worker Pool을 생성합니다.|
|RDMA availability domain|Required|검증된 Alloy 가용성 도메인을 사용하고, Terraform Plan 및 Terraform Apply를 실행하기 전에 BM.GPU.B200.8의 리소스 가용 용량(Capacity)을 확인합니다.|
|Number of RDMA worker nodes|Required|노드 한 개는 프로비저닝 및 단일 노드 기본 동작 확인에만 사용합니다. 다중 노드 RDMA, NCCL 또는 MPI 검증에는 두 개 이상의 노드를 사용합니다.|
|RDMA shape|BM.GPU.B200.8|다른 Shape가 별도로 검증되고 승인된 경우를 제외하고, 검증된 프로파일에는 BM.GPU.B200.8을 사용합니다.|
|RDMA boot volume size|1024 GB|검증된 값인 1024 GB를 사용하거나, 가져온 이미지와 워크로드의 요구사항을 충족하는 고객 승인 값으로 더 크게 설정합니다.|
|RDMA Kubernetes version|Cluster-approved|배포 패키지에서 다른 Worker Pool 버전을 명시적으로 허용하는 경우를 제외하고, OKE 클러스터와 동일한 지원 Kubernetes 버전을 사용합니다.|
|RDMA image type|Custom|검증된 커널, NVIDIA 드라이버, CUDA 및 RDMA 소프트웨어 스택이 포함된, 해당 릴리스에서 승인된 Ubuntu Custom Image를 사용합니다. 관리형 Operator를 설치하는 경우에는 이미지에 포함된 구성요소와 Operator의 관리 범위를 확인하여 중복 설치나 충돌을 방지합니다.|
|Import custom image from URI|Disabled|B200/RDMA 이미지는 Section 2에서 수동으로 가져왔습니다. Terraform Apply 중에 이미지를 다시 가져오지 말고 기존 이미지를 선택합니다.|
|RDMA image compartment and Image|Required|고객 이미지 컴파트먼트와 이미지 가져오기 후 기록한 정확한 리전별 B200/RDMA 이미지 OCID를 선택합니다.|
|Create GPU Memory Cluster pool|Disabled|GPU Memory Cluster는 이 B200 RDMA 프로파일에 포함되지 않으며, 별도의 설계와 테넌시 활성화가 필요합니다.|

## Cluster components와 GPU/RDMA add-ons

|입력 항목|프로파일 설정|설명|
|--------|-----------|---|
|Enable optional Alloy features after base validation|Conditional|기본 클러스터와 워커 노드의 검증을 완료한 후, 이 항목의 활성화가 필요한 관리형 NFD, GPU Operator, Network Operator를 설치하는 경우 활성화합니다. 개별 설치 항목도 별도로 선택해야 합니다.|
|Install Node Feature Discovery add-on|Conditional|관리형 NFD를 사용하는 경우 활성화합니다. 이 패키지의 관리형 GPU Operator 또는 Network Operator를 설치할 때는 NFD 설치 조건도 충족해야 합니다.|
|Install NVIDIA GPU Operator add-on|Conditional|관리형 GPU Operator를 사용하는 경우 활성화하고, 상위 기능 허용 항목 및 NFD 설치 조건을 함께 충족합니다.|
|Install NVIDIA Network Operator add-on|Conditional|RDMA 네트워크 구성요소를 관리형 Network Operator로 관리하는 경우 활성화하고, 상위 기능 허용 항목 및 NFD 설치 조건을 함께 충족합니다.|
|Install Image Credential Provider for OCIR|Conditional|RDMA 워크로드가 인스턴스 주체(Instance Principal)를 사용하여 OCIR에서 비공개 이미지를 가져오는 경우 활성화합니다. IAM 정책을 Stack 외부에서 관리하는 경우, 필요한 저장소 읽기 정책을 별도로 구성해야 합니다.|
|Install OCI HPC OKE Utils|Recommended|검증된 RDMA 프로파일에서 RDMA 토폴로지 레이블러, 이미지 사전 가져오기 도구, 호스트 실행 도구, 펌웨어 및 버전 레이블 지정 또는 유지보수 이벤트 레이블 지정이 필요한 경우 활성화합니다.|
|Install Kueue|Conditional|고객이 활성화된 RDMA GPU 노드 풀에 Kueue 큐와 토폴로지 인식 스케줄링을 사용하는 경우에만 활성화합니다.|
|Install MPI Operator|Conditional|MPIJob 워크로드를 사용하는 경우 활성화합니다. NCCL 전용 워크로드 또는 기본 노드 프로비저닝에는 필요하지 않습니다.|
|Install Slurm Operator|Disabled|Slinky/Slurm은 미리 보기 기능입니다. 별도로 승인된 미리 보기 배포에 대해서만 활성화하고, 이후 RDMA 전용 Worker 설정을 구성합니다.|

> [!NOTE]
> RDMA Worker Pool 생성과 Network Operator 설치는 별도의 선택입니다. Network Operator를 설치하지 않는 경우에는 승인된 이미지와 별도 구성에서 필요한 RDMA 네트워크 구성요소를 어떻게 제공하는지 확인해야 합니다.

## Storage, monitoring과 stack behavior

|입력 항목|프로파일 설정|설명|
|--------|-----------|---|
|Create a RAID array using local NVMe drives|검증된 프로파일에서는 Enabled|BM.GPU.B200.8에 로컬 NVMe가 있는 경우 활성화합니다. 검증된 구성 예시에서는 RAID 10과 바인드 마운트를 사용하여 컨테이너 런타임, Kubelet 임시 스토리지 및 Pod 로그를 구성합니다.|
|RAID level|10|검증된 프로파일에서는 RAID 10을 사용합니다. 고객이 중복성 상실을 명시적으로 수용한 경우에만 RAID 0을 사용합니다.|
|Create a File Storage Service (FSS) file system|검증된 프로파일에서는 Enabled|공유 영구 스토리지를 생성하고, 검증된 Alloy 가용성 도메인을 선택한 후 /mnt/oci-fss와 같은 승인된 마운트 경로를 사용합니다.|
|Create a File Storage with Lustre file system|기본적으로 Disabled|기본적으로 비활성화합니다. 병렬 파일 시스템이 명시적으로 필요한 경우에만 활성화합니다. 선택한 워커 이미지에 호환되는 Lustre 클라이언트가 포함되어 있는지 확인하고, 승인된 가용성 도메인, 용량, 성능 등급 및 Lustre 마운트 경로를 구성합니다.|
|Install Monitoring Stack|Recommended|Prometheus, NVIDIA DCGM 메트릭, 노드 문제 감지, Grafana 및 GPU/RDMA 상태 검증을 위해 활성화합니다.|
|Monitoring advanced options|필요시 선택|개별 모니터링 구성요소와 Exporter 설정을 확인하거나 변경할 때 선택합니다. 이 항목 자체가 모니터링 설치 여부를 결정하지는 않습니다.|
|Install Node Problem Detector & Kube Prometheus Stack|Recommended|노드 상태, 클러스터 메트릭, Prometheus 수집 대상 및 자동 상태 점검을 위해 활성화합니다.|
|Install AMD Device Metrics Exporter|Disabled|BM.GPU.B200.8은 NVIDIA GPU를 사용합니다. AMD Exporter는 비활성화 상태로 유지합니다.|
|Grafana and dashboards|Conditional / recommended|고객이 대화형 GPU/RDMA 대시보드를 요구하는 경우 활성화합니다. 외부에 노출하기 전에 엔드포인트 접근 설정과 DNS 구성을 확인합니다.|
|OCI Metrics Exporter, TLS, and Grafana alerting|Conditional|고객이 OCI 메트릭 수집, DNS 및 인증서, 알림 토픽 구성을 승인한 후에만 활성화합니다.|
|Securely deploy the Kubernetes resources via ORM Private Endpoint|Disabled|이 가이드에서는 배포 과정에서 Public OKE Endpoint를 사용합니다. 별도로 검증된 Private Control Plane 구성에 대해서만 활성화합니다.|
|Minimal Alloy base deployment|Disabled|검증된 RDMA 프로파일에서는 서비스를 활성화할 수 있는 모드를 사용하고, 위에서 명시적으로 승인된 워크로드 서비스만 활성화합니다.|

> [!IMPORTANT]
> 프로파일 3 Plan 확인: Terraform Plan에는 System, CPU 및 GPU with RDMA Worker Pool, 승인된 B200 Shape와 Custom Image, 승인된 스토리지 및 선택한 구성요소가 포함되어야 합니다. 표준 GPU 풀은 두 번째 비 RDMA GPU 풀이 명시적으로 승인된 경우에만 포함합니다. GPU Memory Cluster는 이 프로파일에 포함하지 않습니다. Slurm Operator는 별도로 승인된 미리보기 배포가 아닌 경우 비활성화 상태로 유지합니다.

## Terraform Apply 후 검증:
* System, CPU 및 B200/RDMA 노드의 상태가 Ready이고, 승인된 해당 리전의 Custom Image OCID를 사용하는지 확인합니다.
* 모든 GPU, 승인된 NVIDIA 드라이버 및 CUDA 버전, RDMA 인터페이스와 예상한 토폴로지 레이블을 확인합니다.
* 승인된 다중 노드 RDMA, NCCL 또는 MPI 테스트에는 두 개 이상의 RDMA 노드를 사용합니다. 노드 한 개로는 프로비저닝과 단일 노드 기본 동작을 검증할 수 있지만, 노드 간 RDMA 통신은 검증할 수 없습니다.
* 활성화한 항목에 대해 FSS 마운트, 로컬 RAID 생성, 선택한 Helm 릴리스, Prometheus 수집 대상 및 Grafana 대시보드를 확인합니다.

---

[이전: 4.2 CPU 및 표준 GPU worker를 사용하는 OKE 구성, RDMA 미사용](./06_profile_2.md) | [다음: 5. Identity와 network](./08_identity_network.md)