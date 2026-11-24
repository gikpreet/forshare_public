# 4.1 Profile 1: CPU-Only OKE Workers

Use this profile for a minimal OKE cluster and CPU workloads that do not require GPU resources, RDMA networking, GPU scheduling, or MPI workload operators.

## Required Enabled Settings

* System worker pool
    * Specify the availability domain, worker node count, shape, OCPU count, and other values appropriate for the customer environment.
* Worker Pool: CPU
    * Create CPU pool
* Select Custom Images for the System and CPU pools.
* Minimal Alloy base deployment (can be enabled for a strict minimal configuration)

## Required Disabled Settings

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
> Conditional settings: OCIR credential providers, FSS, monitoring, Grafana, and OCI metrics are optional. Leave them disabled in the strict minimal profile. If any supported service is enabled, set Minimal Alloy base deployment to Disable and enable only the individually approved service inputs.

## Worker Pools and Custom Images

|Input|Profile Setting|Description|
|---|---|---|
|System Worker Pool|Created|Configure the approved availability domain, node count, CPU shape, boot volume, Kubernetes version, and imported Custom Image. This pool runs cluster services and operational workloads.|
|Create CPU pool|Enabled|Create a general-purpose CPU worker pool and configure its availability domain, node count, shape, OCPUs, memory, boot volume, and Kubernetes version.|
|Create GPU pool|Disabled|Leave unselected. Do not enter a GPU shape, GPU node count, GPU image, or other standard GPU node pool inputs.|
|Create GPU with RDMA pool|Disabled|Leave unselected. No RDMA worker pool, cluster network placement, RDMA shape, or RDMA image is created.|
|Create GPU Memory Cluster pool|Disabled|This is a separate self-managed GPU Memory Cluster configuration and is not part of the CPU-only profile.|
|Image type|Custom|Use Custom for the System and CPU worker pools. Do not use OCI platform images or prebuilt images.|
|Import custom image from URI|Disabled|The approved image was imported manually in Section 2. Select the existing Custom Image in the target region instead of importing it again during Terraform Apply.|
|Image compartment and image|Required|For the System and CPU node pools, select the customer image compartment and the exact regional image OCIDs recorded in Section 2.|

## Cluster Components and GPU/RDMA Add-ons

|Input|Profile Setting|Description|
|---|---|---|
|Enable optional Alloy features after base validation|Disabled|Keep false. The CPU-only profile does not install managed NFD, NVIDIA GPU Operator, or NVIDIA Network Operator.|
|Install Node Feature Discovery add-on|Disabled|Not required for CPU-only workers and not enabled in the validated Alloy profile.|
|Install NVIDIA GPU Operator add-on|Disabled|There is no GPU node pool. The managed GPU Operator configuration path depends on the managed NFD configuration path, which remains disabled.|
|Install NVIDIA Network Operator add-on|Disabled|Leave disabled because this profile does not manage high-performance networking components such as RDMA, SR-IOV, or Multus.|
|Install Image Credential Provider for OCIR|Conditional|Enable only if worker nodes must pull private images from OCIR using instance principals. If Create Policies is disabled, configure the required repository read policy separately.|
|Install OCI HPC OKE Utils|Disabled|Do not install the RDMA topology labeler, image prepuller, host execution tools, or related HPC utilities in the strict CPU-only profile.|
|Install Kueue|Disabled|Do not create workload queues, ResourceFlavors, or topology-aware scheduling resources. Use standard Kubernetes scheduling in the strict CPU-only profile.|
|Install MPI Operator|Disabled|Do not install MPI Operator because this profile does not deploy MPIJob workloads.|
|Install Slurm Operator|Disabled|Slurm/Slinky is a preview feature and is not part of the CPU-only customer profile.|

## Storage, Monitoring, and Stack Behavior

|Input|Profile Setting|Description|
|---|---|---|
|Create RAID array using local NVMe drives|Disabled by default|Enable only if the selected CPU shape has local NVMe and the customer explicitly requires improved performance for ephemeral container storage, Kubelet, or Pod logs. Otherwise, leave disabled.|
|Create a File Storage Service (FSS) file system|Conditional|Enable only if CPU workloads require shared persistent storage. Configure the approved availability domain and mount point.|
|Create a File Storage with Lustre file system|Disabled by default|Leave disabled for normal CPU workloads. Enable only for an approved CPU HPC configuration when the selected Custom Image includes the Lustre client.|
|Install Monitoring Stack|Optional|Leave disabled in the strict minimal profile. Enable if Prometheus, node health monitoring, or Grafana is an explicit operational requirement.|
|Advanced options / Install AMD Device Metrics Exporter|Conditional|If monitoring is enabled, configure the monitoring inputs displayed under Advanced options. Leave Install AMD Device Metrics Exporter disabled in the CPU-only profile. Enable Grafana and Grafana dashboards only if required.|
|Grafana, dashboards, OCI Metrics, TLS, and alerting|Conditional|Enable only after the customer approves DNS, certificates, dashboards, OCI metrics collection, and alert destinations.|
|Securely deploy the Kubernetes resources via ORM Private Endpoint|Disabled|This guide uses a public OKE endpoint during deployment. Enable only for a separately validated private control plane configuration.|
|Minimal Alloy base deployment|Enable for a strict minimal configuration. Disable when using optional services, subject to the conditions below.|Enable only when all optional services are disabled. If monitoring, Kueue, MPI, OCI HPC OKE Utils, or another supported service is selected, disable base mode and control each approved service individually.|

> [!IMPORTANT]
> Terraform Plan must include only the System and CPU worker pools. It must not include standard GPU, GPU with RDMA, or GPU Memory Cluster node pools, OCI HPC OKE Utils, Kueue, MPI Operator, or Slurm resources. Install Node Feature Discovery, NVIDIA GPU Operator, and NVIDIA Network Operator must remain disabled.

## Validation After Terraform Apply

* Verify that System and CPU nodes are Ready and use the recorded Custom Image OCIDs.
* Verify that Kubernetes does not report GPU resources. Confirm that this deployment has no GPU/RDMA interfaces or RDMA-specific topology labels.
* Run the approved CPU workload. If storage or monitoring options were enabled, verify that they meet the requirements.

---

[Previous: 4. Selecting a Deployment Profile](./04_select_deployment_profile.md) | [Next: Profile 2: OKE with CPU and Standard GPU Workers, Without RDMA](./06_profile_2.md)
