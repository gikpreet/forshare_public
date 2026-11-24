# 4. Selecting a Deployment Profile

Use this section as the reference for every setting that varies by deployment profile. It covers only the Resource Manager inputs exposed by the supplied stack form. Screenshots in subsequent sections illustrate a GPU/RDMA configuration. Do not copy those settings without applying the profile-specific enable/disable rules below.

> [!NOTE]
> **Enabled** and **Disabled** indicate mandatory states for the profile. **Conditional** means that the setting may be enabled only when explicitly required by the customer workload and the dependent component has been approved; otherwise, leave it disabled. **Recommended** means that the profile can be deployed without the setting, but its use is recommended for validation or operations.

## 4.1 Profile Overview

### Cluster Components

|Profile-Dependent Input|Profile 1 CPU Only|Profile 2 CPU + GPU|Profile 3 CPU + GPU/RDMA|
|---------------|-------------------|------------------|------------------------|
|Create CPU Pool|Enabled|Enabled|Enabled|
|Create GPU Pool|Disabled|Enabled|Disabled[^1]|
|Create GPU with RDMA Pool|Disabled|Disabled|Enabled|
|Create GPU Memory Cluster Pool|Disabled|Disabled|Disabled|
|Enable optional Alloy features after base validation|Disabled|Conditional|Conditional|
|Install Node Feature Discovery add-on|Disabled|Conditional|Conditional|
|Install NVIDIA GPU Operator add-on|Disabled|Conditional|Conditional|
|Install NVIDIA Network Operator add-on|Disabled|Disabled|Conditional|
|Install OCI HPC OKE Utils|Disabled|Disabled by default|Recommended|
|Install Kueue|Disabled|Conditional|Conditional|
|Install MPI Operator|Disabled|Conditional|Conditional|

### Worker Pool: CPU

|Profile-Dependent Input|Profile 1 CPU Only|Profile 2 CPU + GPU|Profile 3 CPU + GPU/RDMA|
|---------------|-------------------|------------------|------------------------|
|Create CPU Pool|Enabled|Enabled|Enabled|

### Worker Pool: GPU

|Profile-Dependent Input|Profile 1 CPU Only|Profile 2 CPU + GPU|Profile 3 CPU + GPU/RDMA|
|---------------|-------------------|------------------|------------------------|
|Create GPU Pool|Disabled|Enabled|Disabled[^1]|

### Worker Pool: GPU with RDMA

|Profile-Dependent Input|Profile 1 CPU Only|Profile 2 CPU + GPU|Profile 3 CPU + GPU/RDMA|
|---------------|-------------------|------------------|------------------------|
|Create GPU with RDMA Pool|Disabled|Disabled|Enabled|

### Workers: GPU Memory Cluster

|Profile-Dependent Input|Profile 1 CPU Only|Profile 2 CPU + GPU|Profile 3 CPU + GPU/RDMA|
|---------------|-------------------|------------------|------------------------|
|Create GPU Memory Cluster Pool|Disabled|Disabled|Disabled|

### Monitoring & Observability

|Profile-Dependent Input|Profile 1 CPU Only|Profile 2 CPU + GPU|Profile 3 CPU + GPU/RDMA|
|---------------|-------------------|-------------------|------------------------|
|Install Monitoring Stack|Optional|Recommended|Recommended|

### Alloy Configuration (Category Not Displayed)

|Profile-Dependent Input|Profile 1 CPU Only|Profile 2 CPU + GPU|Profile 3 CPU + GPU/RDMA|
|---------------|-------------------|-------------------|------------------------|
|Minimal Alloy base deployment|Bare mode: Enabled|Disabled|Disabled|

---
[^1]: Enable the standard GPU node pool in Profile 3 only when there is an approved requirement for a second, non-RDMA GPU node pool. The standard GPU node pool is not part of a normal Profile 3 deployment.

---

[Previous: 3. Creating a Resource Manager Stack and Loading the Configuration](./03_create_resource_manager_stack_load.md) | [Next: Profile 1](./05_profile_1.md)
