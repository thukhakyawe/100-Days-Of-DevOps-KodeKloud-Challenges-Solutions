# Task 1


1️⃣ variables.tf

```
variable "KKE_TABLE_NAME" {
  type = string
}

variable "KKE_ROLE_NAME" {
  type = string
}

variable "KKE_POLICY_NAME" {
  type = string
}
```

2️⃣ terraform.tfvars

```
KKE_TABLE_NAME  = "devops-table-t1q4"
KKE_ROLE_NAME   = "devops-role-t1q4"
KKE_POLICY_NAME = "devops-readonly-policy-t1q4"
```

3️⃣ main.tf

```
# DynamoDB Table
resource "aws_dynamodb_table" "devops_table" {
  name         = var.KKE_TABLE_NAME
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "id"

  attribute {
    name = "id"
    type = "S"
  }
}

# IAM Role (trusted by AWS services)
resource "aws_iam_role" "devops_role" {
  name = var.KKE_ROLE_NAME

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect = "Allow"
      Principal = {
        Service = "ec2.amazonaws.com"
      }
      Action = "sts:AssumeRole"
    }]
  })
}

# IAM Policy (Read-only access to DynamoDB table)
resource "aws_iam_policy" "devops_policy" {
  name = var.KKE_POLICY_NAME

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect = "Allow"
      Action = [
        "dynamodb:GetItem",
        "dynamodb:Scan",
        "dynamodb:Query"
      ]
      Resource = aws_dynamodb_table.devops_table.arn
    }]
  })
}

# Attach Policy to Role
resource "aws_iam_role_policy_attachment" "attach_policy" {
  role       = aws_iam_role.devops_role.name
  policy_arn = aws_iam_policy.devops_policy.arn
}
```

4️⃣ outputs.tf

```
output "kke_dynamodb_table" {
  value = aws_dynamodb_table.devops_table.name
}

output "kke_iam_role_name" {
  value = aws_iam_role.devops_role.name
}

output "kke_iam_policy_name" {
  value = aws_iam_policy.devops_policy.name
}
```


5️⃣ Terraform Commands (Run in Order)
terraform init
terraform validate
terraform apply


Type:

yes

✅ Expected Output

```
bob@iac-server ~/terraform/t1q4 via 💠 default ➜  terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_dynamodb_table.devops_table will be created
  + resource "aws_dynamodb_table" "devops_table" {
      + arn              = (known after apply)
      + billing_mode     = "PAY_PER_REQUEST"
      + hash_key         = "id"
      + id               = (known after apply)
      + name             = "devops-table-t1q4"
      + read_capacity    = (known after apply)
      + stream_arn       = (known after apply)
      + stream_label     = (known after apply)
      + stream_view_type = (known after apply)
      + tags_all         = (known after apply)
      + write_capacity   = (known after apply)

      + attribute {
          + name = "id"
          + type = "S"
        }

      + point_in_time_recovery (known after apply)

      + server_side_encryption (known after apply)

      + ttl (known after apply)
    }

  # aws_iam_policy.devops_policy will be created
  + resource "aws_iam_policy" "devops_policy" {
      + arn              = (known after apply)
      + attachment_count = (known after apply)
      + id               = (known after apply)
      + name             = "devops-readonly-policy-t1q4"
      + name_prefix      = (known after apply)
      + path             = "/"
      + policy           = (known after apply)
      + policy_id        = (known after apply)
      + tags_all         = (known after apply)
    }

  # aws_iam_role.devops_role will be created
  + resource "aws_iam_role" "devops_role" {
      + arn                   = (known after apply)
      + assume_role_policy    = jsonencode(
            {
              + Statement = [
                  + {
                      + Action    = "sts:AssumeRole"
                      + Effect    = "Allow"
                      + Principal = {
                          + Service = "ec2.amazonaws.com"
                        }
                    },
                ]
              + Version   = "2012-10-17"
            }
        )
      + create_date           = (known after apply)
      + force_detach_policies = false
      + id                    = (known after apply)
      + managed_policy_arns   = (known after apply)
      + max_session_duration  = 3600
      + name                  = "devops-role-t1q4"
      + name_prefix           = (known after apply)
      + path                  = "/"
      + tags_all              = (known after apply)
      + unique_id             = (known after apply)

      + inline_policy (known after apply)
    }

  # aws_iam_role_policy_attachment.attach_policy will be created
  + resource "aws_iam_role_policy_attachment" "attach_policy" {
      + id         = (known after apply)
      + policy_arn = (known after apply)
      + role       = "devops-role-t1q4"
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_dynamodb_table  = "devops-table-t1q4"
  + kke_iam_policy_name = "devops-readonly-policy-t1q4"
  + kke_iam_role_name   = "devops-role-t1q4"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_iam_role.devops_role: Creating...
aws_dynamodb_table.devops_table: Creating...
aws_iam_role.devops_role: Creation complete after 0s [id=devops-role-t1q4]
aws_dynamodb_table.devops_table: Creation complete after 3s [id=devops-table-t1q4]
aws_iam_policy.devops_policy: Creating...
aws_iam_policy.devops_policy: Creation complete after 0s [id=arn:aws:iam::000000000000:policy/devops-readonly-policy-t1q4]
aws_iam_role_policy_attachment.attach_policy: Creating...
aws_iam_role_policy_attachment.attach_policy: Creation complete after 0s [id=devops-role-t1q4-20260201122457259600000001]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

Outputs:

kke_dynamodb_table = "devops-table-t1q4"
kke_iam_policy_name = "devops-readonly-policy-t1q4"
kke_iam_role_name = "devops-role-t1q4"
```

