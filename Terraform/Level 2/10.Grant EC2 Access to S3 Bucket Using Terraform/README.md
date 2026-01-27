1️⃣ variables.tf

```
variable "KKE_BUCKET_NAME" {
  type = string
}

variable "KKE_POLICY_NAME" {
  type = string
}

variable "KKE_ROLE_NAME" {
  type = string
}
```

2️⃣ terraform.tfvars


```
KKE_BUCKET_NAME = "xfusion-logs-8687"
KKE_POLICY_NAME = "xfusion-access-policy"
KKE_ROLE_NAME   = "xfusion-role"
```

3️⃣ data.tf


```
data "aws_ami" "amazon_linux_2" {
  most_recent = true
  owners      = ["amazon"]

  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }
}
```

4️⃣ main.tf

```
# S3 bucket for logs
resource "aws_s3_bucket" "logs_bucket" {
  bucket = var.KKE_BUCKET_NAME
}

# IAM role for EC2
resource "aws_iam_role" "xfusion_role" {
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

# IAM policy allowing S3 PutObject
resource "aws_iam_policy" "xfusion_policy" {
  name = var.KKE_POLICY_NAME

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect   = "Allow"
      Action   = ["s3:PutObject"]
      Resource = "arn:aws:s3:::${var.KKE_BUCKET_NAME}/*"
    }]
  })
}

# Attach policy to role
resource "aws_iam_role_policy_attachment" "attach_policy" {
  role       = aws_iam_role.xfusion_role.name
  policy_arn = aws_iam_policy.xfusion_policy.arn
}

# IAM instance profile
resource "aws_iam_instance_profile" "xfusion_profile" {
  name = "xfusion-instance-profile"
  role = aws_iam_role.xfusion_role.name
}

# EC2 instance
resource "aws_instance" "xfusion_ec2" {
  ami                    = data.aws_ami.amazon_linux_2.id
  instance_type          = "t2.micro"
  iam_instance_profile   = aws_iam_instance_profile.xfusion_profile.name

  tags = {
    Name = "xfusion-ec2"
  }
}
```

5️⃣ Terraform Commands (Run in Order)

terraform init
terraform validate
terraform apply


Type:

yes

