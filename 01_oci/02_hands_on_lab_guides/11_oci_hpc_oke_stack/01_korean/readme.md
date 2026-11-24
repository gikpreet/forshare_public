# OCI Resource Manager를 사용하여 Softbank Alloy 환경에서 HPC OKE 환경 생성

|Target Region|ap-westtokyo-1|
|-------------|---------------|
|Alloy realm|oc55|
|Realm domain|oci.svcl.jp|
|Deployment profiles|CPU only; CPU + standard GPU; CPU + GPU/RDMA|
|Source baseline|OCI HPC OKE Quickstart main branch, v26.6.0|
Worker image source|OCI HPC OKE worker-node image catalog; import the approved .oci image by direct URL|
|Guide version|1.2 – October 2026|

이 가이드는 릴리스 승인을 받은 `.oci` 이미지 파일을 고객 태넌시로 가져오고, 세 가지 OKE 워커 풀(worker pool) 프로파일 중 하나를 선택한 후, 선택한 프로파일에 따라 달라지는 모든 Resource Manager 입력값을 구성하는 방법을 안내합니다. 각 프로파일별 설정 표에는 반드시 활성화해야 하는 항목, 비활성화 해야 하는 항목, 승인된 워크로드 요구 사항이 있는 경우에만 활성화할 수 있는 항목이 명확하게 구성되어 있습니다.

## Table of Contents

1. [배포 프로파일과 사전 요구사항](./01_deployment_profile_and_prerequisites.md)
2. [URL을 사용하여 Custom Image를 준비하고 가져오기](./02_prepare_custom_image.md)
3. [Resource Manager 스택을 생성하고 로드](./03_create_resource_manager_stack_load.md)
4. [배포 프로파일 선택](./04_select_deployment_profile.md)  
    4.1. [프로파일 1: CPU만 사용하는 OKE worker](./05_profile_1.md)  
    4.2. [CPU 및 표준 GPU worker를 사용하는 OKE 구성, RDMA 미사용](./06_profile_2.md)  
    4.3. [CPU 및 RDMA 지원 GPU Worker를 사용하는 OKE 구성](./07_profile_3.md)
5. [Identity와 Network](./08_identity_network.md)
6. [클러스터 액세스와 배스천](./09_cluster_access_and_bastion.md)
7. [배스천과 Operator Compute](./10_bastion_operator_compute.md)
8. [Operator 이미지, 도구와 Alloy 메타데이터](./11_operator_image_tools_alloy_metadata.md)
9. [OKE Cluster 구성](./12_configure_oke_cluster.md)
10. [Cluster Components](./13_cluster_compoments.md)
11. [System worker pool](./14_system_worker_pool.md)
12. [CPU worker pool – compute 설정](./15_cpu_worker_pool_compute.md)
13. [CPU worker pool – image 설정](./16_cpu_worker_pool.md)
14. [B200 GPU/RDMA worker pool](./17_b200_gpu_rdma_worker_pool.md)
15. [GPU Memory 클러스터와 Storage](./18_gpu_memory_cluster_storage.md)
16. [Monitoring과 observability](./19_monitoring_observability.md)
17. [Stack 동작, 미리 보기 기능 및 기본 모드](./20_stack_behavior.md)
18. [Plan, apply, 검증](./21_plan_apply_validation.md)

* [부록 - Stack을 구성하는 모든 항목](./appendix_stack_items.md)