---

🧠 Step-by-Step Explanation (Simple & Clear)
🔹 What problem is this lab solving?

The team wants:

A DynamoDB table

Secure, read-only access

Access controlled using IAM

No full permissions, no wildcards

This is least-privilege access control.

🔹 Why use variables.tf here?

This lab explicitly asks for variables because:

Names must be configurable

Grader checks variable usage

Values must be injected via terraform.tfvars

Think:

variables = lab-controlled inputs

🔹 DynamoDB Table
aws_dynamodb_table


Minimal configuration

PAY_PER_REQUEST billing (no capacity tuning)

Simple hash key (id)

This satisfies “minimal configuration”.

🔹 IAM Role
aws_iam_role


The trust policy says:

“EC2 services are allowed to assume this role”

Without this:

The role would exist

But no AWS service could use it

🔹 IAM Policy (Read-only)

Allowed actions only:

GetItem
Scan
Query


✔ Read access
❌ No write
❌ No delete
❌ No wildcard actions

Resource is restricted to:

aws_dynamodb_table.devops_table.arn


This is fine-grained security 🔐

🔹 Policy Attachment
aws_iam_role_policy_attachment


This step connects permissions to identity.

Without it:

Role exists

Policy exists

But access does not work

🔹 What happens during terraform apply?

1️⃣ DynamoDB table is created
2️⃣ IAM role is created
3️⃣ IAM policy is created
4️⃣ Policy is attached to role
5️⃣ AWS enforces read-only access

🧠 Easy Memory Model

DynamoDB = 📦 data store

IAM role = 👤 identity

IAM policy = 🔑 permissions

Attachment = 🔗 connection

Variables = 🎛 controlled inputs

🚨 Common Mistakes (You avoided them)

❌ Using dynamodb:*
❌ Using Resource = "*"
❌ Missing policy attachment
❌ Hardcoding names instead of variables
❌ Creating extra services

---

# Task 2

1️⃣ locals.tf

```
locals {
  KKE_SNS_TOPIC_NAME        = "devops-sns-topic-t1q2"
  KKE_CLOUDWATCH_ALARM_NAME = "devops-cpu-alarm-t1q2"
}
```

2️⃣ main.tf

```
# SNS Topic
resource "aws_sns_topic" "devops_topic" {
  name = local.KKE_SNS_TOPIC_NAME
}

# CloudWatch Alarm for EC2 CPU Utilization
resource "aws_cloudwatch_metric_alarm" "devops_cpu_alarm" {
  alarm_name          = local.KKE_CLOUDWATCH_ALARM_NAME
  comparison_operator = "GreaterThanThreshold"
  evaluation_periods  = 1
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"
  period              = 300
  statistic           = "Average"
  threshold           = 80
  actions_enabled     = true

  alarm_actions = [
    aws_sns_topic.devops_topic.arn
  ]
}
```

3️⃣ outputs.tf

```
output "KKE_sns_topic_name" {
  value = aws_sns_topic.devops_topic.name
}

output "KKE_cloudwatch_alarm_name" {
  value = aws_cloudwatch_metric_alarm.devops_cpu_alarm.alarm_name
}
```

4️⃣ Terraform Commands (Run in Order)
terraform init
terraform validate
terraform apply


Type:

yes

✅ Expected Output

```
bob@iac-server ~/terraform/t1q2 via 💠 default ➜  terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_cloudwatch_metric_alarm.devops_cpu_alarm will be created
  + resource "aws_cloudwatch_metric_alarm" "devops_cpu_alarm" {
      + actions_enabled                       = true
      + alarm_actions                         = (known after apply)
      + alarm_name                            = "devops-cpu-alarm-t1q2"
      + arn                                   = (known after apply)
      + comparison_operator                   = "GreaterThanThreshold"
      + evaluate_low_sample_count_percentiles = (known after apply)
      + evaluation_periods                    = 1
      + id                                    = (known after apply)
      + metric_name                           = "CPUUtilization"
      + namespace                             = "AWS/EC2"
      + period                                = 300
      + statistic                             = "Average"
      + tags_all                              = (known after apply)
      + threshold                             = 80
      + treat_missing_data                    = "missing"
    }

  # aws_sns_topic.devops_topic will be created
  + resource "aws_sns_topic" "devops_topic" {
      + arn                         = (known after apply)
      + beginning_archive_time      = (known after apply)
      + content_based_deduplication = false
      + fifo_topic                  = false
      + id                          = (known after apply)
      + name                        = "devops-sns-topic-t1q2"
      + name_prefix                 = (known after apply)
      + owner                       = (known after apply)
      + policy                      = (known after apply)
      + signature_version           = (known after apply)
      + tags_all                    = (known after apply)
      + tracing_config              = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + KKE_cloudwatch_alarm_name = "devops-cpu-alarm-t1q2"
  + KKE_sns_topic_name        = "devops-sns-topic-t1q2"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_sns_topic.devops_topic: Creating...
aws_sns_topic.devops_topic: Creation complete after 0s [id=arn:aws:sns:us-east-1:000000000000:devops-sns-topic-t1q2]
aws_cloudwatch_metric_alarm.devops_cpu_alarm: Creating...
aws_cloudwatch_metric_alarm.devops_cpu_alarm: Creation complete after 0s [id=devops-cpu-alarm-t1q2]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

Outputs:

KKE_cloudwatch_alarm_name = "devops-cpu-alarm-t1q2"
KKE_sns_topic_name = "devops-sns-topic-t1q2"
```

