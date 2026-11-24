# 4. 배포 프로파일 선택

배포 프로파일에 따라 달라지는 모든 설정은 이 절을 기준으로 구성해야 합니다. 제공된 Stack 양식에서 표시되는 Resource Manager 입력 항목만 포함되어 있습니다. 이후 절의 스크린샷은 GPU/RDMA 구성 예시이므로, 아래의 프로파일 별 활성화 및 비활성화 규칙을 적용하지 않은 상태로 그대로 따라해서는 안됩니다.

> [!NOTE]
> Enabled와 Disabled는 각 프로파일에서 반드시 적용해야 하는 활성화 및 비활성화 상태를 의미합니다. Conditional은 고객 워크로드에서 명시적으로 요구하고 해당 종속 구성요소의 사용이 승인된 경우에만 활성화하며, 그 외에는 비활성화 상태로 유지해야 함을 의미합니다. Recommended는 해당 항목 없이도 프로파일을 배포할 수 있지만, 검증 또는 운영을 위해 사용을 권장함을 의미합니다.

## 4.1 프로파일 개요

### Cluster Components

|프로파일 기반 입력|Profile 1 CPU Only|Profile 2 CPU + GPU|Profile 3 CPU + GPU/RDMA|
|---------------|-------------------|------------------|------------------------|
|Create CPU Pool|Enabled|Enabled|Enabled|
|Create GPU Pool|Disabled|Enabled|Disabled[^1]|
|Create GPU with RDMA Pool|Disabled|Disabled|Enabled|
|Create GPU Memory Cluster Pool|Disabled|Disabled|Disabled|
|Enable optional Alloy features after base validation|Disabled|Conditional|Conditional|
|Install Node Feature Discovery add-on|Disabled|Conditional|Conditional|
|Install NVIDIA GPU Operator add-on|Disabled|Conditional|Conditional|
|Install NVIDIA Network Operator add-on|Disabled|Disabled|Conditional|
|Install OCI HPC OKE Utils|Disabled|기본적으로 Disabled|Recommended|
|Install Kueue|Disabled|Conditional|Conditional|
|Install MPI Operator|Disabled|Conditional|Conditional|

### Worker Pool: CPU

|프로파일 기반 입력|Profile 1 CPU Only|Profile 2 CPU + GPU|Profile 3 CPU + GPU/RDMA|
|---------------|-------------------|------------------|------------------------|
|Create CPU Pool|Enabled|Enabled|Enabled|

### Worker Pool: GPU

|프로파일 기반 입력|Profile 1 CPU Only|Profile 2 CPU + GPU|Profile 3 CPU + GPU/RDMA|
|---------------|-------------------|------------------|------------------------|
|Create GPU Pool|Disabled|Enabled|Disabled[^1]|

### Worker Pool: GPU with RDMA

|프로파일 기반 입력|Profile 1 CPU Only|Profile 2 CPU + GPU|Profile 3 CPU + GPU/RDMA|
|---------------|-------------------|------------------|------------------------|
|Create GPU with RDMA Pool|Disabled|Disabled|Enabled|

### Workers: GPU Memory Cluster

|프로파일 기반 입력|Profile 1 CPU Only|Profile 2 CPU + GPU|Profile 3 CPU + GPU/RDMA|
|---------------|-------------------|------------------|------------------------|
|Create GPU Memory Cluster Pool|Disabled|Disabled|Disabled|

### Monitoring & Observability

|프로파일 기반 입력|Profile 1 CPU Only|Profile 2 CPU + GPU|Profile 3 CPU + GPU/RDMA|
|---------------|-------------------|-------------------|------------------------|
|Install Monitoring Stack|Optional|Recommended|Recommended|

### Alloy configuration (분류가 표시되지 않음)

|프로파일 기반 입력|Profile 1 CPU Only|Profile 2 CPU + GPU|Profile 3 CPU + GPU/RDMA|
|---------------|-------------------|-------------------|------------------------|
|Minimal Alloy base deployment|Bare mode: Enabled|Disabled|Disabled|

---
[^1]: RDMA를 사용하지 않는 두 번째 GPU 노드 풀에 대해 승인된 요구사항을 가지고 있는 경우에만 프로파일 3에서 표준 GPU 노드 풀을 활성화합니다. 표준 GPU 노드 풀은 일반적인 프로파일 3 구성에 포함되지 않습니다.

---

[이전: 3. Resource Manager 스택을 생성하고 로드](./03_create_resource_manager_stack_load.md) | [다음: 프로파일 1](./05_profile_1.md)