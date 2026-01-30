1️⃣ locals.tf


```
locals {
  KKE_SNS_TOPIC_NAME = "datacenter-sns-topic"
  KKE_ROLE_NAME      = "datacenter-sns-role"
  KKE_POLICY_NAME    = "datacenter-sns-policy"
}
```

2️⃣ main.tf

```
# SNS Topic
resource "aws_sns_topic" "datacenter_topic" {
  name = local.KKE_SNS_TOPIC_NAME
}

# IAM Role (trusted by EC2)
resource "aws_iam_role" "datacenter_role" {
  name = local.KKE_ROLE_NAME

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

# IAM Policy (allow publish to SNS topic)
resource "aws_iam_policy" "datacenter_policy" {
  name = local.KKE_POLICY_NAME

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect   = "Allow"
      Action   = ["sns:Publish"]
      Resource = aws_sns_topic.datacenter_topic.arn
    }]
  })
}

# Attach policy to role
resource "aws_iam_role_policy_attachment" "attach_policy" {
  role       = aws_iam_role.datacenter_role.name
  policy_arn = aws_iam_policy.datacenter_policy.arn
}
```

3️⃣ outputs.tf


```
output "kke_sns_topic_name" {
  value = aws_sns_topic.datacenter_topic.name
}

output "kke_role_name" {
  value = aws_iam_role.datacenter_role.name
}

output "kke_policy_name" {
  value = aws_iam_policy.datacenter_policy.name
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
bob@iac-server ~/terraform via 💠 default ➜  terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_iam_policy.datacenter_policy will be created
  + resource "aws_iam_policy" "datacenter_policy" {
      + arn              = (known after apply)
      + attachment_count = (known after apply)
      + id               = (known after apply)
      + name             = "datacenter-sns-policy"
      + name_prefix      = (known after apply)
      + path             = "/"
      + policy           = (known after apply)
      + policy_id        = (known after apply)
      + tags_all         = (known after apply)
    }

  # aws_iam_role.datacenter_role will be created
  + resource "aws_iam_role" "datacenter_role" {
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
      + name                  = "datacenter-sns-role"
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
      + role       = "datacenter-sns-role"
    }

  # aws_sns_topic.datacenter_topic will be created
  + resource "aws_sns_topic" "datacenter_topic" {
      + arn                         = (known after apply)
      + beginning_archive_time      = (known after apply)
      + content_based_deduplication = false
      + fifo_topic                  = false
      + id                          = (known after apply)
      + name                        = "datacenter-sns-topic"
      + name_prefix                 = (known after apply)
      + owner                       = (known after apply)
      + policy                      = (known after apply)
      + signature_version           = (known after apply)
      + tags_all                    = (known after apply)
      + tracing_config              = (known after apply)
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_policy_name    = "datacenter-sns-policy"
  + kke_role_name      = "datacenter-sns-role"
  + kke_sns_topic_name = "datacenter-sns-topic"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_iam_role.datacenter_role: Creating...
aws_sns_topic.datacenter_topic: Creating...
aws_iam_role.datacenter_role: Creation complete after 1s [id=datacenter-sns-role]
aws_sns_topic.datacenter_topic: Creation complete after 1s [id=arn:aws:sns:us-east-1:000000000000:datacenter-sns-topic]
aws_iam_policy.datacenter_policy: Creating...
aws_iam_policy.datacenter_policy: Creation complete after 0s [id=arn:aws:iam::000000000000:policy/datacenter-sns-policy]
aws_iam_role_policy_attachment.attach_policy: Creating...
aws_iam_role_policy_attachment.attach_policy: Creation complete after 0s [id=datacenter-sns-role-20260130141903393400000001]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

Outputs:

kke_policy_name = "datacenter-sns-policy"
kke_role_name = "datacenter-sns-role"
kke_sns_topic_name = "datacenter-sns-topic"
```

🧠 Step-by-Step Explanation (Simple & Clear)

Let’s break this down in plain English.

🔹 What problem is this lab solving?

EC2 instances need to:

Publish messages to SNS

Do it securely

Without access keys

IAM roles + policies are the correct solution.

🔹 Why use locals.tf instead of variables?

locals are:

Fixed values

Defined once

Reusable across files

This lab wants static names, not user input.

Think:

variables → user-supplied

locals → internally defined constants

🔹 SNS Topic
aws_sns_topic


This is the message publisher endpoint.

EC2 will send messages here using:

sns:Publish

🔹 IAM Role (EC2 trusted)
aws_iam_role


The trust policy says:

“EC2 instances are allowed to assume this role.”

Without this, EC2 cannot use the role.

🔹 IAM Policy
aws_iam_policy


Policy allows:

"sns:Publish"


And only to:

datacenter-sns-topic


That’s least privilege 🔐
No subscribe, no delete, no list — publish only.

🔹 Attach policy to role
aws_iam_role_policy_attachment


This connects:

Role → Permissions


Now the role actually works.

🔹 What happens during terraform apply?

1️⃣ Terraform creates SNS topic
2️⃣ Terraform creates IAM role
3️⃣ Terraform creates IAM policy
4️⃣ Terraform attaches policy to role
5️⃣ AWS enforces permissions
6️⃣ Terraform outputs resource names

🧠 Easy Memory Model

SNS topic = 📣 message destination

IAM role = 👤 identity for EC2

IAM policy = 🔑 permission rules

Attachment = 🔗 glue

locals = 📌 fixed constants

🚨 Common Mistakes 

❌ Using variables instead of locals
❌ Allowing sns:* instead of sns:Publish
❌ Using * for resource ARN
❌ Forgetting trust policy
❌ Output name mismatch

---