🧭 Part 1: Lab Step-by-Step Guidelines



Step 1 — Create Required Files

Inside your Terraform root directory:

main.tf
variables.tf
terraform.tfvars
outputs.tf

Step 2 — Define Sensitive Variable
📄 variables.tf

```
variable "KKE_DB_PASSWORD" {
  description = "Database password stored in AWS Secrets Manager"
  type        = string
  sensitive   = true
}
```

✔ sensitive = true ensures:

Terraform CLI does NOT display it in plan/apply output

It is redacted in logs

Step 3 — Create the Secret Resource

📄 main.tf

```
# Create secret metadata
resource "aws_secretsmanager_secret" "kke_secret" {
  name = "xfusion-db-password"

  tags = {
    Project = "SecretsManagement"
  }
}

# Store the secret value
resource "aws_secretsmanager_secret_version" "kke_secret_value" {
  secret_id     = aws_secretsmanager_secret.kke_secret.id
  secret_string = var.KKE_DB_PASSWORD
}
```

Step 4 — Provide Password via tfvars

📄 terraform.tfvars

```
KKE_DB_PASSWORD = "SuperSecretPassword123!"
```

✔ This avoids hardcoding the password inside main.tf.

Step 5 — Define Outputs

📄 outputs.tf

```
output "kke_secret_arn" {
  description = "ARN of the created secret"
  value       = aws_secretsmanager_secret.kke_secret.arn
}

output "kke_secret_string" {
  description = "Database password stored in the secret"
  value       = var.KKE_DB_PASSWORD
  sensitive   = true
}
```

⚠ The password output is marked sensitive = true, which prevents it from being displayed in plain text in normal Terraform output.

Step 6 — Deploy

From the Terraform directory:

terraform init
terraform validate
terraform plan
terraform apply -auto-approve

Step 7 — Verify

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply -auto-approve

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_secretsmanager_secret.kke_secret will be created
  + resource "aws_secretsmanager_secret" "kke_secret" {
      + arn                            = (known after apply)
      + force_overwrite_replica_secret = false
      + id                             = (known after apply)
      + name                           = "xfusion-db-password"
      + name_prefix                    = (known after apply)
      + policy                         = (known after apply)
      + recovery_window_in_days        = 30
      + tags                           = {
          + "Project" = "SecretsManagement"
        }
      + tags_all                       = {
          + "Project" = "SecretsManagement"
        }

      + replica (known after apply)
    }

  # aws_secretsmanager_secret_version.kke_secret_value will be created
  + resource "aws_secretsmanager_secret_version" "kke_secret_value" {
      + arn                  = (known after apply)
      + has_secret_string_wo = (known after apply)
      + id                   = (known after apply)
      + secret_id            = (known after apply)
      + secret_string        = (sensitive value)
      + secret_string_wo     = (write-only attribute)
      + version_id           = (known after apply)
      + version_stages       = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_secret_arn    = (known after apply)
  + kke_secret_string = (sensitive value)
aws_secretsmanager_secret.kke_secret: Creating...
aws_secretsmanager_secret.kke_secret: Creation complete after 0s [id=arn:aws:secretsmanager:us-east-1:000000000000:secret:xfusion-db-password-PQqjSM]
aws_secretsmanager_secret_version.kke_secret_value: Creating...
aws_secretsmanager_secret_version.kke_secret_value: Creation complete after 0s [id=arn:aws:secretsmanager:us-east-1:000000000000:secret:xfusion-db-password-PQqjSM|terraform-20260214054755040800000002]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

Outputs:

kke_secret_arn = "arn:aws:secretsmanager:us-east-1:000000000000:secret:xfusion-db-password-PQqjSM"
kke_secret_string = <sensitive>
```

✔ The password will NOT appear in plaintext.

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
What We Just Did

We created:

A secure storage vault (Secrets Manager secret)

Stored a database password inside it

Made sure Terraform does not print the password openly

Why Use Secrets Manager?

Instead of putting passwords in:

Code

Git repositories

Environment files

We store them in a managed encrypted service.

AWS automatically encrypts the value.

What Does “Sensitive Variable” Mean?

When we wrote:

sensitive = true


Terraform understands:

“This is confidential. Do not print it.”

So during terraform plan or apply, you’ll see:

(sensitive value)


instead of:

SuperSecretPassword123!

Why We Used terraform.tfvars

We separated:

Infrastructure logic → main.tf
Secrets input → terraform.tfvars

This is real-world DevOps practice.

In production, instead of terraform.tfvars, you would:

Use environment variables

Use a CI/CD secret store

Use Vault

Why We Used aws_secretsmanager_secret_version

Secrets Manager separates:

Secret metadata (name, ARN)

Secret value (versioned)

This allows:

Password rotation

Multiple versions

Controlled updates

Security Architecture Overview

Terraform
→ Creates secret container
→ Uploads password securely
→ Stores encrypted value
→ Redacts it from CLI

No plaintext exposure in logs.