---

🧠 Step-by-Step Explanation (Simple & Clear)
🔹 What problem is this lab solving?

The team wants automatic alerts when EC2 CPU usage becomes high.

Flow:

CloudWatch Alarm → SNS Topic → Alert notification

🔹 Why use locals.tf?

Names are fixed by the lab

No user input required

Prevents typos

Ensures outputs match exactly

Think:

locals = constants

🔹 SNS Topic (aws_sns_topic)

This is the alert destination.

CloudWatch sends alarm notifications to SNS, not directly to users.

SNS = 📣 alert broadcaster

🔹 CloudWatch Alarm (aws_cloudwatch_metric_alarm)

The alarm:

Monitors CPUUtilization

Uses Average

Checks every 5 minutes

Triggers when CPU > 80%

Sends notification to SNS

🔹 Why no EC2 instance?

The lab does not ask for one.

CloudWatch alarms can be created without an EC2 instance.
Creating extra resources can cause grader failure.

🔹 What happens during terraform apply?

1️⃣ SNS topic is created
2️⃣ CloudWatch alarm is created
3️⃣ Alarm is linked to SNS via ARN
4️⃣ AWS begins monitoring CPU metrics
5️⃣ Alerts are ready to trigger

🧠 Easy Memory Model

SNS topic = 📣 alerts go here

CloudWatch alarm = 🚨 condition checker

locals = 📌 fixed names

alarm_actions = 🔗 connection

🚨 Common Mistakes 

❌ Creating EC2 when not asked
❌ Wrong output names
❌ Missing actions_enabled = true
❌ Using variables instead of locals
❌ Using wrong comparison operator

---

# Task 3

1️⃣ variables.tf

```
variable "KKE_VPC_CIDR" {
  type = string
}

variable "KKE_SUBNET_CIDR" {
  type = string
}
```

2️⃣ terraform.tfvars

```
KKE_VPC_CIDR    = "10.0.0.0/16"
KKE_SUBNET_CIDR = "10.0.1.0/24"
```

3️⃣ main.tf

```
# VPC
resource "aws_vpc" "devops_vpc" {
  cidr_block = var.KKE_VPC_CIDR

  tags = {
    Name = "devops-priv-vpc-t2q3"
  }
}

# Subnet (Private, no auto-assign public IP)
resource "aws_subnet" "devops_subnet" {
  vpc_id                  = aws_vpc.devops_vpc.id
  cidr_block              = var.KKE_SUBNET_CIDR
  map_public_ip_on_launch = false

  tags = {
    Name = "devops-priv-subnet-t2q3"
  }
}

# Security Group (allow traffic only from VPC CIDR)
resource "aws_security_group" "devops_sg" {
  name   = "devops-priv-sg-t2q3"
  vpc_id = aws_vpc.devops_vpc.id

  ingress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = [var.KKE_VPC_CIDR]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = [var.KKE_VPC_CIDR]
  }
}

# EC2 Instance (Private)
resource "aws_instance" "devops_ec2" {
  ami                         = "ami-0c02fb55956c7d316"
  instance_type               = "t2.micro"
  subnet_id                   = aws_subnet.devops_subnet.id
  vpc_security_group_ids      = [aws_security_group.devops_sg.id]
  associate_public_ip_address = false

  tags = {
    Name = "devops-priv-ec2-t2q3"
  }
}
```


4️⃣ outputs.tf
```
output "KKE_vpc_name" {
  value = aws_vpc.devops_vpc.tags["Name"]
}

output "KKE_subnet_name" {
  value = aws_subnet.devops_subnet.tags["Name"]
}

output "KKE_ec2_private" {
  value = aws_instance.devops_ec2.tags["Name"]
}
```

5️⃣ Terraform Commands (Run in Order)
terraform init
terraform validate
terraform apply


Type:

yes

✅ Expected Output

```
bob@iac-server ~/terraform/t2q3 via 💠 default ➜  terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.devops_ec2 will be created
  + resource "aws_instance" "devops_ec2" {
      + ami                                  = "ami-0c02fb55956c7d316"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = false
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
      + instance_type                        = "t2.micro"
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
          + "Name" = "devops-priv-ec2-t2q3"
        }
      + tags_all                             = {
          + "Name" = "devops-priv-ec2-t2q3"
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

  # aws_security_group.devops_sg will be created
  + resource "aws_security_group" "devops_sg" {
      + arn                    = (known after apply)
      + description            = "Managed by Terraform"
      + egress                 = [
          + {
              + cidr_blocks      = [
                  + "10.0.0.0/16",
                ]
              + from_port        = 0
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "-1"
              + security_groups  = []
              + self             = false
              + to_port          = 0
                # (1 unchanged attribute hidden)
            },
        ]
      + id                     = (known after apply)
      + ingress                = [
          + {
              + cidr_blocks      = [
                  + "10.0.0.0/16",
                ]
              + from_port        = 0
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "-1"
              + security_groups  = []
              + self             = false
              + to_port          = 0
                # (1 unchanged attribute hidden)
            },
        ]
      + name                   = "devops-priv-sg-t2q3"
      + name_prefix            = (known after apply)
      + owner_id               = (known after apply)
      + revoke_rules_on_delete = false
      + tags_all               = (known after apply)
      + vpc_id                 = (known after apply)
    }

  # aws_subnet.devops_subnet will be created
  + resource "aws_subnet" "devops_subnet" {
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
          + "Name" = "devops-priv-subnet-t2q3"
        }
      + tags_all                                       = {
          + "Name" = "devops-priv-subnet-t2q3"
        }
      + vpc_id                                         = (known after apply)
    }

  # aws_vpc.devops_vpc will be created
  + resource "aws_vpc" "devops_vpc" {
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
          + "Name" = "devops-priv-vpc-t2q3"
        }
      + tags_all                             = {
          + "Name" = "devops-priv-vpc-t2q3"
        }
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + KKE_ec2_private = "devops-priv-ec2-t2q3"
  + KKE_subnet_name = "devops-priv-subnet-t2q3"
  + KKE_vpc_name    = "devops-priv-vpc-t2q3"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_vpc.devops_vpc: Creating...
aws_vpc.devops_vpc: Creation complete after 0s [id=vpc-7c0b211705fb1e198]
aws_subnet.devops_subnet: Creating...
aws_security_group.devops_sg: Creating...
aws_subnet.devops_subnet: Creation complete after 1s [id=subnet-a16a5e8ea4469ed2b]
aws_security_group.devops_sg: Creation complete after 1s [id=sg-0da9e8f2cab624d93]
aws_instance.devops_ec2: Creating...
aws_instance.devops_ec2: Still creating... [10s elapsed]
aws_instance.devops_ec2: Creation complete after 10s [id=i-07bc9da1a5849f43e]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

Outputs:

KKE_ec2_private = "devops-priv-ec2-t2q3"
KKE_subnet_name = "devops-priv-subnet-t2q3"
KKE_vpc_name = "devops-priv-vpc-t2q3"
```

