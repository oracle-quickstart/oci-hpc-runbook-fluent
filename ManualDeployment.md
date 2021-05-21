# <img src="https://github.com/oci-hpc/oci-hpc-runbook-fluent/blob/master/fluent_logo.png" height="60"> ANSYS Fluent Runbook

## Table of Contents
- [Launch Cluster Network Steps](#launch-cluster-network-steps)
- [Creation of Cluster Network through Marketplace](#creation-of-cluster-network-through-marketplace)
- [Creation of Cluster Network through Manual Configuration](#creation-of-cluster-network-through-manual-configuration)

## Launch Cluster Network Steps
There are many ways to launch an HPC Cluster Network, this solutions guide will cover two different methods:

Via Marketplace
Manually Depending on your OS, you will want to go with a specific method. If the HPC Cluster Network marketplace image or our OCI HPC CN Terraform scripts are used, this is for Oracle Linux 7 only. If you want to use CentOS, Ubuntu or another OS, manual configuration is required.

### Creation of Cluster Network through Marketplace
Marketplace holds applications and images that can be deployed with our infrastructure. For customers that want to use Oracle Linux, an HPC Cluster Network image is available and can be launched from directly within marketplace. We suggest launching the [CFD Ready Cluster](https://cloudmarketplace.oracle.com/marketplace/en_US/listing/75645211) that will contain librairies needed for CFD.

1. Within marketplace, select <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/get_app.png" height="30"> at the top right.
2. <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/region.png" height="40"> then click <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/sign_in.png" height="30">.
3. Verify the version of the HPC Cluster image and then select the Compartment where the cluster will be launched. Accept the terms and conditions, then Launch Stack.
4. Fill out the remaing details of the stack:
	- i. Select the desired <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/availability_domain.png" height="47"> for the compute shapes and the bastion.
	- ii. Copy-paste your <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/ssh_key.png" height="41">
	- iii. Type in the <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/compute_instances.png" height="39"> for the cluster
	- iv. Uncheck Install OpenFOAM
	- v. If you need more than 6TB of Shared disk space, check GlusterFS and select how many servers you would need. (6TB per server)
5. Click <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/create.png" height="30">.
6. Navigate to <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/tf_actions.png" height="24"> then click Apply. This will launch the CN provisioning.
7. Wait until the job shows <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/succeeded.png" height="38"> then navigate to <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/outputs.png" height="34"> to obtain the bastion and compute node private IP’s <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/bastion_private_ip.png" height="37">.

### Creation of Cluster Network through Manual Configuration
Marketplace holds applications and images that can be deployed with our infrastructure. For customers that want to use Oracle Linux, you can manually create a cluster network as follows:
1. Select the OCI Region <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/region_console.png" height="29"> on the top right.
2. In the main menu, select <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/networking.png" height="28"> and <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/vcn.png" height="30">
3. Click on <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/start_vcn_wizard.png" height="32">, and select <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/vcn_with_internet_connectivity.png" height="38">
4. Choose a name, the right compartment, and use 172.16.0.0/16 as VCN CIDR, 172.16.0.0/24 for Public Subnet and 172.16.1.0/24 for Private Subnet
5. In the main menu, select <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/compute.png" height="31">, <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/instances.png" height="29">, then <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/create_instances.png" height="31">
6. Change the Image <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/change_image.png" height="30"> and select the Oracle Image tab <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/oracle_images.png" height="38">, select <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/oracle_linux_7.png" height="32">
7. Select the Availability Domain <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/ad.png" height="50"> in which you can spin up a BM.HPC2.36 instance
8. Change the shape to <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/shape.png" height="60"> under Bare Metal and Specialty
9. Select the VCN and the public subnet you created.
10. Add a public key <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/paste_public_keys.png" height="30"> to connect to the instance. This key will be used on all compute instances.
11. Once the machine is up, click on the created instance. Under <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/more_actions.png" height="30">, select <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/create_instance_configuration.png" height="25">. You can now <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/terminate.png" height="28"> the instance under More Actions.
12. In the main menu, select <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/compute.png" height="25">, then <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/cluster_networks.png" height="25">
13. Click <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/create_cluster_network.png" height="25"> and fill in all the options. Use the VCN, private subnet and instance configuration that you just created. Select the AD in which you can launch BM.HPC2.36 instances.
14. Launch the cluster network.
15. While it is loading, create another instance under <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/main_menu.png" height="25">, <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/compute.png" height="25"> and <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/instances.png" height="25">.
16. Put it in the public subnet that was just created, using your public key and shape should be VM.Standard2.1 or similar. This will be the bastion that we will use to connect to the cluster.
17. SCP the key to the cluster on the bastion at /home/opc/.ssh/cluster_key and copy it also to /home/opc/.ssh/id_rsa
18. Install the Provisioning Tool on the bastion via the following command:
``` 
sudo rpm -Uvh https://objectstorage.us-ashburn-1.oraclecloud.com/p/aOr79eUYz834A23on3Vbe8S9W4gyi1bsuOL0T7dlxrkkXlmkUTMqasJYpfRMTAnu/n/hpc/b/rpms/o/common/oci-hpc-provision-20190905-63.7.2.x86_64.rpm 
```
19. Navigate to <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/compute.png" height="25"> then <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/instance_pools.png" height="25"> in the Console and collect all the IP addresses for the cluster network pool. Or use this command on the bastion if you have nothing else running on your private subnet.
``` 
for i in `nmap -sL Private_Subnet_CIDR | grep "Nmap scan report for" | grep ")" | awk '{print $6}'`;do echo ${i:1:-1} >> /tmp/ips; done 
```
20. Install the Provisioning Tool via the following command:
``` 
ips=`cat /tmp/ips`
/opt/oci-hpc/setup/provision/HPC_PROVISION/hpc_provision_cluster_nodes.sh -p -i /home/opc/.ssh/id_rsa $ips 
```
## Access your Cluster
The public IP address of the bastion can be found on the lower left menu under <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/outputs.png" height="35">. If you navigate to your instances in the main menu, you will also find your bastion instance as well as the public IP.

The Private Key to access the machines can also be found there. Copy the text in a file on your machine, let's say/home/user/key:
```
chmod 600 /home/user/key 
ssh -i /home/user/key opc@ipaddress 
```
## Configure Visualization
HPC workloads often require visualization tools for scheduling, monitoring or analyzing the output of the simulations. In these scenarios, it is often desired to create a GPU visualization node for optimal resolution and post processing. A GUI is not installed by default on OCI instances; however, one can be configured easily using VNC or X11 remote display protocol. The subsections below will walk through how to create a GPU visualization node in the public subnet using TurboVNC and OpenGL.

### Setting Up a VNC on your bastion
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
### Add a GPU instance.
The below steps are taken Using OpenGL to Enhance GPU Use cases on OCI - refer to the blog for more details.
1. Within the Console, navigate to <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/compute.png" height="25"> then <img src="https://github.com/oracle-quickstart/oci-hpc-runbook-fluent/blob/main/images/instances.png" height="25">.
2. Create a Compute Instance for the Visualization Node: a. Select the desired AD b. Select the desired GPU shape (either VM or BM) c. Specify a GPU-compatible Oracle Linux image The latest Oracle Linux Image will automatically be GPU enabled. d. Select the Cluster Network VCN and Public Subnet e. Copy-paste your public ssh key f. Click Create.
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
