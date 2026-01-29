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
KKE_TABLE_NAME  = "datacenter-table"
KKE_ROLE_NAME   = "datacenter-role"
KKE_POLICY_NAME = "datacenter-readonly-policy"
```

3️⃣ main.tf

```
# DynamoDB table (minimal configuration)
resource "aws_dynamodb_table" "datacenter_table" {
  name         = var.KKE_TABLE_NAME
  billing_mode = "PAY_PER_REQUEST"

  hash_key = "id"

  attribute {
    name = "id"
    type = "S"
  }
}

# IAM Role
resource "aws_iam_role" "datacenter_role" {
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

# IAM Policy (read-only access to specific DynamoDB table)
resource "aws_iam_policy" "datacenter_policy" {
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
      Resource = aws_dynamodb_table.datacenter_table.arn
    }]
  })
}

# Attach policy to role
resource "aws_iam_role_policy_attachment" "attach_policy" {
  role       = aws_iam_role.datacenter_role.name
  policy_arn = aws_iam_policy.datacenter_policy.arn
}
```

4️⃣ outputs.tf


```
output "kke_dynamodb_table" {
  value = aws_dynamodb_table.datacenter_table.name
}

output "kke_iam_role_name" {
  value = aws_iam_role.datacenter_role.name
}

output "kke_iam_policy_name" {
  value = aws_iam_policy.datacenter_policy.name
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

  # aws_dynamodb_table.datacenter_table will be created
  + resource "aws_dynamodb_table" "datacenter_table" {
      + arn              = (known after apply)
      + billing_mode     = "PAY_PER_REQUEST"
      + hash_key         = "id"
      + id               = (known after apply)
      + name             = "datacenter-table"
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

  # aws_iam_policy.datacenter_policy will be created
  + resource "aws_iam_policy" "datacenter_policy" {
      + arn              = (known after apply)
      + attachment_count = (known after apply)
      + id               = (known after apply)
      + name             = "datacenter-readonly-policy"
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
      + name                  = "datacenter-role"
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
      + role       = "datacenter-role"
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_dynamodb_table  = "datacenter-table"
  + kke_iam_policy_name = "datacenter-readonly-policy"
  + kke_iam_role_name   = "datacenter-role"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_iam_role.datacenter_role: Creating...
aws_dynamodb_table.datacenter_table: Creating...
aws_iam_role.datacenter_role: Creation complete after 0s [id=datacenter-role]
aws_dynamodb_table.datacenter_table: Creation complete after 3s [id=datacenter-table]
aws_iam_policy.datacenter_policy: Creating...
aws_iam_policy.datacenter_policy: Creation complete after 0s [id=arn:aws:iam::000000000000:policy/datacenter-readonly-policy]
aws_iam_role_policy_attachment.attach_policy: Creating...
aws_iam_role_policy_attachment.attach_policy: Creation complete after 0s [id=datacenter-role-20260129134119333700000001]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

Outputs:

kke_dynamodb_table = "datacenter-table"
kke_iam_policy_name = "datacenter-readonly-policy"
kke_iam_role_name = "datacenter-role"
```


---

🧠 Step-by-Step Explanation (Simple & Clear)

Let’s understand what you built and why.

🔹 Why PAY_PER_REQUEST for DynamoDB?
billing_mode = "PAY_PER_REQUEST"


This means:

No capacity planning needed

Minimal configuration

Cheapest & simplest

Perfect for labs and light workloads

🔹 Why does DynamoDB need a hash key?

Every DynamoDB table must have:

At least one primary key

We used:

hash_key = "id"


This keeps the table minimal and valid.

🔹 IAM Role vs IAM Policy (again, very important)

Think of it like this:

IAM Policy → 📜 What actions are allowed

IAM Role → 🪪 Who gets those permissions

Attachment → 🔗 Connects them

🔹 Why only these DynamoDB actions?
"dynamodb:GetItem"
"dynamodb:Scan"
"dynamodb:Query"


These are read-only operations:

No writes

No deletes

No table changes

This is least-privilege access 🔐

🔹 Why restrict policy to one table?
Resource = aws_dynamodb_table.datacenter_table.arn


This ensures:

Role can access only datacenter-table

Cannot read other DynamoDB tables

Strong security boundary

🔹 What happens during terraform apply?

1️⃣ Terraform creates DynamoDB table
2️⃣ Terraform creates IAM role
3️⃣ Terraform creates IAM policy
4️⃣ Terraform attaches policy to role
5️⃣ AWS enforces read-only access
6️⃣ Terraform outputs resource names

🧠 Easy Memory Model

DynamoDB table = 📦 data store

IAM role = 👤 identity

IAM policy = 🔑 permissions

Attachment = 🔗 connection

Read-only = 👀 safe access

🚨 Common Mistakes 

❌ Using * for DynamoDB resource
❌ Granting write permissions
❌ Forgetting hash key
❌ Hardcoding names
❌ Output name mismatch

---