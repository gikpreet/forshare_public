# 18. Plan, Apply, and Validation

## Review Terraform Plan

* Verify that the creation location of each resource matches the approved design. Check the target locations of resources to which compartment, region, and availability domain apply. Review IAM policies and references to existing networks and images separately.
* Verify that the selected profile exactly matches Section 4. Profile 1 includes only System and CPU node pools. Profile 2 adds only the standard GPU node pool. Profile 3 adds only the GPU with RDMA node pool unless a second non-RDMA GPU node pool has been explicitly approved.
* Profile 1: Verify that Create GPU pool, Create GPU with RDMA pool, and Create GPU Memory Cluster pool are disabled. Also disable Enable optional Alloy features after base validation, Install Node Feature Discovery add-on, Install NVIDIA GPU Operator add-on, and Install NVIDIA Network Operator add-on. Verify that Install OCI HPC OKE Utils, Install Kueue, Install MPI Operator, and Install Slurm Operator are disabled.
* Profile 2: Verify that Create GPU with RDMA pool, Create GPU Memory Cluster pool, and Install NVIDIA Network Operator add-on are disabled. If using managed GPU Operator, enable Install NVIDIA GPU Operator add-on and verify that the parent feature gate and NFD installation conditions are satisfied.
* Profile 3: Verify that Create GPU with RDMA pool is enabled and that the standard GPU pool is enabled only if a second non-RDMA GPU pool has been explicitly approved. Disable Create GPU Memory Cluster pool. If using managed GPU Operator or Network Operator, verify that the corresponding Install option, parent feature gate, and NFD installation conditions are satisfied.
* Verify that the bastion and Kubernetes endpoint allowed CIDRs include only approved source networks.
* Verify that all Custom Images are Available in the target region and designated image compartment, and that each selected OCID matches the image record approved for the release.
* Verify that managed add-on installation choices match the selected profile. When installing managed add-ons, verify that Enable optional Alloy features after base validation, the corresponding Install option, and dependencies such as NFD are satisfied.
* Verify that only customer-selected components are enabled. In production, verify that Slurm Operator is disabled. Enable it only in a separately approved preview validation environment.

## Validation After Apply

|Check|Acceptance Criteria|
|---|---|
|Resource Manager|Verify that Terraform Apply completes without errors and that outputs and state are retained.|
|Images|Verify that imported images are Available in the target region and customer image compartment, and that recorded OCIDs match the values selected in the stack.|
|Access|Verify that the bastion and OKE API are accessible only from approved networks.|
|OKE|Verify that the control plane is healthy and System and CPU nodes are Ready.|
|GPU pools (Profiles 2 and 3)|- Profile 2: Verify that standard GPU nodes are Ready, the approved driver and CUDA software stack load, and Kubernetes reports the expected GPU resources.<br>- Profile 3: Verify that GPU/RDMA nodes are Ready and that GPUs, RDMA interfaces, and the RDMA topology labels required by the selected configuration are present. Use at least two nodes for multi-node validation.|
|Storage|If FSS is enabled, verify mounting and read/write access. If local RAID is enabled, verify the approved NVMe devices, RAID level, and mount paths. If Lustre is enabled, verify the client, mounts, and Kubernetes integration.|
|Components|Verify that selected Helm releases and operators are healthy or have completed deployment.|
|Workload|Run the approved workload for the selected profile. When validating multi-node RDMA, MPI, or NCCL communication in Profile 3, use at least two GPU/RDMA nodes in the same RDMA pool.|
|Monitoring|If enabled, verify that Prometheus scrape targets are healthy and Grafana dashboards display data.|
|Security hardening|Remove temporary allowed CIDRs, configure customer DNS/TLS, and restrict public service exposure.|

> [!IMPORTANT]
> Resource deletion: If Kueue is enabled, check the deletion order and cleanup procedure defined by the deployment package. Clean up running workloads and custom resources, verify that the related controllers have completed normal cleanup, and then delete the Helm release.
