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

## Step 3. Prepare the model, as follows:
1. Create a directory in the work folder and then move the cas_dat folder contents into this newly created folder. 
   Example:
   ```
   mkdir f1_racecar_140m
   mv bench/fluent/v6/f1_racecar_140m/cas_dat/* f1_racecar_140m/
   ```
2. Gunzip the f1_racecar_140m folder and remove the bench folder.
   ```
   gunzip f1_racecar_140m/*
   rm -rf bench/
   ```
## Step 4.Launch an HPC cluster from marketplace, and install ansys / models from object storage par’s.
<details>
  <summary>If you use the CFD Ready Cluster, it already comes preinstalled with prerequisite libraries:
</summary>
•	libGLU 
 
•	libXrender.x86_64 
 
•	libXtst.x86_64 
 
•	motif-2.3.4-14.el7_5.x86_64 
 
•	mesa-libGLU-9.0.0-4.el7.x86_64  
 
•	mesa-libGLU 
 
•	mesa-libGL 
 
•	motif 
 
•	axel 
 
•	fontconfig 
 
•	freetype 
 
•	freetype-devel 
 
•	fontconfig-devel 
 
•	libXext 
 
•	libXrender-devel.x86_64 
 
•	libXrender.x86_64 
 
•	mesa-libGL.x86_64
 
</details>

If you don’t have the pre-requisites installed, you can also run the following script from the bastion:
https://github.com/oci-hpc/oci-hpc-clusternetwork/blob/CFD_ansible/playbooks/cfd.yml via
 ```
 ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook /home/opc/playbooks/cfd.yml
 ```
## Step 5. Launch a silent install via the following command, using the IP of your license server and 2325 / 1055 are the ANSYS License Interconnect and FlexNet ports, respectively.
 ```
 ./INSTALL -silent -install_dir "/nfs/scratch/fluent" -fluent -licserverinfo 2325:1055:129.146.96.65
 ```
## Step 6. If you want to add Ansys Fluent to your environmental variables, run the following commands:![image](https://user-images.githubusercontent.com/31706671/122606400-ded94080-d046-11eb-973c-af5c2f4867e2.png)

```
echo export PATH=/nfs/scratch/fluent/v202/fluent/bin/:$PATH | sudo tee -a ~/.bashrc
```
```
source ~/.bashrc
```
```
- hosts: compute
  tasks:
  - name: fluent install
    lineinfile:
      path: /home/opc/.bashrc
      line: export PATH=/nfs/scratch/fluent/v202/fluent/bin/:$PATH
      create: yes
```

## Step 7. Create a machine file in either /nfs/scratch or /mnt-nfs-share
```
sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
```

## Step 8. To set your own flags, modify each mpirun.fl file in the fluent folder with your own flags.
1. Replace: 
```
FS_MPIRUN_FLAGS="$FS_MPIRUN_FLAGS -genv I_MPI_ADJUST_REDUCE 2  -genv I_MPI_ADJUST_ALLREDUCE 2 -genv I_MPI_ADJUST_BCAST 1"
```
To: 
```
FS_MPIRUN_FLAGS="$FLUENT_INTEL_MPIRUN_FLAGS -genv I_MPI_ADJUST_REDUCE 2 -genv I_MPI_ADJUST_ALLREDUCE 2 -genv I_MPI_ADJUST_BCAST 1"
```
2. Export the environmental variables flag
```
echo export FLUENT_INTEL_MPIRUN_FLAGS="<<< your flags >>>"
```
Example using Intel 2018 Flags: 
```
echo export FLUENT_INTEL_MPIRUN_FLAGS='"-iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0 -genv I_MPI_PIN_PROCESSOR_LIST=0-35 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71"' | sudo tee -a ~/.bashrc
```
<details>
  <summary>Refer to the runbook and blog for more flags and guidance: 
</summary>
https://blogs.oracle.com/cloud-infrastructure/running-applications-on-oracle-cloud-using-cluster-networking
</details>

## Step 9: If setting the flags in step 8 is too annoying, you can also use -mpiopt flag when you run fluent to set the flags.
```
-mpiopt="-iface enp94s0f0 -genv I_MPI_FABRICS shm:dapl -genv DAT_OVERRIDE /etc/dat.conf -genv I_MPI_DAT_LIBRARY /usr/lib64/libdat2.so -genv I_MPI_DAPL_PROVIDER ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK 0 -genv I_MPI_FALLBACK=0"![image](https://user-images.githubusercontent.com/31706671/122606828-92423500-d047-11eb-9d2b-da4167a19928.png)
```

## Step 10: Within the work folder, set the following variables and run fluent on the desired number of cores. 
Note that this script uses Intel MPI – it is recommended to use Intel MPI for Ansys Fluent on OCI HPC.
```
modelname=f1_racecar_140m
N=<number of cores>
```
```
fluentbench.pl -ssh -noloadchk -casdat=$modelname -t$N -cnf=machinefile -mpi=intel -mpiopt="-iface enp94s0f0 -genv I_MPI_FABRICS shm:dapl -genv DAT_OVERRIDE /etc/dat.conf -genv I_MPI_DAT_LIBRARY /usr/lib64/libdat2.so -genv I_MPI_DAPL_PROVIDER ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK 0 -genv I_MPI_FALLBACK=0"![image]
```
Note: You can kill stopped jobs via kill -9 $(jobs -p)

## Step 11: The output of the simulation produces the following files:
a.	.out file – shows the output of the run, including the solver rating, number of seconds per iteration and speed
b.	.log file – shows the history of the benchmark run
c.	.trn – shows the transcript of the run

You can plot the solver rating, speed and calculate the efficiency at each core count you run, and then plot to determine the optimal parameters to run at.


# Benchmark Example

Performances of Fluent are often measured using the Formula 1 benchmark with 140 Millions cells. The next graph is showing how using more nodes impact the runtime, with a scaling really close to 100%. RDMA network only start to differentiate versus regular TCP runs if the Cells / Core ratio starts to go down. Here is a comparison with AWS C5n HPC machines. 

<p align="center">
 
<img src="https://github.com/oci-hpc/oci-hpc-runbook-fluent/blob/master/fluent_bench.png" height="500" >
 </p>
