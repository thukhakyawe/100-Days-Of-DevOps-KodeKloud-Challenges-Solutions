🧭 Part 1: Lab Step-by-Step Guidelines

📁 Directory Structure
terraform/
│
├── main.tf
├── variables.tf
├── outputs.tf
├── dev.tfvars
├── prod.tfvars
│
└── modules/
    ├── network/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf
    │
    └── compute/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf

Step 1 — Create Workspaces
terraform init
terraform workspace new dev
terraform workspace new prod


Switch when needed:

terraform workspace select dev

==============================
ROOT MODULE
==============================
📄 root/variables.tf

```
variable "KKE_VPC_CIDR" {
  type    = string
  default = "10.0.0.0/16"
}

variable "KKE_INSTANCE_TYPE" {
  type = string

  validation {
    condition     = contains(["t3.micro", "t3.large"], var.KKE_INSTANCE_TYPE)
    error_message = "Instance type must be either t3.micro or t3.large."
  }
}
```

📄 root/main.tf

```
# -----------------------------
# Locals
# -----------------------------
locals {
  name_prefix = "xfusion-${terraform.workspace}"

  default_tags = {
    Project     = "xfusion"
    Environment = terraform.workspace
  }
}

# -----------------------------
# Network Module
# -----------------------------
module "network" {
  source = "./modules/network"

  KKE_NAME_PREFIX = local.name_prefix
  KKE_VPC_CIDR    = var.KKE_VPC_CIDR
  KKE_TAGS        = local.default_tags
}

# -----------------------------
# Compute Module
# -----------------------------
module "compute" {
  source = "./modules/compute"

  KKE_NAME_PREFIX   = local.name_prefix
  KKE_SUBNET_ID     = module.network.kke_subnet_id
  KKE_INSTANCE_TYPE = var.KKE_INSTANCE_TYPE
  KKE_TAGS          = local.default_tags
}
```


📄 root/outputs.tf

```
output "kke_vpc_name" {
  value = module.network.kke_vpc_name
}

output "kke_subnet_name" {
  value = module.network.kke_subnet_name
}

output "kke_instance_name" {
  value = module.compute.kke_instance_name
}
```

📄 root/dev.tfvars

```
KKE_INSTANCE_TYPE = "t3.micro"
```

📄 root/prod.tfvars

```
KKE_INSTANCE_TYPE = "t3.large"
```

==============================
NETWORK MODULE
==============================
📄 modules/network/variables.tf

```
variable "KKE_NAME_PREFIX" {
  type = string
}

variable "KKE_VPC_CIDR" {
  type = string
}

variable "KKE_TAGS" {
  type = map(string)
}
```

📄 modules/network/main.tf

```
resource "aws_vpc" "vpc" {
  cidr_block = var.KKE_VPC_CIDR

  tags = merge(
    var.KKE_TAGS,
    { Name = "${var.KKE_NAME_PREFIX}-vpc" }
  )
}

resource "aws_subnet" "subnet" {
  vpc_id     = aws_vpc.vpc.id
  cidr_block = "10.0.1.0/24"

  tags = merge(
    var.KKE_TAGS,
    { Name = "${var.KKE_NAME_PREFIX}-subnet" }
  )
}
```

📄 modules/network/outputs.tf

```
output "kke_vpc_name" {
  value = aws_vpc.vpc.tags["Name"]
}

output "kke_subnet_name" {
  value = aws_subnet.subnet.tags["Name"]
}

output "kke_subnet_id" {
  value = aws_subnet.subnet.id
}
```
==============================
COMPUTE MODULE
==============================
📄 modules/compute/variables.tf

```
variable "KKE_NAME_PREFIX" {
  type = string
}

variable "KKE_SUBNET_ID" {
  type = string
}

variable "KKE_INSTANCE_TYPE" {
  type = string
}

variable "KKE_TAGS" {
  type = map(string)
}
```

📄 modules/compute/main.tf

