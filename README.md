# <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/fluent_logo.png" height="60"> ANSYS Fluent Runbook

# Introduction
This Runbook will take you through the process of deploying an ANSYS Fluent cluster on Oracle Cloud with low latency networking between the compute nodes. Running ANSYS Fluent on Oracle Cloud is quite straightforward, follow along this guide for all the tips and tricks.

Fluent software contains the broad, physical modeling capabilities needed to model flow, turbulence, heat transfer and reactions for industrial applications. These range from air flow over an aircraft wing to combustion in a furnace, from bubble columns to oil platforms, from blood flow to semiconductor manufacturing and from clean room design to wastewater treatment plants. Fluent spans an expansive range, including special models, with capabilities to model in-cylinder combustion, aero-acoustics, turbomachinery and multiphase systems. [Ansys Fluent Website](https://www.ansys.com/products/fluids/ansys-fluent)

For details of the architecture, see [_High Performance Computing: Ansys Fluent on Oracle Cloud Infrastructure_](https://docs.oracle.com/en/solutions/hpc-fluent/)
 
 ## Prerequisites

- Permission to `manage` the following types of resources in your Oracle Cloud Infrastructure tenancy: `vcns`, `internet-gateways`, `route-tables`, `network-security-groups`, `subnets`, and `instances`.

- Quota to create the following resources: 1 VCN, 2 subnets, 1 Internet Gateway, 1 NAT Gateway, 1 Service Gateway, 3 route rules, and minimum 2 compute instances in instance pool or cluster network (plus bastion host).

If you don't have the required permissions and quota, contact your tenancy administrator. See [Policy Reference](https://docs.cloud.oracle.com/en-us/iaas/Content/Identity/Reference/policyreference.htm), [Service Limits](https://docs.cloud.oracle.com/en-us/iaas/Content/General/Concepts/servicelimits.htm), [Compartment Quotas](https://docs.cloud.oracle.com/iaas/Content/General/Concepts/resourcequotas.htm).

## Deploy Using Oracle Resource Manager

1. Click [![Deploy to Oracle Cloud](https://oci-resourcemanager-plugin.plugins.oci.oraclecloud.com/latest/deploy-to-oracle-cloud.svg)](https://cloud.oracle.com/resourcemanager/stacks/create?region=home&zipUrl=https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/releases/latest/download/oci-hpc-runbook-fluent-stack-latest.zip)

    If you aren't already signed in, when prompted, enter the tenancy and user credentials.

2. Review and accept the terms and conditions.

3. Select the region where you want to deploy the stack.

4. Follow the on-screen prompts and instructions to create the stack.

   a. If you choose a shape that doesn't have NVMe, please add additional storage
   <p align="center">
<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/Additional%20Storage.png" height="300" >
 </p>

5. After creating the stack, click **Terraform Actions**, and select **Plan**.

6. Wait for the job to be completed, and review the plan.

    To make any changes, return to the Stack Details page, click **Edit Stack**, and make the required changes. Then, run the **Plan** action again.

7. If no further changes are necessary, return to the Stack Details page, click **Terraform Actions**, and select **Apply**. 

## Deploy Using the Terraform CLI

### Clone the Module
Now, you'll want a local copy of this repo. You can make that with the commands:

    git clone https://github.com/oracle-quickstart/oci-hpc-runbook-fluent.git
    cd oci-hpc-runbook-fluent
    ls

### Set Up and Configure Terraform

1. Complete the prerequisites described [here](https://github.com/cloud-partners/oci-prerequisites).

2. Create a `terraform.tfvars` file, and specify the following variables:

```
# Authentication
tenancy_ocid         = "<tenancy_ocid>"
user_ocid            = "<user_ocid>"
fingerprint          = "<finger_print>"
private_key_path     = "<pem_private_key_path>"

# Region
region = "<oci_region>"

# Availablity Domain 
ad = "<availablity doman>" # for example "GrCH:US-ASHBURN-AD-1"

# Bastion 
bastion_ad                = "<availablity doman>" # for example "GrCH:US-ASHBURN-AD-1"
bastion_boot_volume_size  = "<bastion_boot_volume_size>" # for example 1000
bastion_shape             = "<bastion_shape>" # for example "VM.Standard.E3.Flex"

boot_volume_size          = "<boot_volume_size>" # for example 100
cluster_block_volume_size = 1000
scratch_nfs_type_pool     = "block"
node_count                = "<node_count>" # for example 2
ssh_key                   = "<ssh_key>"
targetCompartment         = "<targetCompartment>" 
use_custom_name           = false
use_existing_vcn          = false
use_marketplace_image     = true
use_standard_image        = true
cluster_network           = false
instance_pool_shape       = "<instance_pool_shape>" # for example VM.Standard.E3.Flex
fluent_binaries           = "<fluent_binaries>" # for example https://objectstorage.us-phoenix-1.oraclecloud.com/p/(...)/n/hpc/b/HPC_APPS/o/FLUIDS_2020R2_LINX64.tar"
fluent_license_server_ip = "<fluent_license_server_ip>"

````

### Create the Resources
Run the following commands:

    terraform init
    terraform plan
    terraform apply

### Destroy the Deployment
When you no longer need the deployment, you can run this command to destroy the resources:

    terraform destroy
    
# Architecture
![](https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/architecture-hpc.png "Architecture for Running Ansys Fluent in OCI")
The architecture for this runbook is as follow, we have one small machine (bastion) that you will connect into. The compute nodes will be on a separate private network linked with RDMA RoCE v2 networking. The bastion will be accesible through SSH from anyone with the key (or VNC if you decide to enable it). Compute nodes will only be accessible through the bastion inside the network. This is made possible with 1 Virtual Cloud Network with 2 subnets, one public and one private.

The above baseline infrastructure provides the following specifications:
-	Networking
    -	1 x 100 Gbps RDMA over converged ethernet (ROCE) v2
    -	Latency as low as 1.5 µs
-	HPC Compute Nodes (BM.HPC2.36)
    -	6.4 TB Local NVME SSD storage per node
    -	36 cores per node
    -	384 GB memory per node

# Upload Fluent binaries to Object Storage
1. Log In

You can start by logging in the Oracle Cloud console. If this is the first time, instructions to do so are available [here](https://docs.cloud.oracle.com/iaas/Content/GSG/Tasks/signingin.htm).
Select the region in which you wish to create your Object Storage Bucket. Click on the current region in the top right dropdown list to select another one. 

2. Go to Buckets by clicking on  <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-lsdyna/blob/main/images/menu.png?raw=true" height="30">  and selecting **Storage**  <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-lsdyna/blob/main/images/Storage%20option.png?raw=true" height="130">  > **Buckets**  <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-lsdyna/blob/main/images/Buckets.png?raw=true" height="70">

3. Create a bucket by clicking  <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-lsdyna/blob/main/images/Create%20bucket.png?raw=true" height="30">. Give your bucket a name and select the storage tier and encryption.

4. Once the bucket has been created, upload an object (binary) to the bucket by clicking **Upload**  <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-lsdyna/blob/main/images/Upload%20Object.png?raw=true" height="90">  under **Objects**.

5. Create a Pre-Authenitcated Request (PAR) using the following steps:

	- Click on  <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-lsdyna/blob/main/images/par%20menu.png?raw=true" height="40">  for the object, then select  <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-lsdyna/blob/main/images/Create%20PAR%20button%20from%20menu.png?raw=true" height="30"> 

	- Select  <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-lsdyna/blob/main/images/Object%20option%20PAR%20menu.png?raw=true" height="100">  for the **Pre-Authenticated Request Target** and then select an access type.

	- Click  <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-lsdyna/blob/main/images/Create%20PAR.png?raw=true" height="30">

	- Be sure to copy the PAR URL by clicking <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-lsdyna/blob/main/images/Copy.png?raw=true" height="30"> before closing because you will **NOT** have access to the URL again.  

6. Add this PAR to the lsdyna_binaries variable.

# Run Ansys Fluent

### Step 1. Go to your model to run Fluent
```
cd /nfs/scratch/fluent/work
```

### Step 2. To set your own flags, modify each mpirun.fl file in the fluent folder with your own flags.
Example using Intel 2018 Flags: 
```
echo export FLUENT_INTEL_MPIRUN_FLAGS='"-iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0 -genv I_MPI_PIN_PROCESSOR_LIST=0-35 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71"' | sudo tee -a ~/.bashrc
```
<details>
  <summary>Refer to the runbook and blog for more flags and guidance: 
</summary>
https://blogs.oracle.com/cloud-infrastructure/running-applications-on-oracle-cloud-using-cluster-networking
</details>

### Step 3: If setting the flags in step 2 is too annoying, you can also use -mpiopt flag when you run fluent to set the flags.
```
-mpiopt="-iface enp94s0f0 -genv I_MPI_FABRICS shm:dapl -genv DAT_OVERRIDE /etc/dat.conf -genv I_MPI_DAT_LIBRARY /usr/lib64/libdat2.so -genv I_MPI_DAPL_PROVIDER ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK 0 -genv I_MPI_FALLBACK=0"![image](https://user-images.githubusercontent.com/31706671/122606828-92423500-d047-11eb-9d2b-da4167a19928.png)
```
### Step 4: Within the work folder, set the following variables and run fluent on the desired number of cores. 
Note that this script uses Intel MPI – it is recommended to use Intel MPI for Ansys Fluent on OCI HPC.
```
modelname=f1_racecar_140m
N=<number of cores>
```
```
/nfs/scratch/fluent/install/v{{VersionNumber}/fluent/bin/fluentbench.pl -ssh -noloadchk -casdat=$modelname -t$N -cnf=machinefile -mpi=intel -mpiopt="-iface enp94s0f0 -genv I_MPI_FABRICS shm:dapl -genv DAT_OVERRIDE /etc/dat.conf -genv I_MPI_DAT_LIBRARY /usr/lib64/libdat2.so -genv I_MPI_DAPL_PROVIDER ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK 0 -genv I_MPI_FALLBACK=0"
```
Note: You can kill stopped jobs via kill -9 $(jobs -p)

### Step 5: The output of the simulation produces the following files:
a.	.out file – shows the output of the run, including the solver rating, number of seconds per iteration and speed

b.	.log file – shows the history of the benchmark run

c.	.trn – shows the transcript of the run

You can plot the solver rating, speed and calculate the efficiency at each core count you run, and then plot to determine the optimal parameters to run at.

# Benchmark Example

Performances of Fluent are often measured using the Formula 1 benchmark with 140 Millions cells. The next graph is showing how using more nodes impact the runtime, with a scaling really close to 100%. RDMA network only start to differentiate versus regular TCP runs if the Cells / Core ratio starts to go down. Here is a comparison with AWS C5n HPC machines. 

<p align="center">
 
<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/fluent_bench.png" height="500" >
 </p>
