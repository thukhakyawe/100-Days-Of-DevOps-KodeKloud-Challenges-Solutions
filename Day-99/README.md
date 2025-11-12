
# Step 1: Create Variables Terraform Configuration

```
# variables.tf

variable "KKE_TABLE_NAME" {
  description = "Name of the DynamoDB table"
  type        = string
}

variable "KKE_ROLE_NAME" {
  description = "Name of the IAM role"
  type        = string
}

variable "KKE_POLICY_NAME" {
  description = "Name of the IAM policy"
  type        = string
}
```

# Step 2: Create Main Terraform Configuration

```
# main.tf

# Create DynamoDB table
resource "aws_dynamodb_table" "nautilus_table" {
  name           = var.KKE_TABLE_NAME
  billing_mode   = "PAY_PER_REQUEST"
  hash_key       = "id"

  attribute {
    name = "id"
    type = "S"
  }

  tags = {
    Name = var.KKE_TABLE_NAME
  }
}

# Create IAM role
resource "aws_iam_role" "nautilus_role" {
  name = var.KKE_ROLE_NAME

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Principal = {
          Service = "ec2.amazonaws.com"
        }
      }
    ]
  })

  tags = {
    Name = var.KKE_ROLE_NAME
  }
}

# Create IAM policy for read-only DynamoDB access
resource "aws_iam_policy" "nautilus_readonly_policy" {
  name        = var.KKE_POLICY_NAME
  description = "Read-only access policy for DynamoDB table"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "dynamodb:GetItem",
          "dynamodb:Scan",
          "dynamodb:Query"
        ]
        Resource = aws_dynamodb_table.nautilus_table.arn
      }
    ]
  })

  tags = {
    Name = var.KKE_POLICY_NAME
  }
}

# Attach policy to role
resource "aws_iam_role_policy_attachment" "nautilus_policy_attachment" {
  role       = aws_iam_role.nautilus_role.name
  policy_arn = aws_iam_policy.nautilus_readonly_policy.arn
}
```

# Step 3: Create Outputs Terraform Configuration


```
# outputs.tf

output "kke_dynamodb_table" {
  description = "Name of the DynamoDB table"
  value       = aws_dynamodb_table.nautilus_table.name
}

output "kke_iam_role_name" {
  description = "Name of the IAM role"
  value       = aws_iam_role.nautilus_role.name
}

output "kke_iam_policy_name" {
  description = "Name of the IAM policy"
  value       = aws_iam_policy.nautilus_readonly_policy.name
}
```

# Step 4: Create Outputs Terraform Configuration

```
# terraform.tfvars

KKE_TABLE_NAME  = "nautilus-table"
KKE_ROLE_NAME   = "nautilus-role"
KKE_POLICY_NAME = "nautilus-readonly-policy"
```



