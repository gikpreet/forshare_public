# 4.3 OKE with CPU and RDMA-Capable GPU Workers

Use this profile for distributed GPU workloads requiring RDMA connectivity. The validated Alloy example uses BM.GPU.B200.8 workers and release-approved Ubuntu Custom Images.

## Required Enabled Settings

* System worker pool
    * Specify the availability domain, worker node count, shape, OCPU count, and other values appropriate for the customer environment.
* Worker Pool: CPU
    * Create CPU pool
* Worker Pool: GPU with RDMA
    * Create GPU with RDMA pool
* Verify the B200 Custom Image selection.
* Verify local NVMe RAID and FSS settings.

## Required Disabled Settings

* Worker Pool: GPU
    * Create GPU pool: Leave unselected unless adding a standard GPU pool alongside the RDMA GPU pool has been explicitly approved.
* Workers: GPU Memory Cluster
    * Create GPU Memory Cluster pool
* Preview Features (not for production use)
    * Install Slurm Operator, unless preview use has been separately approved

> [!NOTE]
> OCI HPC OKE Utils and monitoring are recommended in the validated RDMA profile. Enable Kueue, MPI Operator, and the OCIR credential provider only when required by the approved workload and operational design.

## Worker Pools and Custom Images

|Input|Profile Setting|Description|
|---|---|---|
|System worker pool|Created|Configure the approved System pool availability domain, size, shape, boot volume, Kubernetes version, and imported Custom Image.|
|Create CPU pool|Enabled|Create a CPU worker pool for controllers, services, and application components that do not use GPUs.|
|Create GPU pool|Disabled|Leave the standard GPU node pool unselected. Enable only if the customer has a separately approved non-RDMA GPU requirement and sufficient capacity is available to deploy both types of GPU node pool.|
|Create GPU with RDMA pool|Enabled|Create an RDMA-capable GPU worker pool using the validated Alloy cluster network deployment method.|
|RDMA availability domain|Required|Use a validated Alloy availability domain and confirm BM.GPU.B200.8 capacity before running Terraform Plan and Terraform Apply.|
|Number of RDMA worker nodes|Required|Use one node only for provisioning and basic single-node checks. Use at least two nodes for multi-node RDMA, NCCL, or MPI validation.|
|RDMA shape|BM.GPU.B200.8|Use BM.GPU.B200.8 for the validated profile unless another shape has been separately validated and approved.|
|RDMA boot volume size|1024 GB|Use the validated value of 1024 GB or a larger customer-approved value that meets the imported image and workload requirements.|
|RDMA Kubernetes version|Cluster-approved|Use the same supported Kubernetes version as the OKE cluster unless the deployment package explicitly permits another worker pool version.|
|RDMA image type|Custom|Use a release-approved Ubuntu Custom Image containing the validated kernel, NVIDIA driver, CUDA, and RDMA software stack. If installing managed operators, check the image contents and each operator's management scope to prevent duplicate installation or conflicts.|
|Import custom image from URI|Disabled|The B200/RDMA image was imported manually in Section 2. Select the existing image instead of importing it again during Terraform Apply.|
|RDMA image compartment and Image|Required|Select the customer image compartment and the exact regional B200/RDMA image OCID recorded after import.|
|Create GPU Memory Cluster pool|Disabled|GPU Memory Cluster is not part of this B200 RDMA profile and requires separate design and tenancy enablement.|

## Cluster Components and GPU/RDMA Add-ons

|Input|Profile Setting|Description|
|---|---|---|
|Enable optional Alloy features after base validation|Conditional|After validating the base cluster and worker nodes, enable this setting when installing managed NFD, GPU Operator, or Network Operator that requires it. Select each individual installation option separately.|
|Install Node Feature Discovery add-on|Conditional|Enable when using managed NFD. When installing this package's managed GPU Operator or Network Operator, also satisfy the NFD installation conditions.|
|Install NVIDIA GPU Operator add-on|Conditional|Enable when using managed GPU Operator and satisfy the parent feature gate and NFD installation conditions.|
|Install NVIDIA Network Operator add-on|Conditional|Enable when RDMA networking components will be managed by managed Network Operator and satisfy the parent feature gate and NFD installation conditions.|
|Install Image Credential Provider for OCIR|Conditional|Enable if RDMA workloads pull private images from OCIR using instance principals. If IAM policies are managed outside the stack, configure the required repository read policy separately.|
|Install OCI HPC OKE Utils|Recommended|Enable when the validated RDMA profile requires the RDMA topology labeler, image prepuller, host execution tools, firmware and version labels, or maintenance event labels.|
|Install Kueue|Conditional|Enable only if the customer uses Kueue queues and topology-aware scheduling for the active RDMA GPU node pool.|
|Install MPI Operator|Conditional|Enable for MPIJob workloads. It is not required for NCCL-only workloads or basic node provisioning.|
|Install Slurm Operator|Disabled|Slinky/Slurm is a preview feature. Enable only for a separately approved preview deployment, then configure RDMA-specific worker settings.|