---

🧠 Step-by-Step Explanation (Simple & Clear)
🔹 What problem is this lab solving?

The team wants:

A private network

No public internet exposure

EC2 access only inside the VPC

Secure internal communication

This is basic cloud network isolation.

🔹 Why create a VPC?

A VPC is a private network boundary.

CIDR 10.0.0.0/16 means:

All private IPs

No internet by default

Full isolation from public AWS networks

🔹 Why create a private subnet?

The subnet:

Lives inside the VPC

Uses a smaller CIDR (/24)

Has map_public_ip_on_launch = false

This ensures:

EC2 instances never receive public IPs

🔹 Why the Security Group restricts CIDR?

Ingress + Egress:

cidr_blocks = [10.0.0.0/16]


Meaning:

Only traffic inside the VPC

No internet access

No external SSH, HTTP, or ICMP

This is zero-trust networking.

🔹 Why no Internet Gateway?

The lab never asks for one.

Without an Internet Gateway:

Subnet stays private

EC2 stays isolated

Grader expectations are met

🔹 Why variables.tf?

CIDR blocks are:

Configurable

Provided by lab

Required via terraform.tfvars

Think:

variables = lab-controlled inputs

🔹 What happens during terraform apply?

1️⃣ VPC is created
2️⃣ Subnet is created inside the VPC
3️⃣ Security group is created
4️⃣ EC2 instance launches privately
5️⃣ No public access exists

🧠 Easy Memory Model

VPC = 🏠 private network

Subnet = 🧱 private segment

Security Group = 🔒 firewall

EC2 = 🖥 private compute

No IGW = 🚫 no internet

🚨 Common Mistakes (You avoided them)

❌ Enabling public IP
❌ Using 0.0.0.0/0
❌ Adding Internet Gateway
❌ Wrong CIDR blocks
❌ Missing security group

---

# Task 4

1️⃣ Prerequisite (IMPORTANT)

📂 Working directory must be exactly:

```
# Run:
cd /home/bob/terraform/t2q1
# Verify:
pwd
```


2️⃣ Verify Existing EC2 Instance in State

```
# Run:
terraform state list
```


3️⃣ Capture Current Instance ID (Before Replace)

```
# Run:
terraform output
```

📌 Note the current EC2 instance ID
instance_id = "i-2bed8aebc72202bb9"



4️⃣ Force Recreate EC2 Using -replace

Run exactly:

terraform apply -replace="aws_instance.web_server"


Type:

yes

5️⃣ Confirm Recreation (During Apply)

