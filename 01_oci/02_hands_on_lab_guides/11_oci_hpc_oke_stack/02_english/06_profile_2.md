# 4.2 OKE with CPU and Standard GPU Workers, Without RDMA

Use this profile for GPU-accelerated workloads using a standard OKE managed GPU node pool and VCN networking. This profile does not create an RDMA cluster network or an RDMA worker pool.

## Required Enabled Settings

* System worker pool
    * Specify the availability domain, worker node count, shape, OCPU count, and other values appropriate for the customer environment.
* Worker Pool: CPU
    * Create CPU pool
* Worker Pool: GPU
    * Create GPU pool
* Select approved Custom Images appropriate for each role and shape in the System, CPU, and standard GPU pools.

## Required Disabled Settings

* Cluster Components
    * Install NVIDIA Network Operator add-on
* Worker Pool: GPU with RDMA
    * Create GPU with RDMA Pool
* Workers: GPU Memory Cluster
    * Create GPU Memory Cluster Pool
* Preview Features (not for production use)
    * Install Slurm Operator, unless preview use has been separately approved

> [!NOTE]
> Enable Kueue, MPI Operator, OCI HPC OKE Utils, monitoring, FSS, Lustre, and the OCIR credential provider only when explicitly required by the workload operations or operational design. Otherwise, leave them disabled. Monitoring is recommended for GPU health checks and workload validation; determine whether to install it based on operational requirements.
> If GPUs will be managed by GPU Operator, Install NVIDIA GPU Operator add-on can be enabled. In that case, also enable Enable optional Alloy features after base validation.

## Worker Pools and Custom Images

|Input|Profile Setting|Description|
|---|---|---|
|System worker pool|Created|Configure the approved System pool availability domain, node count, shape, boot volume, Kubernetes version, and imported Custom Image.|
|Create CPU pool|Enabled|Create and size a general-purpose CPU worker pool for system support workloads and application workloads that do not use GPUs.|
|Create GPU pool|Enabled|Create a standard OKE managed GPU worker pool. In a normal Profile 2 configuration, enable only this type of GPU node pool.|
|GPU availability domain|Required|Select an availability domain with confirmed capacity for the selected standard GPU shape.|
|Number of GPU worker nodes|Required|Set the node count required by the workload and availability design. One node can be used for basic provisioning and single-node validation.|
|GPU Shape|Required|Select a customer-approved non-RDMA GPU node pool shape supported by the deployment package and available in the target region.|
|GPU boot volume and Kubernetes version|Required|Set the boot volume size to meet the approved image and workload minimum requirements. Use the same approved Kubernetes version as the cluster unless a supported per-node-pool version is required.|
|GPU image type|Custom|Use an imported Custom Image matching the GPU shape, Ubuntu release, kernel, GPU driver, and CUDA release. Do not use platform images or prebuilt images.|
|GPU image compartment and Image|Required|Select the image compartment and the exact regional GPU image OCID recorded after the image import in Section 2.|
|Create GPU with RDMA pool|Disabled|Leave unselected. Do not configure an RDMA node count, RDMA shape, cluster network placement, or RDMA image.|
|Create GPU Memory Cluster pool|Disabled|GPU Memory Cluster is a separate configuration and is not used in this profile.|

## Cluster Components and GPU/RDMA Add-ons

|Input|Profile Setting|Description|
|---|---|---|
|Enable optional Alloy features after base validation|Conditional|Controls the managed NFD, managed GPU Operator, and managed Network Operator configuration paths. Enable when NFD and GPU Operator will be used.|
|Install Node Feature Discovery add-on|Conditional|Enable when managed Node Feature Discovery (NFD) will be used.|
|Install NVIDIA GPU Operator add-on|Conditional|Enable when using managed GPU Operator. Also enable Enable optional Alloy features after base validation and Install Node Feature Discovery add-on as required by this package's installation conditions.|
|Install NVIDIA Network Operator add-on|Disabled|Leave disabled.|
|Install Image Credential Provider for OCIR|Conditional|Enable if workloads pull private images from OCIR using instance principals. Otherwise, leave disabled.|
|Install OCI HPC OKE Utils|Disabled by default|RDMA topology labeling is not required. Enable only if non-RDMA utility functions such as image prepulling or host execution tools are explicitly required.|
|Install Kueue|Conditional|Enable only if queuing and topology-aware scheduling are required for the standard GPU node pool. Otherwise, leave disabled.|
|Install MPI Operator|Conditional|Enable only for MPIJob workloads. Profile 2 can run MPI over standard networking but does not provide RDMA acceleration.|
|Install Slurm Operator|Disabled|Leave the Slinky/Slurm preview configuration path disabled unless the customer uses a separately approved preview deployment.|

## Storage, Monitoring, and Stack Behavior

|Input|Profile Setting|Description|
|---|---|---|
|Create RAID array using local NVMe drives|Conditional|Enable only if the selected GPU shape has local NVMe and the workload requires high-throughput ephemeral storage for containers, Kubelet, or Pod logs.|
|Create a File Storage Service (FSS) file system|Conditional|Enable if the workload requires shared persistent storage. Configure the approved availability domain and mount point.|
|Create a File Storage with Lustre file system|Conditional|Enable only if there is an approved requirement for a parallel file system and the selected Custom Image includes the Lustre client.|
|Install Monitoring Stack|Recommended|Recommended for GPU node health checks, Prometheus metrics, NVIDIA DCGM metrics, and workload validation. It is not a prerequisite for creating the GPU node pool.|
|Install Node Problem Detector & Kube Prometheus Stack|Recommended|When using the monitoring stack, enable to collect node health and cluster metrics.|
|OCI Metrics Exporter, TLS, and Grafana alerting|Conditional|Enable only after the customer approves OCI metrics collection, DNS and certificate configuration, and alert destinations.|
|Securely deploy the Kubernetes resources via ORM Private Endpoint|Disabled|This guide uses a public OKE endpoint. Enable only for a separately validated private control plane deployment.|
|Minimal Alloy base deployment|Disabled|Use the mode that permits services in the standard GPU profile, particularly when monitoring, Kueue, MPI, or other selected operational services are enabled.|

> [!IMPORTANT]
> Profile 2 Plan check: Terraform Plan must include the System, CPU, and standard GPU worker pools. It must not include a GPU with RDMA node pool, cluster network or Compute Cluster RDMA resources, or a GPU Memory Cluster node pool. Leave Install Slurm Operator disabled unless preview use has been separately approved.

## Validation After Terraform Apply

* Verify that System, CPU, and standard GPU nodes are Ready and use the approved image OCIDs.
* Verify that the NVIDIA driver and CUDA software stack load correctly, Kubernetes reports the expected allocatable GPU resources, and the approved GPU workload completes. RDMA interfaces and RDMA topology labels must not be present. Do not perform multi-node RDMA validation.

---

[Previous: 4.1 Profile 1: CPU-Only OKE Workers](./05_profile_1.md) | [Next: 4.3 OKE with CPU and RDMA-Capable GPU Workers](./07_profile_3.md)