# Step 5: To deploy this configuration

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

  # aws_dynamodb_table.nautilus_table will be created
  + resource "aws_dynamodb_table" "nautilus_table" {
      + arn              = (known after apply)
      + billing_mode     = "PAY_PER_REQUEST"
      + hash_key         = "id"
      + id               = (known after apply)
      + name             = "nautilus-table"
      + read_capacity    = (known after apply)
      + stream_arn       = (known after apply)
      + stream_label     = (known after apply)
      + stream_view_type = (known after apply)
      + tags             = {
          + "Name" = "nautilus-table"
        }
      + tags_all         = {
          + "Name" = "nautilus-table"
        }
      + write_capacity   = (known after apply)

      + attribute {
          + name = "id"
          + type = "S"
        }

      + point_in_time_recovery (known after apply)

      + server_side_encryption (known after apply)

      + ttl (known after apply)
    }

  # aws_iam_policy.nautilus_readonly_policy will be created
  + resource "aws_iam_policy" "nautilus_readonly_policy" {
      + arn              = (known after apply)
      + attachment_count = (known after apply)
      + description      = "Read-only access policy for DynamoDB table"
      + id               = (known after apply)
      + name             = "nautilus-readonly-policy"
      + name_prefix      = (known after apply)
      + path             = "/"
      + policy           = (known after apply)
      + policy_id        = (known after apply)
      + tags             = {
          + "Name" = "nautilus-readonly-policy"
        }
      + tags_all         = {
          + "Name" = "nautilus-readonly-policy"
        }
    }

  # aws_iam_role.nautilus_role will be created
  + resource "aws_iam_role" "nautilus_role" {
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
      + name                  = "nautilus-role"
      + name_prefix           = (known after apply)
      + path                  = "/"
      + tags                  = {
          + "Name" = "nautilus-role"
        }
      + tags_all              = {
          + "Name" = "nautilus-role"
        }
      + unique_id             = (known after apply)

      + inline_policy (known after apply)
    }

  # aws_iam_role_policy_attachment.nautilus_policy_attachment will be created
  + resource "aws_iam_role_policy_attachment" "nautilus_policy_attachment" {
      + id         = (known after apply)
      + policy_arn = (known after apply)
      + role       = "nautilus-role"
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_dynamodb_table  = "nautilus-table"
  + kke_iam_policy_name = "nautilus-readonly-policy"
  + kke_iam_role_name   = "nautilus-role"

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

  # aws_dynamodb_table.nautilus_table will be created
  + resource "aws_dynamodb_table" "nautilus_table" {
      + arn              = (known after apply)
      + billing_mode     = "PAY_PER_REQUEST"
      + hash_key         = "id"
      + id               = (known after apply)
      + name             = "nautilus-table"
      + read_capacity    = (known after apply)
      + stream_arn       = (known after apply)
      + stream_label     = (known after apply)
      + stream_view_type = (known after apply)
      + tags             = {
          + "Name" = "nautilus-table"
        }
      + tags_all         = {
          + "Name" = "nautilus-table"
        }
      + write_capacity   = (known after apply)

      + attribute {
          + name = "id"
          + type = "S"
        }

      + point_in_time_recovery (known after apply)

      + server_side_encryption (known after apply)

      + ttl (known after apply)
    }

  # aws_iam_policy.nautilus_readonly_policy will be created
  + resource "aws_iam_policy" "nautilus_readonly_policy" {
      + arn              = (known after apply)
      + attachment_count = (known after apply)
      + description      = "Read-only access policy for DynamoDB table"
      + id               = (known after apply)
      + name             = "nautilus-readonly-policy"
      + name_prefix      = (known after apply)
      + path             = "/"
      + policy           = (known after apply)
      + policy_id        = (known after apply)
      + tags             = {
          + "Name" = "nautilus-readonly-policy"
        }
      + tags_all         = {
          + "Name" = "nautilus-readonly-policy"
        }
    }

  # aws_iam_role.nautilus_role will be created
  + resource "aws_iam_role" "nautilus_role" {
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
      + name                  = "nautilus-role"
      + name_prefix           = (known after apply)
      + path                  = "/"
      + tags                  = {
          + "Name" = "nautilus-role"
        }
      + tags_all              = {
          + "Name" = "nautilus-role"
        }
      + unique_id             = (known after apply)

      + inline_policy (known after apply)
    }

  # aws_iam_role_policy_attachment.nautilus_policy_attachment will be created
  + resource "aws_iam_role_policy_attachment" "nautilus_policy_attachment" {
      + id         = (known after apply)
      + policy_arn = (known after apply)
      + role       = "nautilus-role"
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_dynamodb_table  = "nautilus-table"
  + kke_iam_policy_name = "nautilus-readonly-policy"
  + kke_iam_role_name   = "nautilus-role"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_iam_role.nautilus_role: Creating...
aws_dynamodb_table.nautilus_table: Creating...
aws_iam_role.nautilus_role: Creation complete after 0s [id=nautilus-role]
aws_dynamodb_table.nautilus_table: Creation complete after 3s [id=nautilus-table]
aws_iam_policy.nautilus_readonly_policy: Creating...
aws_iam_policy.nautilus_readonly_policy: Creation complete after 0s [id=arn:aws:iam::000000000000:policy/nautilus-readonly-policy]
aws_iam_role_policy_attachment.nautilus_policy_attachment: Creating...
aws_iam_role_policy_attachment.nautilus_policy_attachment: Creation complete after 0s [id=nautilus-role-20251112152603559400000001]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

Outputs:

kke_dynamodb_table = "nautilus-table"
kke_iam_policy_name = "nautilus-readonly-policy"
kke_iam_role_name = "nautilus-role"
```

***
