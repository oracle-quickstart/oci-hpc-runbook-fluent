# <img src="https://github.com/oci-hpc/oci-hpc-runbook-fluent/blob/master/fluent_logo.png" height="60"> ANSYS Fluent Runbook

### Table of Contents
- [Launch Cluster Network Steps](#launch-cluster-network-steps)
- [Creation of Cluster Network through Marketplace](#creation-of-cluster-network-through-marketplace)
  - [Log In](#log-in)
  - [Virtual Cloud Network](#virtual-cloud-network)
  - [Compute Instance](#compute-instance)
  - [Block Storage](#block-storage)
  - [Mounting a Block Storage or NVME SSD Drive](#mounting-a-block-storage-or-nvme-ssd-drive)
  - [Adding a Visualization Node](#adding-a-visualization-node)
  - [Creating a Network File System](#creating-a-network-file-system)
  - [Set up a VNC](#set-up-a-vnc)
  - [Accessing a VNC](#accessing-a-vnc)
- [Gromacs Installation](#gromacs-installation)
  - [Configuring NVIDIA GPUs (CentOS Only)](#configuring-nvidia-gpus)
  - [Compiling Gromacs](#compiling-gromacs)

## Launch Cluster Network Steps
There are many ways to launch an HPC Cluster Network, this solutions guide will cover two different methods:

Via Marketplace
Manually Depending on your OS, you will want to go with a specific method. If the HPC Cluster Network marketplace image or our OCI HPC CN Terraform scripts are used, this is for Oracle Linux 7 only. If you want to use CentOS, Ubuntu or another OS, manual configuration is required.

## Creation of Cluster Network through Marketplace
Marketplace holds applications and images that can be deployed with our infrastructure. For customers that want to use Oracle Linux, an HPC Cluster Network image is available and can be launched from directly within marketplace. We suggest launching the [CFD Ready Cluster](https://cloudmarketplace.oracle.com/marketplace/en_US/listing/75645211) that will contain librairies needed for CFD.

1. Within marketplace, select <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/get_app.png" height="28"> at the top right.
2. <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/region.png" height="35"> then click <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/sign_in.png" height="28">.
3. Verify the version of the HPC Cluster image and then select the Compartment where the cluster will be launched. Accept the terms and conditions, then Launch Stack.
4. Fill out the remaing details of the stack:
  i. Select the desired AD for the compute shapes and the bastion.
  ii. Copy-paste your public ssh key
  iii. Type in the number of Compute instances for the cluster
  iv. Uncheck Install OpenFOAM
  v. If you need more than 6TB of Shared disk space, check GlusterFS and select how many servers you would need. (6TB per server)
5. Click Create.
6. Navigate to Terraform Actions then click Apply. This will launch the CN provisioning.
7. Wait until the job shows ‘Succeeded’ then navigate to Outputs to obtain the bastion and compute node private IP’s.
