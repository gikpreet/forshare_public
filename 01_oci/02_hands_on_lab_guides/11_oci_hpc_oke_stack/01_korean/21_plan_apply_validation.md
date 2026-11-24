# 18. Plan, apply, 검증

## 18.1 Terraform Plan 검토

* 리소스별 생성 위치가 승인된 설계와 일치하는지 확인합니다. 컴파트먼트, 리전 및 가용성 도메인이 적용되는 리소스는 각 대상 위치를 확인하고, IAM 정책과 기존 네트워크·이미지 참조도 별도로 검토합니다.
* 선택한 프로파일이 Section 4의 구성과 정확히 일치하는지 확인합니다. Profile 1에는 System 및 CPU 노드 풀만 포함됩니다. Profile 2에는 표준 GPU 노드 풀만 추가됩니다. Profile 3에는 두 번째 비 RDMA GPU 노드 풀이 명시적으로 승인된 경우를 제외하고 GPU with RDMA 노드 풀만 추가됩니다.
* 프로파일 1: Create GPU pool, Create GPU with RDMA pool 및 Create GPU Memory Cluster pool이 비활성화되어 있는지 확인합니다. Enable optional Alloy features after base validation, Install Node Feature Discovery add-on, Install NVIDIA GPU Operator add-on 및 Install NVIDIA Network Operator add-on도 비활성화합니다. Install OCI HPC OKE Utils, Install Kueue, Install MPI Operator 및 Install Slurm Operator가 비활성화되어 있는지 확인합니다.
* 프로파일 2: Create GPU with RDMA pool, Create GPU Memory Cluster pool 및 Install NVIDIA Network Operator add-on이 비활성화되어 있는지 확인합니다. 관리형 GPU Operator를 사용하는 경우에는 Install NVIDIA GPU Operator add-on을 활성화하고, 상위 기능 허용 항목 및 NFD 설치 조건이 충족되는지 확인합니다.
* 프로파일 3: Create GPU with RDMA pool이 활성화되어 있고, 표준 GPU 풀은 두 번째 비 RDMA GPU 풀이 명시적으로 승인된 경우에만 활성화되어 있는지 확인합니다. Create GPU Memory Cluster pool은 비활성화합니다. 관리형 GPU Operator 또는 Network Operator를 사용하는 경우 해당 Install 항목과 상위 기능 허용 항목, NFD 설치 조건이 충족되는지 확인합니다.
* Bastion 및 Kubernetes Endpoint의 허용 CIDR 대역에 승인된 소스 네트워크만 포함되어 있는지 확인합니다.
* 모든 Custom Image가 대상 리전과 지정된 이미지 컴파트먼트에서 Available 상태이고, 선택한 각 OCID가 해당 릴리스에서 승인된 이미지 기록과 일치하는지 확인합니다.
* 관리형 애드온의 설치 여부가 선택한 프로파일과 일치하는지 확인합니다. 관리형 애드온을 설치하는 경우에는 Enable optional Alloy features after base validation과 해당 Install 항목, NFD 등 종속 조건이 충족되는지 확인합니다.
* 고객이 선택한 구성요소만 활성화되어 있는지 확인합니다. 운영 환경에서는 Slurm Operator가 비활성화되어 있는지 확인합니다. 별도로 승인된 미리 보기 검증 환경에서만 활성화합니다.

## 18.2 Apply 후 검증

|검증 항목|인수 확인 기준|
|-------|-------------|
|Resource Manager|Terraform Apply가 오류 없이 완료되고, 출력 값과 상태가 보존되는지 확인합니다.|
|Images|가져온 이미지가 대상 리전과 고객 이미지 컴파트먼트에서 Available 상태이고, 기록된 OCID가 Stack에서 선택한 값과 일치하는지 확인합니다.|
|Access|승인된 네트워크에서만 Bastion 및 OKE API에 접근할 수 있는지 확인합니다.|
|OKE|Control Plane이 정상 상태이고, System 및 CPU 노드의 상태가 Ready인지 확인합니다.|
|GPU pools (Profile 2와 3)|- 프로파일 2: 표준 GPU 노드의 상태가 Ready이고, 승인된 드라이버와 CUDA 소프트웨어 스택이 로드되며, Kubernetes에 예상한 GPU 리소스가 표시되는지 확인합니다.<br>- 프로파일 3: GPU/RDMA 노드의 상태가 Ready이고, GPU, RDMA 인터페이스 및 선택한 구성에서 요구하는 RDMA 토폴로지 레이블이 존재하는지 확인합니다. 다중 노드 검증에는 두 개 이상의 노드를 사용합니다.|
|Storage|FSS를 활성화한 경우 마운트와 읽기·쓰기를 확인합니다. 로컬 RAID를 활성화한 경우 승인된 NVMe 장치, RAID 수준 및 마운트 경로를 확인합니다. Lustre를 활성화한 경우 클라이언트, 마운트 및 Kubernetes 연동을 확인합니다.|
|Components|선택한 Helm 릴리스와 Operator가 정상 상태이거나 배포 완료 상태인지 확인합니다.|
|Workload|선택한 프로파일에 대해 승인된 워크로드를 실행합니다. Profile 3에서 다중 노드 RDMA, MPI 또는 NCCL 통신을 검증하는 경우, 동일한 RDMA 풀에 속한 두 개 이상의 GPU/RDMA 노드를 사용합니다.|
|Monitoring|활성화한 경우, Prometheus 수집 대상이 정상 상태이고 Grafana 대시보드에 데이터가 표시되는지 확인합니다.|
|보안 강화|임시로 허용한 CIDR 대역을 제거하고, 고객 DNS/TLS를 구성하며, Public 서비스 노출을 제한합니다.|

> [!IMPORTANT]
> 리소스 삭제 시 참고사항: Kueue를 활성화한 경우, 배포 패키지에서 정의한 삭제 순서와 정리 절차를 확인합니다. 실행 중인 워크로드와 사용자 지정 리소스를 정리하고, 관련 컨트롤러의 정상적인 정리가 완료되었는지 확인한 후 Helm 릴리스를 삭제합니다.