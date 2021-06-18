# <img src="https://github.com/oci-hpc/oci-hpc-runbook-fluent/blob/master/fluent_logo.png" height="60"> ANSYS Fluent Runbook

# Introduction

Fluent software contains the broad, physical modeling capabilities needed to model flow, turbulence, heat transfer and reactions for industrial applications. These range from air flow over an aircraft wing to combustion in a furnace, from bubble columns to oil platforms, from blood flow to semiconductor manufacturing and from clean room design to wastewater treatment plants. Fluent spans an expansive range, including special models, with capabilities to model in-cylinder combustion, aero-acoustics, turbomachinery and multiphase systems. [Ansys Fluent Website](https://www.ansys.com/products/fluids/ansys-fluent)

This Runbook will take you through the process of deploying an ANSYS Fluent cluster on Oracle Cloud with low latency networking between the compute nodes. Running ANSYS Fluent on Oracle Cloud is quite straightforward, follow along this guide for all the tips and tricks.

<p align="center">
<img src="https://github.com/oci-hpc/oci-hpc-runbook-fluent/blob/master/fluent_image.jpeg" height="300" >
 </p>
 

# Architecture
![](https://github.com/oci-hpc/oci-hpc-runbook-shared/blob/master/images/arch.png "Architecture for Running StarCCM+ in OCI")
The architecture for this runbook is as follow, we have one small machine (bastion) that you will connect into. The compute nodes will be on a separate private network linked with RDMA RoCE v2 networking. The bastion will be accesible through SSH from anyone with the key (or VNC if you decide to enable it). Compute nodes will only be accessible through the bastion inside the network. This is made possible with 1 Virtual Cloud Network with 2 subnets, one public and one private.

The above baseline infrastructure provides the following specifications:
* VCN
  *	Public Subnet, Security List, Route Table
  *	Private Subnet, Security List, Route Table
  *	Internet Gateway
  *	NAT Gateway
*	Compute Nodes
  *	Bastion Host in a Public Subnet
  *	HPC Compute Nodes in Private Subnet

The above baseline infrastructure provides the following specifications:
-	Networking
    -	1 x 100 Gbps RDMA over converged ethernet (ROCE) v2
    -	Latency as low as 1.5 µs
-	HPC Compute Nodes (BM.HPC2.36)
    -	6.4 TB Local NVME SSD storage per node
    -	36 cores per node
    -	384 GB memory per node

# Phase 1. Run Fluent
## Step 1. Locate the Ansys Fluent version and models you want to use from object storage.
1. See table below for the most common ones.
<details>
  <summary>For more Fluent versions, check here:</summary>
https://console.us-phoenix-1.oraclecloud.com/object-storage/buckets/hpc/HPC_APPS/objects 
</details>
<details>
  <summary>For more models, check here:</summary>
https://console.us-phoenix-1.oraclecloud.com/object-storage/buckets/hpc/HPC_BENCHMARKS/objects 
</details>

## Step 2. Install the models and fluent version in /nfs/scratch or /mnt/nfs-share  (path will depend on the image version you are using)






Running Fluent is pretty straightforward: You can either start the GUI if you have a VNC session started with
```
/mnt/gluster-share/install/fluent/v190/fluent/bin/fluent
```
To specify the host you need to run on, you need to create a machinefile. You can generate it as follow, or manually. Format is hostname:corenumber.
```
sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
```
To run on multiple nodes, place the model on the share drive (Ex:/mnt/nfs-share/work/).
Example provided here is to run any of the benchmark model provided on the ANSYS website. You can add it to object storage like the installer and download it or scp it to the machine.
```
wget https://objectstorage.us-phoenix-1.oraclecloud.com/p/qwbdhqwdhqh/n/tenancy/b/bucket/o/f1_racecar_140m.tar  -O - | tar x
mkdir f1_racecar_140m
mv bench/fluent/v6/f1_racecar_140m/cas_dat/* f1_racecar_140m/
gunzip f1_racecar_140m/*
rm -rf bench/
```

Now that you have set up the model, you can run it with the following command (change the modelname and core number):
```
modelname=f1_racecar_140m
N=288
fluentbench.pl -ssh -noloadchk -casdat=$modelname -t$N -cnf=machinefile -mpi=intel
```

Intel is the prefered MPI for ANSYS Fluent on OCI. 

# Benchmark Example

Performances of Fluent are often measured using the Formula 1 benchmark with 140 Millions cells. The next graph is showing how using more nodes impact the runtime, with a scaling really close to 100%. RDMA network only start to differentiate versus regular TCP runs if the Cells / Core ratio starts to go down. Here is a comparison with AWS C5n HPC machines. 

<p align="center">
 
<img src="https://github.com/oci-hpc/oci-hpc-runbook-fluent/blob/master/fluent_bench.png" height="500" >
 </p>
