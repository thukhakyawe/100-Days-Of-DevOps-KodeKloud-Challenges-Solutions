To ensure proper resource provisioning order, the DevOps team wants to explicitly define the dependency between an AWS VPC and a Subnet. The objective is to create a VPC and then a Subnet that explicitly depends on it using Terraform's depends_on argument.

Please complete the following tasks:

Create a VPC named xfusion-vpc.

Create a Subnet named xfusion-subnet.

Ensure the Subnet uses the depends_on argument to explicitly depend on the VPC resource.

Create the main.tf file (do not create a separate .tf file) to provision a VPC and Subnet.

Use variables.tf, define the following variables:

KKE_VPC_NAME for the VPC name.
KKE_SUBNET_NAME for the Subnet name.
Use terraform.tfvars to input the names of the VPC and subnet.

In outputs.tf, output the following:

kke_vpc_name: The name of the VPC.
kke_subnet_name: The name of the Subnet.