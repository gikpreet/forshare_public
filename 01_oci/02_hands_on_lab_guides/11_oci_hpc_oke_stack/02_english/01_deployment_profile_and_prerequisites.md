# 1. Deployment Profiles and Prerequisites

This guide describes deployment on Softbank Alloy using three profiles for three HPC OKE scenarios.

* Profile 1: An OKE cluster with CPU nodes only
* Profile 2: An OKE cluster with CPU and GPU nodes
* Profile 3: A GPU cluster with CPU nodes and RDMA networking

> [!NOTE]
> A deployment profile is a predefined configuration option that determines the types of worker nodes and GPU/HPC infrastructure deployed when an OKE cluster is created through OCI Resource Manager (Terraform).

## 1.1 Validated Profiles

|Area|Deployment Guidance|
|---|---|
|Access model|If the OKE API endpoint and Kubernetes load balancer are public during deployment, restrict access to both endpoints to approved customer networks.|
|Node pools|In addition to the base system node pool (System Pool), select either a GPU configuration using a standard GPU node pool or a CPU configuration that includes a GPU/RDMA node pool.|
|Storage|Use FSS for shared storage and optionally configure NVMe RAID. Use a Lustre file system only when explicitly required.|
|Optional services|Configure the OCIR (Oracle Cloud Infrastructure Registry) credential provider, OCI HPC OKE utilities, Kueue workload scheduler, MPI Operator, and monitoring tools as needed.|
|Managed add-ons for GPU/RDMA networking|Node Feature Discovery (NFD), NVIDIA GPU Operator, and NVIDIA Network Operator can be configured during deployment.|
|Base mode|Use `Minimal Alloy base deployment` only for a base OKE cluster without optional services.|

## 1.2 Prerequisites

* Confirm service availability and resource capacity for every compute shape included in the selected deployment profile. For an RDMA deployment, also confirm sufficient capacity to create BM.GPU.B200.8 instances in the target availability domain.
* Obtain the customer compartment, administrator and VPN network CIDRs, SSH public key, and nonoverlapping VCN and Kubernetes Service CIDRs.
* Import the release-approved bastion, operator, and worker images as `Compute Custom Images` in the target OCI region using the direct URL procedure in [Section 2, Preparing and Importing Custom Images by URL](02_prepare_custom_image.md).
* Verify that the deploying user has permission to create the required IAM policies and dynamic groups or use existing policies and groups.
* Confirm the customer's decisions on optional components, public DNS, TLS certificates, and Grafana alerting.

> [!WARNING]
> Do not use 0.0.0.0/0 for bastion SSH access or the Kubernetes API endpoint. Allow only the minimum customer-approved CIDRs, and remove temporary access permissions after validation.

## 1.3 Deployment Procedure

1. Copy the approved `.oci` URL from the worker image catalog and import the image as an OCI Compute Image by following [Section 2, Preparing and Importing Custom Images by URL](./02_prepare_custom_image.md).
2. Create an OCI Resource Manager stack and load the required configuration files by following [Section 3, Creating a Resource Manager Stack and Loading the Configuration](./03_create_resource_manager_stack_load.md). Select a deployment profile in [Section 4, Selecting a Deployment Profile](./04_select_deployment_profile.md), then configure the inputs described from [Section 5, Identity and Network](./08_identity_network.md) through [Section 17, Stack Behavior, Preview Features, and Base Settings](./20_stack_behavior.md).
3. Run Terraform Plan and review the resources to be created or changed, CIDRs, selected Custom Image OCIDs, IAM changes, and enabled components.
4. Run Terraform Apply to deploy the resources. Complete the validation procedure in [Section 18, Plan, Apply, and Validation](./21_plan_apply_validation.md), then apply the customer's security hardening policies and DNS controls.

---

Next: [2. Preparing and Importing Custom Images by URL](./02_prepare_custom_image.md)