```
resource "aws_instance" "instance" {
  ami           = "ami-0c94855ba95c71c99"
  instance_type = var.KKE_INSTANCE_TYPE
  subnet_id     = var.KKE_SUBNET_ID

  tags = merge(
    var.KKE_TAGS,
    { Name = "${var.KKE_NAME_PREFIX}-instance" }
  )
}
```

📄 modules/compute/outputs.tf

```
output "kke_instance_name" {
  value = aws_instance.instance.tags["Name"]
}
```

🚀 Deploying
DEV

```
terraform workspace select dev
terraform validate
terraform apply -var-file=dev.tfvars -auto-approve
```

Output

```
bob@iac-server ~/terraform via 💠 dev ➜  terraform apply -var-file=dev.tfvars -auto-approve

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # module.compute.aws_instance.instance will be created
  + resource "aws_instance" "instance" {
      + ami                                  = "ami-0c94855ba95c71c99"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      + disable_api_stop                     = (known after apply)
      + disable_api_termination              = (known after apply)
      + ebs_optimized                        = (known after apply)
      + enable_primary_ipv6                  = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      + host_resource_group_arn              = (known after apply)
      + iam_instance_profile                 = (known after apply)
      + id                                   = (known after apply)
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_lifecycle                   = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t3.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = (known after apply)
      + monitoring                           = (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      + placement_partition_number           = (known after apply)
      + primary_network_interface_id         = (known after apply)
      + private_dns                          = (known after apply)
      + private_ip                           = (known after apply)
      + public_dns                           = (known after apply)
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + spot_instance_request_id             = (known after apply)
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Environment" = "dev"
          + "Name"        = "xfusion-dev-instance"
          + "Project"     = "xfusion"
        }
      + tags_all                             = {
          + "Environment" = "dev"
          + "Name"        = "xfusion-dev-instance"
          + "Project"     = "xfusion"
        }
      + tenancy                              = (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = (known after apply)

      + capacity_reservation_specification (known after apply)

      + cpu_options (known after apply)

      + ebs_block_device (known after apply)

      + enclave_options (known after apply)

      + ephemeral_block_device (known after apply)

      + instance_market_options (known after apply)

      + maintenance_options (known after apply)

      + metadata_options (known after apply)

      + network_interface (known after apply)

      + private_dns_name_options (known after apply)

      + root_block_device (known after apply)
    }

  # module.network.aws_subnet.subnet will be created
  + resource "aws_subnet" "subnet" {
      + arn                                            = (known after apply)
      + assign_ipv6_address_on_creation                = false
      + availability_zone                              = (known after apply)
      + availability_zone_id                           = (known after apply)
      + cidr_block                                     = "10.0.1.0/24"
      + enable_dns64                                   = false
      + enable_resource_name_dns_a_record_on_launch    = false
      + enable_resource_name_dns_aaaa_record_on_launch = false
      + id                                             = (known after apply)
      + ipv6_cidr_block_association_id                 = (known after apply)
      + ipv6_native                                    = false
      + map_public_ip_on_launch                        = false
      + owner_id                                       = (known after apply)
      + private_dns_hostname_type_on_launch            = (known after apply)
      + tags                                           = {
          + "Environment" = "dev"
          + "Name"        = "xfusion-dev-subnet"
          + "Project"     = "xfusion"
        }
      + tags_all                                       = {
          + "Environment" = "dev"
          + "Name"        = "xfusion-dev-subnet"
          + "Project"     = "xfusion"
        }
      + vpc_id                                         = (known after apply)
    }

  # module.network.aws_vpc.vpc will be created
  + resource "aws_vpc" "vpc" {
      + arn                                  = (known after apply)
      + cidr_block                           = "10.0.0.0/16"
      + default_network_acl_id               = (known after apply)
      + default_route_table_id               = (known after apply)
      + default_security_group_id            = (known after apply)
      + dhcp_options_id                      = (known after apply)
      + enable_dns_hostnames                 = (known after apply)
      + enable_dns_support                   = true
      + enable_network_address_usage_metrics = (known after apply)
      + id                                   = (known after apply)
      + instance_tenancy                     = "default"
      + ipv6_association_id                  = (known after apply)
      + ipv6_cidr_block                      = (known after apply)
      + ipv6_cidr_block_network_border_group = (known after apply)
      + main_route_table_id                  = (known after apply)
      + owner_id                             = (known after apply)
      + tags                                 = {
          + "Environment" = "dev"
          + "Name"        = "xfusion-dev-vpc"
          + "Project"     = "xfusion"
        }
      + tags_all                             = {
          + "Environment" = "dev"
          + "Name"        = "xfusion-dev-vpc"
          + "Project"     = "xfusion"
        }
    }

Plan: 3 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_instance_name = "xfusion-dev-instance"
  + kke_subnet_name   = "xfusion-dev-subnet"
  + kke_vpc_name      = "xfusion-dev-vpc"
module.network.aws_vpc.vpc: Creating...
module.network.aws_vpc.vpc: Creation complete after 1s [id=vpc-ba647e16f78cbc076]
module.network.aws_subnet.subnet: Creating...
module.network.aws_subnet.subnet: Creation complete after 0s [id=subnet-dd567a944740d7ba2]
module.compute.aws_instance.instance: Creating...
module.compute.aws_instance.instance: Still creating... [10s elapsed]
module.compute.aws_instance.instance: Creation complete after 11s [id=i-14d793ed9f694bd4c]

Apply complete! Resources: 3 added, 0 changed, 0 destroyed.

Outputs:

kke_instance_name = "xfusion-dev-instance"
kke_subnet_name = "xfusion-dev-subnet"
kke_vpc_name = "xfusion-dev-vpc"
```