```
bob@iac-server ~/terraform/t2q1 via 💠 default ✖ terraform apply -replace="aws_instance.web_server"
aws_instance.web_server: Refreshing state... [id=i-2bed8aebc72202bb9]

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
-/+ destroy and then create replacement

Terraform will perform the following actions:

  # aws_instance.web_server will be replaced, as requested
-/+ resource "aws_instance" "web_server" {
      ~ arn                                  = "arn:aws:ec2:us-east-1::instance/i-2bed8aebc72202bb9" -> (known after apply)
      ~ associate_public_ip_address          = true -> (known after apply)
      ~ availability_zone                    = "us-east-1a" -> (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      ~ disable_api_stop                     = false -> (known after apply)
      ~ disable_api_termination              = false -> (known after apply)
      ~ ebs_optimized                        = false -> (known after apply)
      + enable_primary_ipv6                  = (known after apply)
      - hibernation                          = false -> null
      + host_id                              = (known after apply)
      + host_resource_group_arn              = (known after apply)
      + iam_instance_profile                 = (known after apply)
      ~ id                                   = "i-2bed8aebc72202bb9" -> (known after apply)
      ~ instance_initiated_shutdown_behavior = "stop" -> (known after apply)
      + instance_lifecycle                   = (known after apply)
      ~ instance_state                       = "running" -> (known after apply)
      ~ ipv6_address_count                   = 0 -> (known after apply)
      ~ ipv6_addresses                       = [] -> (known after apply)
      + key_name                             = (known after apply)
      ~ monitoring                           = false -> (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      ~ placement_partition_number           = 0 -> (known after apply)
      ~ primary_network_interface_id         = "eni-2033b90a4cb4ce1a4" -> (known after apply)
      ~ private_dns                          = "ip-10-85-67-208.ec2.internal" -> (known after apply)
      ~ private_ip                           = "10.85.67.208" -> (known after apply)
      ~ public_dns                           = "ec2-54-214-95-71.compute-1.amazonaws.com" -> (known after apply)
      ~ public_ip                            = "54.214.95.71" -> (known after apply)
      ~ secondary_private_ips                = [] -> (known after apply)
      ~ security_groups                      = [] -> (known after apply)
      + spot_instance_request_id             = (known after apply)
      ~ subnet_id                            = "subnet-90508b0e58e056084" -> (known after apply)
        tags                                 = {
            "Name" = "devops-ec2-t2q1"
        }
      ~ tenancy                              = "default" -> (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      ~ vpc_security_group_ids               = [] -> (known after apply)
        # (6 unchanged attributes hidden)

      ~ capacity_reservation_specification (known after apply)

      ~ cpu_options (known after apply)

      ~ ebs_block_device (known after apply)

      ~ enclave_options (known after apply)

      ~ ephemeral_block_device (known after apply)

      ~ instance_market_options (known after apply)

      ~ maintenance_options (known after apply)

      ~ metadata_options (known after apply)
      - metadata_options {
          - http_endpoint               = "enabled" -> null
          - http_protocol_ipv6          = "disabled" -> null
          - http_put_response_hop_limit = 1 -> null
          - http_tokens                 = "optional" -> null
          - instance_metadata_tags      = "disabled" -> null
        }

      ~ network_interface (known after apply)

      ~ private_dns_name_options (known after apply)

      ~ root_block_device (known after apply)
      - root_block_device {
          - delete_on_termination = true -> null
          - device_name           = "/dev/sda1" -> null
          - encrypted             = false -> null
          - iops                  = 0 -> null
          - tags                  = {} -> null
          - tags_all              = {} -> null
          - throughput            = 0 -> null
          - volume_id             = "vol-2b57460d83d2e0698" -> null
          - volume_size           = 8 -> null
          - volume_type           = "gp2" -> null
            # (1 unchanged attribute hidden)
        }
    }

Plan: 1 to add, 0 to change, 1 to destroy.

Changes to Outputs:
  ~ instance_id = "i-2bed8aebc72202bb9" -> (known after apply)

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.web_server: Destroying... [id=i-2bed8aebc72202bb9]
aws_instance.web_server: Still destroying... [id=i-2bed8aebc72202bb9, 10s elapsed]
aws_instance.web_server: Destruction complete after 11s
aws_instance.web_server: Creating...
aws_instance.web_server: Still creating... [10s elapsed]
aws_instance.web_server: Creation complete after 10s [id=i-12d5264fb1ac8596d]

Apply complete! Resources: 1 added, 0 changed, 1 destroyed.

Outputs:

instance_id = "i-12d5264fb1ac8596d"
```


✅ This confirms forced recreation, not update.

6️⃣ Verify Instance ID Has Changed

```
# Run:
terraform output
```

✅ The EC2 instance ID must be different from before.

This satisfies:

“The new instance created using the -replace option should have a different instance ID”

---

🧠 Step-by-Step Explanation (Simple & Clear)
🔹 What problem is this lab testing?

This lab tests your understanding of:

Terraform state

Resource recreation

Forced replacement without config change

This is a real production skill.

🔹 What does -replace actually do?

-replace tells Terraform:

“Destroy this resource and recreate it,
even if nothing has changed in the code.”

Normally, Terraform only changes resources if config changes.
-replace overrides that behavior.

🔹 Why not change the code?

The lab explicitly says:

“Even though the configuration remains unchanged”

So:

❌ No edits to .tf files

❌ No AMI changes

❌ No instance_type changes

CLI-only operation ✔

🔹 Why instance ID must change?

An EC2 instance ID is unique per instance.

If Terraform:

Destroys the old instance

Creates a new one

AWS must assign a new ID.

Same ID = ❌ lab failure
Different ID = ✅ lab success

🔹 Why terraform plan must be clean?

KodeKloud graders always check:

terraform plan


If Terraform still wants to change something:

It means recreation was incomplete

Or state drift exists

Or resource name was wrong

That’s why this step is mandatory.

🧠 Easy Memory Model

terraform apply → normal behavior

-replace → 🔨 force rebuild

State updated → 🧠 Terraform remembers new instance

Clean plan → ✅ safe to submit

🚨 Common Mistakes 

❌ Using wrong resource name in -replace
❌ Editing .tf files
❌ Running in wrong directory
❌ Not checking terraform plan
❌ Submitting with pending changes

---

# TASK 5

1️⃣ variables.tf

```
variable "KKE_INSTANCE_COUNT" {
  type = number
}

variable "KKE_INSTANCE_TYPE" {
  type = string
}

variable "KKE_KEY_NAME" {
  type = string
}

variable "KKE_INSTANCE_PREFIX" {
  type = string
}
```

2️⃣ terraform.tfvars

```
KKE_INSTANCE_COUNT  = 3
KKE_INSTANCE_TYPE   = "t2.micro"
KKE_KEY_NAME        = "devops-key-t3q4"
KKE_INSTANCE_PREFIX = "devops-instance-t3q4"
```

3️⃣ locals.tf

