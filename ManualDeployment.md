# <img src="https://github.com/oci-hpc/oci-hpc-runbook-fluent/blob/master/fluent_logo.png" height="60"> ANSYS Fluent Runbook

### Table of Contents
- [Launch Cluster Network Steps](#launch-cluster-network-steps)
- [Creation of Cluster Network through Marketplace](#creation-of-cluster-network-through-marketplace)
- [Creation of Cluster Network through Manual Configuration](#creation-of-cluster-network-through-manual-configuration)

## Launch Cluster Network Steps
There are many ways to launch an HPC Cluster Network, this solutions guide will cover two different methods:

Via Marketplace
Manually Depending on your OS, you will want to go with a specific method. If the HPC Cluster Network marketplace image or our OCI HPC CN Terraform scripts are used, this is for Oracle Linux 7 only. If you want to use CentOS, Ubuntu or another OS, manual configuration is required.

## Creation of Cluster Network through Marketplace
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

## Creation of Cluster Network through Manual Configuration
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
11. Once the machine is up, click on the created instance. Under More Actions, select Create Instance Configuration. You can now terminate the instance under More Actions.