PROD

```
terraform workspace select prod
terraform apply -var-file=prod.tfvars -auto-approve
```

Output

```
bob@iac-server ~/terraform via 💠 prod ➜  terraform apply -var-file=prod.tfvars -auto-approve

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # module.compute.aws_instance.instance will be created
  + resource "aws_instance" "instance" {
      + ami                                  = "ami-0c94855ba95c71c99"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      + disable_api_stop                     = (known after apply)
      + disable_api_termination              = (known after apply)
      + ebs_optimized                        = (known after apply)
      + enable_primary_ipv6                  = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      + host_resource_group_arn              = (known after apply)
      + iam_instance_profile                 = (known after apply)
      + id                                   = (known after apply)
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_lifecycle                   = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t3.large"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = (known after apply)
      + monitoring                           = (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      + placement_partition_number           = (known after apply)
      + primary_network_interface_id         = (known after apply)
      + private_dns                          = (known after apply)
      + private_ip                           = (known after apply)
      + public_dns                           = (known after apply)
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + spot_instance_request_id             = (known after apply)
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Environment" = "prod"
          + "Name"        = "xfusion-prod-instance"
          + "Project"     = "xfusion"
        }
      + tags_all                             = {
          + "Environment" = "prod"
          + "Name"        = "xfusion-prod-instance"
          + "Project"     = "xfusion"
        }
      + tenancy                              = (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = (known after apply)

      + capacity_reservation_specification (known after apply)

      + cpu_options (known after apply)

      + ebs_block_device (known after apply)

      + enclave_options (known after apply)

      + ephemeral_block_device (known after apply)

      + instance_market_options (known after apply)

      + maintenance_options (known after apply)

      + metadata_options (known after apply)

      + network_interface (known after apply)

      + private_dns_name_options (known after apply)

      + root_block_device (known after apply)
    }

  # module.network.aws_subnet.subnet will be created
  + resource "aws_subnet" "subnet" {
      + arn                                            = (known after apply)
      + assign_ipv6_address_on_creation                = false
      + availability_zone                              = (known after apply)
      + availability_zone_id                           = (known after apply)
      + cidr_block                                     = "10.0.1.0/24"
      + enable_dns64                                   = false
      + enable_resource_name_dns_a_record_on_launch    = false
      + enable_resource_name_dns_aaaa_record_on_launch = false
      + id                                             = (known after apply)
      + ipv6_cidr_block_association_id                 = (known after apply)
      + ipv6_native                                    = false
      + map_public_ip_on_launch                        = false
      + owner_id                                       = (known after apply)
      + private_dns_hostname_type_on_launch            = (known after apply)
      + tags                                           = {
          + "Environment" = "prod"
          + "Name"        = "xfusion-prod-subnet"
          + "Project"     = "xfusion"
        }
      + tags_all                                       = {
          + "Environment" = "prod"
          + "Name"        = "xfusion-prod-subnet"
          + "Project"     = "xfusion"
        }
      + vpc_id                                         = (known after apply)
    }

  # module.network.aws_vpc.vpc will be created
  + resource "aws_vpc" "vpc" {
      + arn                                  = (known after apply)
      + cidr_block                           = "10.0.0.0/16"
      + default_network_acl_id               = (known after apply)
      + default_route_table_id               = (known after apply)
      + default_security_group_id            = (known after apply)
      + dhcp_options_id                      = (known after apply)
      + enable_dns_hostnames                 = (known after apply)
      + enable_dns_support                   = true
      + enable_network_address_usage_metrics = (known after apply)
      + id                                   = (known after apply)
      + instance_tenancy                     = "default"
      + ipv6_association_id                  = (known after apply)
      + ipv6_cidr_block                      = (known after apply)
      + ipv6_cidr_block_network_border_group = (known after apply)
      + main_route_table_id                  = (known after apply)
      + owner_id                             = (known after apply)
      + tags                                 = {
          + "Environment" = "prod"
          + "Name"        = "xfusion-prod-vpc"
          + "Project"     = "xfusion"
        }
      + tags_all                             = {
          + "Environment" = "prod"
          + "Name"        = "xfusion-prod-vpc"
          + "Project"     = "xfusion"
        }
    }

Plan: 3 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_instance_name = "xfusion-prod-instance"
  + kke_subnet_name   = "xfusion-prod-subnet"
  + kke_vpc_name      = "xfusion-prod-vpc"
module.network.aws_vpc.vpc: Creating...
module.network.aws_vpc.vpc: Creation complete after 0s [id=vpc-44bbd807232b1fc09]
module.network.aws_subnet.subnet: Creating...
module.network.aws_subnet.subnet: Creation complete after 0s [id=subnet-9888cf1d4460a2e7d]
module.compute.aws_instance.instance: Creating...
module.compute.aws_instance.instance: Still creating... [10s elapsed]
module.compute.aws_instance.instance: Creation complete after 10s [id=i-788d5fba6f79d55ad]

Apply complete! Resources: 3 added, 0 changed, 0 destroyed.

Outputs:

kke_instance_name = "xfusion-prod-instance"
kke_subnet_name = "xfusion-prod-subnet"
kke_vpc_name = "xfusion-prod-vpc"
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
What We Built

We built an infrastructure system that changes based on environment.

Same code.
Different workspace.
Different results.

How Workspaces Help

When you run:

terraform workspace select dev


Terraform automatically changes:

xfusion-dev


When you switch to prod:

xfusion-prod


No code changes required.

Why We Used Modules

Instead of putting everything in one file:

Network logic lives in the network module.

Compute logic lives in the compute module.

This keeps infrastructure clean and reusable.

How Tag Merging Works

We defined common tags once:

Project = xfusion
Environment = dev or prod


Each module merges:

Common tags + Name tag


So every resource is consistently tagged.

Why Validation Is Important

If someone tries:

KKE_INSTANCE_TYPE = "m5.large"


Terraform will fail immediately.

This prevents invalid production deployments.

---