```
data "aws_ami" "amazon_linux_2" {
  most_recent = true
  owners      = ["amazon"]

  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }

  filter {
    name   = "state"
    values = ["available"]
  }
}

locals {
  AMI_ID = data.aws_ami.amazon_linux_2.id
}
```

4️⃣ main.tf

```
resource "aws_instance" "devops_instances" {
  count         = var.KKE_INSTANCE_COUNT
  ami           = local.AMI_ID
  instance_type = var.KKE_INSTANCE_TYPE
  key_name      = var.KKE_KEY_NAME

  tags = {
    Name = "${var.KKE_INSTANCE_PREFIX}-${count.index + 1}"
  }
}
```

5️⃣ outputs.tf

```
output "kke_instance_names" {
  value = aws_instance.devops_instances[*].tags["Name"]
}
```

6️⃣ Terraform Commands (Run in Order)
terraform init
terraform validate
terraform apply


Type:

yes

✅ Expected Output

```
bob@iac-server ~/terraform/t3q4 via 💠 default ➜  terraform apply
data.aws_ami.amazon_linux_2: Reading...
data.aws_ami.amazon_linux_2: Read complete after 0s [id=ami-04681a1dbd79675a5]

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.devops_instances[0] will be created
  + resource "aws_instance" "devops_instances" {
      + ami                                  = "ami-04681a1dbd79675a5"
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
      + instance_type                        = "t2.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = "devops-key-t3q4"
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
          + "Name" = "devops-instance-t3q4-1"
        }
      + tags_all                             = {
          + "Name" = "devops-instance-t3q4-1"
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

  # aws_instance.devops_instances[1] will be created
  + resource "aws_instance" "devops_instances" {
      + ami                                  = "ami-04681a1dbd79675a5"
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
      + instance_type                        = "t2.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = "devops-key-t3q4"
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
          + "Name" = "devops-instance-t3q4-2"
        }
      + tags_all                             = {
          + "Name" = "devops-instance-t3q4-2"
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

  # aws_instance.devops_instances[2] will be created
  + resource "aws_instance" "devops_instances" {
      + ami                                  = "ami-04681a1dbd79675a5"
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
      + instance_type                        = "t2.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = "devops-key-t3q4"
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
          + "Name" = "devops-instance-t3q4-3"
        }
      + tags_all                             = {
          + "Name" = "devops-instance-t3q4-3"
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

Plan: 3 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_instance_names = [
      + "devops-instance-t3q4-1",
      + "devops-instance-t3q4-2",
      + "devops-instance-t3q4-3",
    ]

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.devops_instances[1]: Creating...
aws_instance.devops_instances[0]: Creating...
aws_instance.devops_instances[2]: Creating...
aws_instance.devops_instances[2]: Still creating... [10s elapsed]
aws_instance.devops_instances[0]: Still creating... [10s elapsed]
aws_instance.devops_instances[1]: Still creating... [10s elapsed]
aws_instance.devops_instances[0]: Creation complete after 10s [id=i-520955aa9320f2ca7]
aws_instance.devops_instances[1]: Creation complete after 10s [id=i-af6d98201e1afd126]
aws_instance.devops_instances[2]: Creation complete after 10s [id=i-30cc75ca448363d06]

Apply complete! Resources: 3 added, 0 changed, 0 destroyed.

Outputs:

kke_instance_names = [
  "devops-instance-t3q4-1",
  "devops-instance-t3q4-2",
  "devops-instance-t3q4-3",
]
```
---

🧠 Step-by-Step Explanation (Simple & Clear)
🔹 What problem is this lab solving?

The team wants:

Multiple EC2 instances

Consistent naming

Scalable configuration

No copy-paste resources

Terraform’s count solves this cleanly.

🔹 Why use count?

count lets Terraform:

Create multiple identical resources

Track them individually in state

Scale up/down by changing one number

Example:

count = 3


→ Terraform creates 3 EC2 instances

🔹 Why use variables.tf?

This lab requires configurability:

Number of instances

Instance type

Key pair

Name prefix

Think:

variables = lab-controlled inputs

🔹 Why locals.tf for AMI?

The lab explicitly asks to:

“Define a local variable named AMI_ID that retrieves the latest Amazon Linux 2 AMI using a data source”

So we:

1️⃣ Query AWS for the latest AMI
2️⃣ Store it in local.AMI_ID
3️⃣ Reuse it safely

This avoids hardcoding AMI IDs ❌

🔹 How instance naming works
"${var.KKE_INSTANCE_PREFIX}-${count.index + 1}"


count.index starts at 0

We add + 1 to match lab naming

Produces:

devops-instance-t3q4-1
devops-instance-t3q4-2
devops-instance-t3q4-3

🔹 Why output uses [*]
aws_instance.devops_instances[*].tags["Name"]


This collects all instance names into a list.

The grader expects:

A list

With correct names

In order

🔹 What happens during terraform apply?

1️⃣ Terraform fetches latest Amazon Linux 2 AMI
2️⃣ Terraform creates 3 EC2 instances
3️⃣ Each instance gets a unique name
4️⃣ Terraform outputs all instance names

🧠 Easy Memory Model

count = 🔁 repeat

variables = 🎛 inputs

locals = 📌 internal constants

data source = 🔍 lookup

output list = 📤 results

🚨 Common Mistakes 

❌ Hardcoding AMI ID
❌ Using for_each instead of count
❌ Missing +1 in naming
❌ Wrong output format
❌ Creating separate .tf files unnecessarily

---

# TASK 6

1️⃣ variables.tf

```
variable "KKE_USER_NAME" {
  type = string
}
```

2️⃣ terraform.tfvars

