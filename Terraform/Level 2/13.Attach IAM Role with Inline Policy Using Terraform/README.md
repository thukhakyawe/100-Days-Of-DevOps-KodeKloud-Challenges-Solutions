1️⃣ variables.tf


```
variable "KKE_ROLE_NAME" {
  type = string
}

variable "KKE_POLICY_NAME" {
  type = string
}
```

2️⃣ terraform.tfvars

```
KKE_ROLE_NAME   = "devops-role"
KKE_POLICY_NAME = "devops-policy"
```

3️⃣ main.tf

```
# IAM Role
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

# IAM Policy (allow listing EC2 instances)
resource "aws_iam_policy" "devops_policy" {
  name = var.KKE_POLICY_NAME

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect   = "Allow"
      Action   = [
        "ec2:DescribeInstances"
      ]
      Resource = "*"
    }]
  })
}

# Attach policy to role
resource "aws_iam_role_policy_attachment" "attach_policy" {
  role       = aws_iam_role.devops_role.name
  policy_arn = aws_iam_policy.devops_policy.arn
}
```

4️⃣ outputs.tf

```
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
bob@iac-server ~/terraform via 💠 default ➜  terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_iam_policy.devops_policy will be created
  + resource "aws_iam_policy" "devops_policy" {
      + arn              = (known after apply)
      + attachment_count = (known after apply)
      + id               = (known after apply)
      + name             = "devops-policy"
      + name_prefix      = (known after apply)
      + path             = "/"
      + policy           = jsonencode(
            {
              + Statement = [
                  + {
                      + Action   = [
                          + "ec2:DescribeInstances",
                        ]
                      + Effect   = "Allow"
                      + Resource = "*"
                    },
                ]
              + Version   = "2012-10-17"
            }
        )
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
      + name                  = "devops-role"
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
      + role       = "devops-role"
    }

Plan: 3 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_iam_policy_name = "devops-policy"
  + kke_iam_role_name   = "devops-role"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_iam_policy.devops_policy: Creating...
aws_iam_role.devops_role: Creating...
aws_iam_policy.devops_policy: Creation complete after 1s [id=arn:aws:iam::000000000000:policy/devops-policy]
aws_iam_role.devops_role: Creation complete after 1s [id=devops-role]
aws_iam_role_policy_attachment.attach_policy: Creating...
aws_iam_role_policy_attachment.attach_policy: Creation complete after 0s [id=devops-role-20260128104355055200000001]

Apply complete! Resources: 3 added, 0 changed, 0 destroyed.

Outputs:

kke_iam_policy_name = "devops-policy"
kke_iam_role_name = "devops-role"
```

---

🧠 Step-by-Step Explanation (Simple & Clear)

Let’s explain what you built and why.

🔹 What problem is this lab solving?

The team wants:

Controlled access to AWS

No hard-coded credentials

Permissions defined centrally

IAM Roles + Policies solve this cleanly.

🔹 IAM Role vs IAM Policy (important)

Think of it like this:

Policy = 📜 What actions are allowed

Role = 🪪 Who can use those actions

Attachment = 🔗 Connects them

A role without a policy → useless
A policy without a role → unused

You need both.

🔹 Step 1: IAM Role
aws_iam_role


This role:

Can be assumed by EC2 (ec2.amazonaws.com)

Does nothing by itself yet

The assume_role_policy answers:

“Who is allowed to use this role?”

🔹 Step 2: IAM Policy
aws_iam_policy


This policy allows:

"ec2:DescribeInstances"


Meaning:

List EC2 instances

Read-only access

No create / delete / modify

This is least privilege ✅

🔹 Step 3: Attach Policy to Role
aws_iam_role_policy_attachment


This is the glue:

Role now inherits permissions

AWS enforces them automatically

🔹 What happens during terraform apply?

1️⃣ Terraform creates IAM role
2️⃣ Terraform creates IAM policy
3️⃣ Terraform attaches policy to role
4️⃣ AWS enforces permissions
5️⃣ Terraform outputs names

🧠 Easy Memory Model

Role = 👤 identity

Policy = 🔑 permissions

Attachment = 🔗 link

🚨 Common Mistakes 

❌ Forgetting assume role policy
❌ Using wrong EC2 action
❌ Hardcoding names
❌ Missing attachment
❌ Output name mismatch

---