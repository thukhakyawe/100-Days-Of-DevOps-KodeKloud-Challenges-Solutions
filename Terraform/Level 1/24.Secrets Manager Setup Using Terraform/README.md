# Step 1: Create Main Terraform Configuration

```
# main.tf

# Create Secrets Manager secret for storing sensitive data
resource "aws_secretsmanager_secret" "xfusion" {
  name = "xfusion-secret"
}

# Create secret version with key-value pair
resource "aws_secretsmanager_secret_version" "xfusion_value" {
  secret_id     = aws_secretsmanager_secret.xfusion.id
  secret_string = jsonencode({
    username = "admin"
    password = "Namin123"
  })
}
```

# Step2: To deploy this configuration

# Navigate to the Terraform directory:

```
cd /home/bob/terraform
```

# Initialize Terraform:

```
terraform init
```

Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform init
Initializing the backend...
Initializing provider plugins...
- Finding hashicorp/aws versions matching "5.91.0"...
- Installing hashicorp/aws v5.91.0...
- Installed hashicorp/aws v5.91.0 (signed by HashiCorp)
Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

# Plan the deployment to verify the configuration:

```
terraform plan
```

Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform plan

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_secretsmanager_secret.xfusion will be created
  + resource "aws_secretsmanager_secret" "xfusion" {
      + arn                            = (known after apply)
      + force_overwrite_replica_secret = false
      + id                             = (known after apply)
      + name                           = "xfusion-secret"
      + name_prefix                    = (known after apply)
      + policy                         = (known after apply)
      + recovery_window_in_days        = 30
      + tags_all                       = (known after apply)

      + replica (known after apply)
    }

  # aws_secretsmanager_secret_version.xfusion_value will be created
  + resource "aws_secretsmanager_secret_version" "xfusion_value" {
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

─────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to
take exactly these actions if you run "terraform apply" now.
```


# Apply the configuration:

```
terraform apply
```

Then type `yes` when prompted to confirm the creation of the snapshot.


Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_secretsmanager_secret.xfusion will be created
  + resource "aws_secretsmanager_secret" "xfusion" {
      + arn                            = (known after apply)
      + force_overwrite_replica_secret = false
      + id                             = (known after apply)
      + name                           = "xfusion-secret"
      + name_prefix                    = (known after apply)
      + policy                         = (known after apply)
      + recovery_window_in_days        = 30
      + tags_all                       = (known after apply)

      + replica (known after apply)
    }

  # aws_secretsmanager_secret_version.xfusion_value will be created
  + resource "aws_secretsmanager_secret_version" "xfusion_value" {
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

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_secretsmanager_secret.xfusion: Creating...
aws_secretsmanager_secret.xfusion: Creation complete after 0s [id=arn:aws:secretsmanager:us-east-1:000000000000:secret:xfusion-secret-YpVXwh]
aws_secretsmanager_secret_version.xfusion_value: Creating...
aws_secretsmanager_secret_version.xfusion_value: Creation complete after 0s [id=arn:aws:secretsmanager:us-east-1:000000000000:secret:xfusion-secret-YpVXwh|terraform-20251105163140204000000002]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

***