```
KKE_USER_NAME = "iamuser_ravi_t3q2"
```

3️⃣ main.tf

```
resource "aws_iam_user" "iam_user" {
  name = var.KKE_USER_NAME

  provisioner "local-exec" {
    command = "echo 'KKE ${var.KKE_USER_NAME} has been created successfully!' >> /home/bob/terraform/t3q2/KKE_user_created.log"
  }
}
```

4️⃣ outputs.tf

```
output "kke_iam_user_name" {
  value = aws_iam_user.iam_user.name
}
```

5️⃣ Terraform Commands (Run in Order)
terraform init
terraform validate
terraform apply


Type:

yes

✅ Expected Output

```
bob@iac-server ~/terraform/t3q2 via 💠 default ➜  terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_iam_user.iam_user will be created
  + resource "aws_iam_user" "iam_user" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "iamuser_ravi_t3q2"
      + path          = "/"
      + tags_all      = (known after apply)
      + unique_id     = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_iam_user_name = "iamuser_ravi_t3q2"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_iam_user.iam_user: Creating...
aws_iam_user.iam_user: Provisioning with 'local-exec'...
aws_iam_user.iam_user (local-exec): Executing: ["/bin/sh" "-c" "echo 'KKE iamuser_ravi_t3q2 has been created successfully!' >> /home/bob/terraform/t3q2/KKE_user_created.log"]
aws_iam_user.iam_user: Creation complete after 0s [id=iamuser_ravi_t3q2]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

kke_iam_user_name = "iamuser_ravi_t3q2"
```

---

🧠 Step-by-Step Explanation (Simple & Clear)
🔹 What problem is this lab solving?

This lab demonstrates Terraform provisioners, specifically:

Running a local command

After a resource is created

To perform an external action (logging)

🔹 Why use aws_iam_user?

The lab requires:

An IAM user

With an exact name

Managed by Terraform

So we use:

aws_iam_user

🔹 What is a local-exec provisioner?

local-exec runs a command:

On the machine where Terraform runs

Not inside AWS

After the resource is created

Here it:

Writes a confirmation message

To a local log file

🔹 Why is the provisioner inside the resource?

Provisioners are tied to resource lifecycle:

Resource created → provisioner runs

Resource destroyed → provisioner does NOT run (by default)

This guarantees:

Log is written only if IAM user creation succeeds

🔹 Why use variables.tf here?

The lab explicitly requires:

IAM user name via variable

Value supplied via terraform.tfvars

This ensures:

Configurable input

Grader validation

No hardcoding mistakes

🔹 What happens during terraform apply?

1️⃣ Terraform creates IAM user
2️⃣ AWS confirms creation
3️⃣ local-exec runs
4️⃣ Message is written to KKE_user_created.log
5️⃣ Terraform outputs IAM user name

🧠 Easy Memory Model

IAM user = 👤 identity

Provisioner = ⚙️ post-action

local-exec = 🖥 local command

Log file = 📝 proof of execution

🚨 Common Mistakes

❌ Wrong file path
❌ Missing local-exec
❌ Writing log to wrong directory
❌ Hardcoding user name
❌ Using remote-exec instead of local-exec

---

# TASK 7

1️⃣ variables.tf

```
variable "KKE_BUCKET" {
  type = string
}
```

2️⃣ terraform.tfvars

```
KKE_BUCKET = "devops-sync-5915-t4q4"
```

3️⃣ main.tf

```
# Existing bucket (DO NOT MODIFY)
resource "aws_s3_bucket" "wordpress_bucket" {
  bucket = "devops-s3-16124-t4q4"
}

resource "aws_s3_bucket_acl" "wordpress_bucket_acl" {
  bucket = aws_s3_bucket.wordpress_bucket.id
  acl    = "private"
}

# Create the NEW private bucket (from variable)
resource "aws_s3_bucket" "sync_bucket" {
  bucket = var.KKE_BUCKET
}

resource "aws_s3_bucket_acl" "sync_bucket_acl" {
  bucket = aws_s3_bucket.sync_bucket.id
  acl    = "private"
}

# Perform data migration (Terraform-triggered)
resource "null_resource" "s3_sync" {

  provisioner "local-exec" {
    command = "aws s3 sync s3://devops-s3-16124-t4q4 s3://${var.KKE_BUCKET}"
  }

  depends_on = [
    aws_s3_bucket.sync_bucket
  ]
}
```

4️⃣ outputs.tf

```
output "new_kke_bucket_name" {
  value = aws_s3_bucket.sync_bucket.bucket
}

output "new_kke_bucket_acl" {
  value = aws_s3_bucket_acl.sync_bucket_acl.acl
}
```

5️⃣ Terraform Commands (Run in Order)
terraform init
terraform validate
terraform apply


Type:

yes

✅ Outputs show:

```
new_kke_bucket_name = "devops-sync-5915-t4q4"
new_kke_bucket_acl  = "private"
```
---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
🔹 What problem is this lab solving?

You need to:

Create a new S3 bucket

Copy all objects from an existing bucket

Ensure no data loss

Terraform is used to:

Define infrastructure

Perform controlled copying

Ensure consistency

🔹 Why does the lab give you the source bucket?
resource "aws_s3_bucket" "wordpress_bucket"


This bucket:

Already exists

Already has data

Is treated as read-only

Terraform needs this block so it can:

Discover object keys

Reference the bucket during copy

👉 Terraform does NOT recreate it

🔹 Why must the new bucket use variables.tf?

KodeKloud checks:

Bucket name must come from var.KKE_BUCKET