✅ Expected Result

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply
data.aws_ami.amazon_linux_2: Reading...
data.aws_ami.amazon_linux_2: Read complete after 2s [id=ami-04681a1dbd79675a5]

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_iam_instance_profile.xfusion_profile will be created
  + resource "aws_iam_instance_profile" "xfusion_profile" {
      + arn         = (known after apply)
      + create_date = (known after apply)
      + id          = (known after apply)
      + name        = "xfusion-instance-profile"
      + name_prefix = (known after apply)
      + path        = "/"
      + role        = "xfusion-role"
      + tags_all    = (known after apply)
      + unique_id   = (known after apply)
    }

  # aws_iam_policy.xfusion_policy will be created
  + resource "aws_iam_policy" "xfusion_policy" {
      + arn              = (known after apply)
      + attachment_count = (known after apply)
      + id               = (known after apply)
      + name             = "xfusion-access-policy"
      + name_prefix      = (known after apply)
      + path             = "/"
      + policy           = jsonencode(
            {
              + Statement = [
                  + {
                      + Action   = [
                          + "s3:PutObject",
                        ]
                      + Effect   = "Allow"
                      + Resource = "arn:aws:s3:::xfusion-logs-8687/*"
                    },
                ]
              + Version   = "2012-10-17"
            }
        )
      + policy_id        = (known after apply)
      + tags_all         = (known after apply)
    }

  # aws_iam_role.xfusion_role will be created
  + resource "aws_iam_role" "xfusion_role" {
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
      + name                  = "xfusion-role"
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
      + role       = "xfusion-role"
    }

  # aws_instance.xfusion_ec2 will be created
  + resource "aws_instance" "xfusion_ec2" {
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
      + iam_instance_profile                 = "xfusion-instance-profile"
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
          + "Name" = "xfusion-ec2"
        }
      + tags_all                             = {
          + "Name" = "xfusion-ec2"
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

  # aws_s3_bucket.logs_bucket will be created
  + resource "aws_s3_bucket" "logs_bucket" {
      + acceleration_status         = (known after apply)
      + acl                         = (known after apply)
      + arn                         = (known after apply)
      + bucket                      = "xfusion-logs-8687"
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

Plan: 6 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_iam_policy.xfusion_policy: Creating...
aws_iam_role.xfusion_role: Creating...
aws_s3_bucket.logs_bucket: Creating...
aws_iam_policy.xfusion_policy: Creation complete after 0s [id=arn:aws:iam::000000000000:policy/xfusion-access-policy]
aws_iam_role.xfusion_role: Creation complete after 0s [id=xfusion-role]
aws_iam_role_policy_attachment.attach_policy: Creating...
aws_iam_instance_profile.xfusion_profile: Creating...
aws_iam_role_policy_attachment.attach_policy: Creation complete after 0s [id=xfusion-role-20260127105344156700000001]
aws_s3_bucket.logs_bucket: Creation complete after 1s [id=xfusion-logs-8687]
aws_iam_instance_profile.xfusion_profile: Creation complete after 5s [id=xfusion-instance-profile]
aws_instance.xfusion_ec2: Creating...
aws_instance.xfusion_ec2: Still creating... [10s elapsed]
aws_instance.xfusion_ec2: Creation complete after 11s [id=i-21a696aa51c991f4f]

Apply complete! Resources: 6 added, 0 changed, 0 destroyed.
```
---

Step-by-Step Explanation (Why & What Happens)

Let’s break this down simply.

🔹 Why use IAM Role instead of access keys?

Because:

Access keys can leak ❌

Roles are temporary & secure ✅

AWS best practice ✅

EC2 automatically gets credentials from the role.

🔹 What is happening conceptually?

Think of it like this:

EC2 = 🖥️ application server

S3 = 📦 log storage

IAM Role = 🪪 identity card

IAM Policy = 📜 permission rules

🔹 Step 1: Create the S3 bucket
resource "aws_s3_bucket"


This is where logs will be uploaded.

🔹 Step 2: Create IAM Role
aws_iam_role


This says:

“EC2 instances are allowed to assume this role.”

Without this, EC2 cannot use the role.

🔹 Step 3: Create IAM Policy
aws_iam_policy


Policy allows:

s3:PutObject → xfusion-logs-8687/*


Meaning:

EC2 can upload files, but nothing else.

🔐 Least-privilege access.

🔹 Step 4: Attach policy to role

This connects:

Role → Policy


Now the role has permission.

🔹 Step 5: Instance Profile (IMPORTANT)

EC2 cannot use roles directly.

AWS requires:

EC2 → Instance Profile → IAM Role


That’s why this exists:

aws_iam_instance_profile

🔹 Step 6: Launch EC2 with role attached
iam_instance_profile = ...


Now:

EC2 boots

AWS injects temporary credentials

App can upload logs to S3

No passwords, no keys

🔹 What happens during terraform apply?

1️⃣ Terraform finds latest AMI
2️⃣ Creates S3 bucket
3️⃣ Creates IAM role
4️⃣ Creates IAM policy
5️⃣ Attaches policy to role
6️⃣ Creates instance profile
7️⃣ Launches EC2 with role
8️⃣ Saves everything in state

🧠 Easy Memory Trick

Policy = what you can do

Role = who you are

Instance profile = how EC2 gets the role

S3 PutObject = upload logs

🚨 Common Mistakes (avoided them)

❌ Using access keys
❌ Forgetting instance profile
❌ Wrong S3 ARN (/* missing)
❌ Hardcoding AMI
❌ Creating resources in wrong files

---