1️⃣ variables.tf


```
variable "KKE_SECRET_NAME" {
  type = string
}

variable "KKE_SECRET_VALUE" {
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
KKE_SECRET_NAME  = "nautilus-app-secret"
KKE_SECRET_VALUE = "{\"db_user\":\"admin\",\"db_pass\":\"supersecret\"}"
KKE_ROLE_NAME    = "nautilus-app-role"
KKE_POLICY_NAME  = "nautilus-app-policy"
```

⚠️ The JSON string must be escaped properly — this is important.

3️⃣ main.tf

```
provider "aws" {
  region = "us-east-1"
}

# Create Secrets Manager secret
resource "aws_secretsmanager_secret" "app_secret" {
  name = var.KKE_SECRET_NAME
}

# Store secret value
resource "aws_secretsmanager_secret_version" "app_secret_value" {
  secret_id     = aws_secretsmanager_secret.app_secret.id
  secret_string = var.KKE_SECRET_VALUE
}

# IAM Role (trusted by EC2)
resource "aws_iam_role" "app_role" {
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

# Inline IAM Policy (read secret only)
resource "aws_iam_role_policy" "app_inline_policy" {
  name = var.KKE_POLICY_NAME
  role = aws_iam_role.app_role.id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect = "Allow"
      Action = [
        "secretsmanager:GetSecretValue",
        "secretsmanager:DescribeSecret"
      ]
      Resource = aws_secretsmanager_secret.app_secret.arn
    }]
  })
}
```


4️⃣ outputs.tf


```
output "KKE_secret_name" {
  value = aws_secretsmanager_secret.app_secret.name
}

output "KKE_role_name" {
  value = aws_iam_role.app_role.name
}

output "KKE_policy_name" {
  value = aws_iam_role_policy.app_inline_policy.name
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

  # aws_iam_role.app_role will be created
  + resource "aws_iam_role" "app_role" {
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
      + name                  = "nautilus-app-role"
      + name_prefix           = (known after apply)
      + path                  = "/"
      + tags_all              = (known after apply)
      + unique_id             = (known after apply)

      + inline_policy (known after apply)
    }

  # aws_iam_role_policy.app_inline_policy will be created
  + resource "aws_iam_role_policy" "app_inline_policy" {
      + id          = (known after apply)
      + name        = "nautilus-app-policy"
      + name_prefix = (known after apply)
      + policy      = (known after apply)
      + role        = (known after apply)
    }

  # aws_secretsmanager_secret.app_secret will be created
  + resource "aws_secretsmanager_secret" "app_secret" {
      + arn                            = (known after apply)
      + force_overwrite_replica_secret = false
      + id                             = (known after apply)
      + name                           = "nautilus-app-secret"
      + name_prefix                    = (known after apply)
      + policy                         = (known after apply)
      + recovery_window_in_days        = 30
      + tags_all                       = (known after apply)

      + replica (known after apply)
    }

  # aws_secretsmanager_secret_version.app_secret_value will be created
  + resource "aws_secretsmanager_secret_version" "app_secret_value" {
      + arn                  = (known after apply)
      + has_secret_string_wo = (known after apply)
      + id                   = (known after apply)
      + secret_id            = (known after apply)
      + secret_string        = (sensitive value)
      + secret_string_wo     = (write-only attribute)
      + version_id           = (known after apply)
      + version_stages       = (known after apply)
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + KKE_policy_name = "nautilus-app-policy"
  + KKE_role_name   = "nautilus-app-role"
  + KKE_secret_name = "nautilus-app-secret"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_secretsmanager_secret.app_secret: Creating...
aws_iam_role.app_role: Creating...
aws_secretsmanager_secret.app_secret: Creation complete after 1s [id=arn:aws:secretsmanager:us-east-1:000000000000:secret:nautilus-app-secret-XTRWVy]
aws_secretsmanager_secret_version.app_secret_value: Creating...
aws_secretsmanager_secret_version.app_secret_value: Creation complete after 0s [id=arn:aws:secretsmanager:us-east-1:000000000000:secret:nautilus-app-secret-XTRWVy|terraform-20260130144343211100000002]
aws_iam_role.app_role: Creation complete after 1s [id=nautilus-app-role]
aws_iam_role_policy.app_inline_policy: Creating...
aws_iam_role_policy.app_inline_policy: Creation complete after 0s [id=nautilus-app-role:nautilus-app-policy]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

Outputs:

KKE_policy_name = "nautilus-app-policy"
KKE_role_name = "nautilus-app-role"
KKE_secret_name = "nautilus-app-secret"
```

---

🧠 Step-by-Step Explanation (Simple & Clear)

Let’s understand what you built and why.

🔹 Why use AWS Secrets Manager?

Secrets Manager is used to store:

Database passwords

API keys

Credentials

Benefits:

Encrypted at rest

IAM-controlled access

No secrets in code

🔹 Why split secret & secret version?

Terraform requires:

aws_secretsmanager_secret → container

aws_secretsmanager_secret_version → actual value

This allows:

Secret rotation

Versioning

Updates without deleting the secret

🔹 Why an IAM Role?

EC2 instances should:

Access secrets without access keys

Use temporary credentials

IAM Role does exactly that.

🔹 Why an inline IAM policy?

Inline policy means:

Policy is embedded inside the role

Cannot be reused elsewhere

Strong binding → perfect for sensitive access

The lab explicitly asked for this.

🔹 Why only these permissions?
"secretsmanager:GetSecretValue"
"secretsmanager:DescribeSecret"


These allow:

Reading the secret

Nothing else

❌ No delete
❌ No update
❌ No list all secrets

This is least-privilege access 🔐

🔹 Why restrict policy to one secret?
Resource = aws_secretsmanager_secret.app_secret.arn


This ensures:

Role can access only xfusion-app-secret

Other secrets stay protected

🔹 What happens during terraform apply?

1️⃣ Terraform creates the secret
2️⃣ Stores the secret value securely
3️⃣ Creates IAM role
4️⃣ Attaches inline policy to role
5️⃣ AWS enforces permissions
6️⃣ Terraform outputs names

🧠 Easy Memory Model

Secret = 🔒 sensitive data

Secret version = 📄 actual value

IAM role = 👤 identity

Inline policy = 🔑 tightly bound permission

EC2 = 🖥️ consumer

🚨 Common Mistakes 

❌ Using managed policy instead of inline
❌ Granting secretsmanager:*
❌ Using * for resource
❌ Forgetting JSON escaping
❌ Output name mismatch

---