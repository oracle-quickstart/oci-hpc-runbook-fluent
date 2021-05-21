# <img src="https://github.com/oci-hpc/oci-hpc-runbook-fluent/blob/master/fluent_logo.png" height="60"> ANSYS Fluent Runbook

# Table of Contents
- [Launch Cluster Network Steps](#launch-cluster-network-steps)
- [Creation of Cluster Network through Marketplace](#creation-of-cluster-network-through-marketplace)
- [Creation of Cluster Network through Manual Configuration](#creation-of-cluster-network-through-manual-configuration)
- [Access your Cluster](#access-your-cluster)
- [Configure Visualization](#configure-visualization)
- [Setting Up a VNC on your bastion](#setting-up-a-vnc-on-your-bastion)
- [Add a GPU instance](#add-a-gpu-instance)
- [Accessing a VNC](#accessing-a-vnc)
- [Installing Fluent](#installing-fluent)
- [Adding specific libraries](#adding-specific-libraries)
- [Download the binaries](#download-the-binaries)
- [Install](#install)
- [Running Fluent](#running-fluent)
- [Benchmark Example](#benchmark-example)

# Launch Cluster Network Steps
There are many ways to launch an HPC Cluster Network, this solutions guide will cover two different methods:

Via Marketplace
Manually Depending on your OS, you will want to go with a specific method. If the HPC Cluster Network marketplace image or our OCI HPC CN Terraform scripts are used, this is for Oracle Linux 7 only. If you want to use CentOS, Ubuntu or another OS, manual configuration is required.

## Creation of Cluster Network through Marketplace
Marketplace holds applications and images that can be deployed with our infrastructure. For customers that want to use Oracle Linux, an HPC Cluster Network image is available and can be launched from directly within marketplace. We suggest launching the [CFD Ready Cluster](https://cloudmarketplace.oracle.com/marketplace/en_US/listing/75645211) that will contain librairies needed for CFD.

1. Within marketplace, select **Get App** at the top right.
<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/marketplace_get_app.png" height="150">
2. Select the OCI Region then click **Sign In**.
<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/compartments_signin.png" height="250">
3. Verify the version of the HPC Cluster image and then select the Compartment where the cluster will be launched. Accept the terms and conditions, then Launch Stack.

4. Fill out the remaing details of the stack:
	- i. Select the desired <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/availability_domain.png" height="56"> for 	the compute shapes and the bastion.
	- ii. Copy-paste your <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/ssh_key.png" height="50">
	- iii. Type in the <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/compute_instances.png" height="46"> for the 		cluster
	- iv. Uncheck Install OpenFOAM
	- v. If you need more than 6TB of Shared disk space, check GlusterFS and select how many servers you would need. (6TB per server)
5. Click <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/create.png" height="35">.
6. Navigate to <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/tf_actions.png" height="28"> then click Apply. This will launch the CN provisioning.
7. Wait until the job shows <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/succeeded.png" height="42"> then navigate to <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/outputs.png" height="38"> to obtain the bastion and compute node private IP’s <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/bastion_private_ip.png" height="50">.

## Creation of Cluster Network through Manual Configuration
Marketplace holds applications and images that can be deployed with our infrastructure. For customers that want to use Oracle Linux, you can manually create a cluster network as follows:
1. Select the OCI Region on the top right.

<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/region3.png" height="150">

2. In the main menu, select **Networking** and **Virtual Cloud Network**

<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/vcn2.png" height="200"> 

3. Click on <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/start_vcn_wizard.png" height="32">, and select **VCN with Internet Connectivity**.
<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/vcn_wizard.png" height="270">

4. Choose a name, the right compartment, and use 172.16.0.0/16 as VCN CIDR, 172.16.0.0/24 for Public Subnet and 172.16.1.0/24 for Private Subnet
5. In the main menu, select **Compute, Instances, then Create Instance**.

<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/compute_instances_console.png" height="180">.

6. Change the Image and select the **Oracle Image** tab, select **Oracle Linux 7 - HPC Cluster Networking Image**.

<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/oracle_linux_7.png" height="45">

7. Select the **Availability Domain** in which you can spin up a BM.HPC2.36 instance

<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/ad.png" height="70">

8. Change the **shape** to BM.HPC2.36 under Bare Metal and Specialty

<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/shape.png" height="80">

9. Select the VCN and the public subnet you created.
10. Add a public key to connect to the instance. This key will be used on all compute instances.

<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/key.png" height="150">

11. Once the machine is up, click on the created instance. Under **More Actions**, select **Create Instance Configuration**. You can now **terminate** the instance under **More Actions**.

<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/machine_instance.png" height="280">

12. In the main menu, select **Compute**, then **Cluster Networks**

<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/cluster_networks_console.png" height="210">

13. Click **Create Cluster Network** and fill in all the options. Use the VCN, private subnet and instance configuration that you just created. Select the AD in which you can launch BM.HPC2.36 instances.

<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/create_cluster.png" height="170">

14. Launch the cluster network.

15. While it is loading, create another instance under **Main Menu**, **Compute** and **Instances**.

<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/compute_instances_console.png" height="170">

16. Put it in the public subnet that was just created, using your public key and shape should be VM.Standard2.1 or similar. This will be the bastion that we will use to connect to the cluster.
17. SCP the key to the cluster on the bastion at /home/opc/.ssh/cluster_key and copy it also to /home/opc/.ssh/id_rsa
18. Install the Provisioning Tool on the bastion via the following command:
``` 
sudo rpm -Uvh https://objectstorage.us-ashburn-1.oraclecloud.com/p/aOr79eUYz834A23on3Vbe8S9W4gyi1bsuOL0T7dlxrkkXlmkUTMqasJYpfRMTAnu/n/hpc/b/rpms/o/common/oci-hpc-provision-20190905-63.7.2.x86_64.rpm 
```
19. Navigate to **Compute** then **Instance Pools** in the Console and collect all the IP addresses for the cluster network pool. Or use this command on the bastion if you have nothing else running on your private subnet.
 
<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/compute_instance_pools.png" height="180">

``` 
for i in `nmap -sL Private_Subnet_CIDR | grep "Nmap scan report for" | grep ")" | awk '{print $6}'`;do echo ${i:1:-1} >> /tmp/ips; done 
```
20. Install the Provisioning Tool via the following command:
``` 
ips=`cat /tmp/ips`
/opt/oci-hpc/setup/provision/HPC_PROVISION/hpc_provision_cluster_nodes.sh -p -i /home/opc/.ssh/id_rsa $ips 
```
# Access your Cluster
The public IP address of the bastion can be found on the lower left menu under <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/outputs.png" height="35">. If you navigate to your instances in the main menu, you will also find your bastion instance as well as the public IP.

The Private Key to access the machines can also be found there. Copy the text in a file on your machine, let's say/home/user/key:
```
chmod 600 /home/user/key 
ssh -i /home/user/key opc@ipaddress 
```
# Configure Visualization
HPC workloads often require visualization tools for scheduling, monitoring or analyzing the output of the simulations. In these scenarios, it is often desired to create a GPU visualization node for optimal resolution and post processing. A GUI is not installed by default on OCI instances; however, one can be configured easily using VNC or X11 remote display protocol. The subsections below will walk through how to create a GPU visualization node in the public subnet using TurboVNC and OpenGL.

## Setting Up a VNC on your bastion
By default, the only access to the Oracle Linux machine is through SSH in a console mode. If you want to see the graphical interface, you will need to set up a VNC connection. The following script will work for the default user opc. The password for the vnc session is set as "HPC_oci1" but it can be edited in the next set of commands. If you are not currently connected to the headnode via SSH, please do so as these commands need to be run on the headnode.
```
sudo yum -y groupinstall "Server with GUI"
sudo yum -y install tigervnc-server mesa-libGL
sudo systemctl set-default graphical.target
sudo cp /usr/lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:1.service
sudo sed -i 's/<USER>/opc/g' /etc/systemd/system/vncserver@:1.service
sudo sed -ie '/^ExecStart=/a PIDFile=/home/opc/.vnc/%H%i.pid' /etc/systemd/system/vncserver@:1.service
sudo mkdir /home/opc/.vnc/
sudo chown opc:opc /home/opc/.vnc
echo "password" | vncpasswd -f > /home/opc/.vnc/passwd
chown opc:opc /home/opc/.vnc/passwd
chmod 600 /home/opc/.vnc/passwd
sudo systemctl start vncserver@:1.service
sudo systemctl enable vncserver@:1.service
```
## Add a GPU instance
The below steps are taken Using OpenGL to Enhance GPU Use cases on OCI - refer to the blog for more details.
1. Within the Console, navigate to Compute then Instances.

<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/compute_instances_console.png" height="150">

2. Create a Compute Instance for the Visualization Node: 
- a. Select the desired AD
- b. Select the desired GPU shape (either VM or BM)
- c. Specify a GPU-compatible Oracle Linux image The latest Oracle Linux Image will automatically be GPU enabled.
- d. Select the Cluster Network VCN and Public Subnet 
- e. Copy-paste your public ssh key 
- f. Click Create.
3. Wait for the instance to provision then log into the instance via:
```
ssh opc@<public ip> -i <private key> 
```
4. Install X Window System, a display manager (GNOME/GDM), and a desktop environment (MATE):
```
sudo yum groupinstall "X Window System"
sudo yum install gdm
sudo yum groupinstall "MATE Desktop"
```
5. Install VNC server and VirtualGL. Note that VirtualGL is an open source toolkit that lets any Linux or Unix console run OpenGL applications with full hardware acceleration.
```
sudo yum install https://downloads.sourceforge.net/project/virtualgl/2.6.3/VirtualGL-2.6.3.x86_64.rpm
sudo yum install https://downloads.sourceforge.net/project/turbovnc/2.2.4/turbovnc-2.2.4.x86_64.rpm
```
6. Configure the X server to enable GPU sharing for virtual sessions. Run the following commands:
```
sudo nvidia-xconfig --use-display-device=none --busid="PCI:4:0:0"
```
7. Configure the X server to enable GPU sharing for virtual sessions. Run the following commands:
```
sudo vglserver_config -config -s -f -t
```
8. To avoid being locked out when the screen saver launches, set the local user password to something you can use later:
```
sudo passwd opc
```
9. Change your VNC password to something you can use for logging on:
```
vncpasswd
```
10. Restart the X Server:
```
kill $(pgrep Xvnc)
vncserver
```
11. Enable and Start GDM:
```
systemctl enable gdm --now
```
12. Launch the VNC server:
```
/opt/TurboVNC/bin/vncserver -wm mate-session
```
13. If you want to access the VNC server directly without SSH forwarding, ensure that your security list allows connections on port 5901/tcp.
- i. In the Console, navigate to Networking then Virtual Cloud Networks.
- ii. Select Subnets and then the public subnet.
- iii. In the default security list, add an Ingress Rule with the following details:
	- a. Stateless: No
	- b. Source Type: CIDR
	- c. Source CIDR: 0.0.0.0/0
	- d. IP Protocol: TCP
	- e. Source Port Range: All
	- f. Destination Port Range: 5901
	
Note: The standard VNC port is 5900 plus a display number (for example, 5901 for :1, 5902 for :2)

14. Allow access in local firewall settings, as follows:
```
sudo firewall-cmd --zone=public --permanent --add-port=5901/tcp
sudo firewall-cmd --reload
```
15. Open TurboVNC or TigerVNC client. Enter the IP address connection as :1

# Accessing a VNC
We will connect through an SSH tunnel to the instance. On your machine, connect using ssh PORT below will be the number that results from 5900 + N. N is the display number, if the output for N was 1, PORT is 5901, if the output was 9, PORT is 5909 public_ip is the public IP address of the headnode, which is running the VNC server. If you used the previous instructions, port will be 5901
```
ssh -L 5901:127.0.0.1:5901 opc@public_ip
```
You can now connect using any VNC viewer using localhost:N as VNC server and the password you set during the vnc installation. You can chose a VNC client that you prefer or use this guide to install on your local machine:
- [Windows - TigerVNC](https://github.com/TigerVNC/tigervnc/wiki/Setup-TigerVNC-server-%28Windows%29)
- [MacOS/Windows - RealVNC](https://www.realvnc.com/en/connect/download/) 

# Installing Fluent
## Adding specific libraries
**If you used the CFD Ready Cluster from marketplace, this step is not needed.**
There are a couple of library that need to be added to the Oracle Linux image on all the compute nodes.
```
sudo yum -y install libGLU libXrender.x86_64 libXtst.x86_64 motif-2.3.4-14.el7_5.x86_64 mesa-libGLU-9.0.0-4.el7.x86_64  mesa-libGLU mesa-libGL motif axel fontconfig freetype freetype-devel fontconfig-devel libXext libXrender-devel.x86_64 libXrender.x86_64 mesa-libGL.x86_64
```
## Download the binaries
You can download the FLUENT installer from the ANSYS website and push it to your machine using scp.
```
scp /path/own/machine/FLUID_version.zip opc@1.1.1.1:/home/opc/
```
Another possibility is to upload the installer into object storage.
1. In the main menu of the console, select Object Storage.
<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/object_storage.png" height="200">
2. Choose the correct region on the top right
<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/region3.png" height="140">
3. Select the correct compartment on the left-hand side
<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/compartments.png" height="120">
4. Create a bucket if you do not have one already created
<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/create_bucket.png" height="30">
5. In the bucket, select upload object and specify the path of the installer.
<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/objects2.png" height="80">
6. Select the 3 dots on the right-hand side of the installer object and select Create Pre-Authenticated Request
<img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/create_par.png" height="90">
7. If you lose the URL, you cannot get it back, but you can regenerate a new Pre-Authenticated Request

Download the installer form object storage with
```
wget PAR_URL
```
Untar or unzip the installer depending on your version
```
tar -xf installer.tgz
unzip installer.tgz
```
## Install
Launch the installer on a shared location. By default, an HPC cluster has a NFS-share or a Gluster-share mounted on all the compute nodes. Add the IP address to access the license server.
```
mkdir /mnt/nfs-share/install/fluent
sudo ./INSTALL -silent -install_dir "/mnt/nfs-share/install/fluent" -fluent -licserverinfo 2325:1055:IP
```
There is a known problem when running the ANSYS installer that it can hang. When you see ``` RSS is disabled ```, feel free to exit using CTRL-C

Finally, you can add fluent to your path like this
```
export PATH=$SHARE_DIR/applications/ansys_inc/v$VERS/fluent/bin:$PATH
echo export PATH=$SHARE_DIR/applications/ansys_inc/v$VERS/fluent/bin:'$PATH' | sudo tee /etc/profile.d/ansys.sh
```
# Running Fluent
Running Fluent is pretty straightforward: You can either start the GUI if you have a VNC session started with
```
/mnt/gluster-share/install/fluent/v190/fluent/bin/fluent
```
To specify the host you need to run on, you need to create a machinefile. You can generate it as follow, or manually. Format is hostname:corenumber.
```
sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
```
To run on multiple nodes, place the model on the share drive (Ex:/mnt/nfs-share/work/). Example provided here is to run any of the benchmark model provided on the ANSYS website. You can add it to object storage like the installer and download it or scp it to the machine.
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
<img src="https://github.com/oci-hpc/oci-hpc-runbook-fluent/blob/master/fluent_bench.png" height="500">