Hardcoding = ❌ fail

This ensures:

Parameterized infrastructure

Reusability

Correct grading

🔹 How does data migration actually happen?
Step 1: List objects
data "aws_s3_bucket_objects"


Terraform:

Asks AWS: “What files exist in this bucket?”

Gets a list of object keys

Step 2: Copy each object
aws_s3_bucket_object


Terraform:

Loops over every object (for_each)

Copies it key-by-key

Places it in the new bucket

This is Terraform’s version of:

aws s3 sync source-bucket destination-bucket

🔹 Why depends_on is needed
depends_on = [aws_s3_bucket.new_bucket]


This forces Terraform to:

1️⃣ Create new bucket
2️⃣ Then start copying objects

Without this → race condition → ❌ failures

🔹 What happens during terraform apply

1️⃣ Terraform checks source bucket
2️⃣ Creates destination bucket
3️⃣ Sets ACL to private
4️⃣ Lists source objects
5️⃣ Copies every object
6️⃣ Outputs final values

🧠 Easy Mental Model
Component	Meaning
Source bucket	📦 Read-only data
New bucket	📥 Destination
Data source	📋 Object list
for_each	🔁 Copy loop
outputs	📢 Proof of success
🚨 Common Mistakes (That You Avoided)

❌ Modifying source bucket
❌ Hardcoding new bucket name
❌ Using aws s3 sync manually
❌ Forgetting ACL
❌ Missing depends_on

---

# TASK 8

1️⃣ variables.tf

```
variable "KKE_BUCKET_NAME" {
  type = string
}
```


2️⃣ terraform.tfvars

```
KKE_BUCKET_NAME = "devops-s3-28270-t4q2"
```



3️⃣ main.tf

```
resource "aws_s3_bucket" "protected_bucket" {
  bucket = var.KKE_BUCKET_NAME

  lifecycle {
    prevent_destroy = true
  }
}
```

4️⃣ outputs.tf

```
output "s3_bucket_name" {
  value = aws_s3_bucket.protected_bucket.bucket
}
```


5️⃣ Terraform Commands (Run in Order)
terraform init
terraform validate
terraform apply


Type:

yes

✅ Output

```
bob@iac-server ~/terraform/t4q2 via 💠 default ➜  terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_s3_bucket.protected_bucket will be created
  + resource "aws_s3_bucket" "protected_bucket" {
      + acceleration_status         = (known after apply)
      + acl                         = (known after apply)
      + arn                         = (known after apply)
      + bucket                      = "devops-s3-28270-t4q2"
      + bucket_domain_name          = (known after apply)
      + bucket_prefix               = (known after apply)
      + bucket_regional_domain_name = (known after apply)
      + force_destroy               = false
      + hosted_zone_id              = (known after apply)
      + id                          = (known after apply)
      + object_lock_enabled         = (known after apply)
      + policy                      = (known after apply)
      + region                      = (known after apply)
      + request_payer               = (known after apply)
      + tags_all                    = (known after apply)
      + website_domain              = (known after apply)
      + website_endpoint            = (known after apply)

      + cors_rule (known after apply)

      + grant (known after apply)

      + lifecycle_rule (known after apply)

      + logging (known after apply)

      + object_lock_configuration (known after apply)

      + replication_configuration (known after apply)

      + server_side_encryption_configuration (known after apply)

      + versioning (known after apply)

      + website (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + s3_bucket_name = "devops-s3-28270-t4q2"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_s3_bucket.protected_bucket: Creating...
aws_s3_bucket.protected_bucket: Creation complete after 0s [id=devops-s3-28270-t4q2]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

s3_bucket_name = "devops-s3-28270-t4q2"
```

---

🧠 Simple Step-by-Step Explanation (Beginner Friendly)

Let’s explain why each part exists, in plain language.

🔹 Why use variables.tf?

The lab wants the bucket name to be:

Dynamic

Not hardcoded

That’s why we use:

bucket = var.KKE_BUCKET_NAME


The grader checks this explicitly.

🔹 What does prevent_destroy = true do?
lifecycle {
  prevent_destroy = true
}


This tells Terraform:

❌ “Never allow this bucket to be destroyed — even if someone runs terraform destroy.”

If someone tries:

terraform destroy


Terraform will stop with an error.

This protects against accidental deletion, which is the core goal of this lab.

🔹 Why no ACL, versioning, or encryption?

Because the lab did not ask for them.

KodeKloud graders are strict:

Extra resources can cause failures

Minimal, exact configuration is safest

🔹 What happens during terraform apply?

Terraform reads terraform.tfvars

Resolves var.KKE_BUCKET_NAME

Creates the S3 bucket

Registers the lifecycle rule in state

Outputs the bucket name

🔹 What happens if someone tries to delete it later?

Terraform will say:

❌ Resource has prevent_destroy set and cannot be destroyed.

That’s expected and correct.

🧠 Easy Memory Rule
variables.tf      → user input
terraform.tfvars  → actual value
main.tf           → infrastructure + protection
lifecycle block   → safety lock 🔒
outputs.tf        → grader verification

🚨 Common Mistakes (Avoid These)

❌ Hardcoding bucket name
❌ Forgetting prevent_destroy
❌ Putting lifecycle in a separate file
❌ Output name mismatch
❌ Adding unnecessary resources

---

[KodeKloud Certificate-Terraform- Level 2](https://engineer.kodekloud.com/certificate-verification/87115dae-c75f-46f6-956e-bbda943826cf)

![alt text](<Terraform - Level 2.png>)