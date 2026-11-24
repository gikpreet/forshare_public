# Deploying HPC OKE on Softbank Alloy with OCI Resource Manager

|Target Region|ap-westtokyo-1|
|---|---|
|Alloy realm|oc55|
|Realm domain|oci.svcl.jp|
|Deployment profiles|CPU only; CPU + standard GPU; CPU + GPU/RDMA|
|Source baseline|OCI HPC OKE Quickstart main branch, v26.6.0|
|Worker image source|OCI HPC OKE worker-node image catalog; import the approved .oci image by direct URL|
|Guide version|1.2 – October 2026|

This guide explains how to import release-approved `.oci` images into the customer tenancy, select one of three OKE worker pool profiles, and configure every Resource Manager input that varies by profile. Each profile's configuration tables identify settings that must be enabled, settings that must be disabled, and settings that may be enabled only for approved workload requirements.

## Table of Contents

1. [Deployment Profiles and Prerequisites](./01_deployment_profile_and_prerequisites.md)
2. [Preparing and Importing Custom Images by URL](./02_prepare_custom_image.md)
3. [Creating a Resource Manager Stack and Loading the Configuration](./03_create_resource_manager_stack_load.md)
4. [Selecting a Deployment Profile](./04_select_deployment_profile.md)  
    4.1. [Profile 1: CPU-Only OKE Workers](./05_profile_1.md)  
    4.2. [OKE with CPU and Standard GPU Workers, Without RDMA](./06_profile_2.md)  
    4.3. [OKE with CPU and RDMA-Capable GPU Workers](./07_profile_3.md)
5. [Identity and Network](./08_identity_network.md)
6. [Cluster Access and Bastion](./09_cluster_access_and_bastion.md)
7. [Bastion and Operator Compute](./10_bastion_operator_compute.md)
8. [Operator Image, Tools, and Alloy Metadata](./11_operator_image_tools_alloy_metadata.md)
9. [Configuring the OKE Cluster](./12_configure_oke_cluster.md)
10. [Cluster Components](./13_cluster_compoments.md)
11. [System Worker Pool](./14_system_worker_pool.md)
12. [CPU Worker Pool – Compute Settings](./15_cpu_worker_pool_compute.md)
13. [CPU Worker Pool – Image Settings](./16_cpu_worker_pool.md)
14. [B200 GPU/RDMA Worker Pool](./17_b200_gpu_rdma_worker_pool.md)
15. [GPU Memory Cluster and Storage](./18_gpu_memory_cluster_storage.md)
16. [Monitoring and Observability](./19_monitoring_observability.md)
17. [Stack Behavior, Preview Features, and Base Settings](./20_stack_behavior.md)
18. [Plan, Apply, and Validation](./21_plan_apply_validation.md)

* [Appendix – Stack Creation Inputs](./appendix_stack_items.md)