> [!NOTE]
> Creating an RDMA worker pool and installing Network Operator are separate choices. If Network Operator is not installed, confirm how the required RDMA networking components are provided by the approved image and separate configuration.

## Storage, Monitoring, and Stack Behavior

|Input|Profile Setting|Description|
|---|---|---|
|Create a RAID array using local NVMe drives|Enabled in the validated profile|Enable when BM.GPU.B200.8 has local NVMe. The validated example uses RAID 10 and bind mounts for the container runtime, Kubelet ephemeral storage, and Pod logs.|
|RAID level|10|Use RAID 10 in the validated profile. Use RAID 0 only if the customer explicitly accepts the loss of redundancy.|
|Create a File Storage Service (FSS) file system|Enabled in the validated profile|Create shared persistent storage, select the validated Alloy availability domain, and use an approved mount path such as /mnt/oci-fss.|
|Create a File Storage with Lustre file system|Disabled by default|Leave disabled by default. Enable only if a parallel file system is explicitly required. Verify that the selected worker image includes a compatible Lustre client, then configure the approved availability domain, capacity, performance tier, and Lustre mount path.|
|Install Monitoring Stack|Recommended|Enable for Prometheus, NVIDIA DCGM metrics, node problem detection, Grafana, and GPU/RDMA health validation.|
|Monitoring advanced options|Select as needed|Select to review or change individual monitoring components and exporter settings. This option itself does not determine whether monitoring is installed.|
|Install Node Problem Detector & Kube Prometheus Stack|Recommended|Enable for node health, cluster metrics, Prometheus scrape targets, and automated health checks.|
|Install AMD Device Metrics Exporter|Disabled|BM.GPU.B200.8 uses NVIDIA GPUs. Leave the AMD exporter disabled.|
|Grafana and dashboards|Conditional / recommended|Enable if the customer requires interactive GPU/RDMA dashboards. Check endpoint access and DNS configuration before exposing them externally.|
|OCI Metrics Exporter, TLS, and Grafana alerting|Conditional|Enable only after the customer approves OCI metrics collection, DNS and certificates, and alert topic configuration.|
|Securely deploy the Kubernetes resources via ORM Private Endpoint|Disabled|This guide uses a public OKE endpoint during deployment. Enable only for a separately validated private control plane configuration.|
|Minimal Alloy base deployment|Disabled|Use the mode that permits services in the validated RDMA profile and enable only the workload services explicitly approved above.|

> [!IMPORTANT]
> Profile 3 Plan check: Terraform Plan must include the System, CPU, and GPU with RDMA worker pools, the approved B200 shape and Custom Image, approved storage, and selected components. Include a standard GPU pool only if a second non-RDMA GPU pool has been explicitly approved. Do not include GPU Memory Cluster in this profile. Leave Slurm Operator disabled unless this is a separately approved preview deployment.

## Validation After Terraform Apply

* Verify that System, CPU, and B200/RDMA nodes are Ready and use the approved Custom Image OCIDs for the target region.
* Check all GPUs, the approved NVIDIA driver and CUDA versions, RDMA interfaces, and expected topology labels.
* Use at least two RDMA nodes for approved multi-node RDMA, NCCL, or MPI tests. One node can validate provisioning and basic single-node behavior, but cannot validate RDMA communication between nodes.
* For enabled options, verify FSS mounts, local RAID creation, selected Helm releases, Prometheus scrape targets, and Grafana dashboards.

---

[Previous: 4.2 OKE with CPU and Standard GPU Workers, Without RDMA](./06_profile_2.md) | [Next: 5. Identity and Network](./08_identity_network.md)
