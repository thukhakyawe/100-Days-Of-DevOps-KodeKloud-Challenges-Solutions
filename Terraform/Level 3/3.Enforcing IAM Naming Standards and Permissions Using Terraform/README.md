✅ Part 1: Lab Step-by-Step Guidelines



Step 1: Create main.tf

```
provider "aws" {
  region = "us-east-1"
}

# -------------------------
# Locals: Naming & Tags
# -------------------------
locals {
  sanitized_project = lower(
    replace(var.KKE_PROJECT, "_", "-")
  )

  sanitized_team = lower(
    replace(var.KKE_TEAM, "_", "-")
  )

  name_prefix = "${local.sanitized_project}-${local.sanitized_team}"

  common_tags = {
    Project   = "xfusion"
    Team      = "dev-team"
    ManagedBy = "Terraform"
    Env       = var.KKE_ENVIRONMENT
  }
}

# -------------------------
# IAM User
# -------------------------
resource "aws_iam_user" "project_user" {
  name = "${local.name_prefix}-user"
  tags = local.common_tags
}

# -------------------------
# IAM Role (EC2 Assume Role)
# -------------------------
resource "aws_iam_role" "project_role" {
  name = "${local.name_prefix}-role"

  assume_role_policy = <<POLICY
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
POLICY

  tags = merge(
    local.common_tags,
    {
      RoleType = "EC2"
    }
  )
}
```


Step 2: Create variables.tf

```
variable "KKE_PROJECT" {
  description = "Name of the project (must be non-empty)"
  type        = string

  validation {
    condition     = length(trimspace(var.KKE_PROJECT)) > 0
    error_message = "KKE_PROJECT must not be empty."
  }
}

variable "KKE_TEAM" {
  description = "Name of the team (letters, digits, dashes, underscores only)"
  type        = string

  validation {
    condition     = can(regex("^[a-zA-Z0-9_-]+$", var.KKE_TEAM))
    error_message = "KKE_TEAM may only contain letters, digits, dashes, or underscores."
  }
}

variable "KKE_ENVIRONMENT" {
  description = "Environment name"
  type        = string
}
```

Step 3: Create terraform.tfvars

```
KKE_PROJECT     = "xfusion"
KKE_TEAM        = "dev-team"
KKE_ENVIRONMENT = "dev"
```

Step 4: Create outputs.tf

```
output "kke_user_name" {
  description = "Name of the created IAM user"
  value       = aws_iam_user.project_user.name
}

output "kke_role_name" {
  description = "Name of the created IAM role"
  value       = aws_iam_role.project_role.name
}

output "kke_tags_applied" {
  description = "Tags applied to the IAM user"
  value       = aws_iam_user.project_user.tags
}
```

Step 5: Validate and Apply

```
terraform init
terraform validate
terraform apply
```

Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_iam_role.project_role will be created
  + resource "aws_iam_role" "project_role" {
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
      + name                  = "xfusion-dev-team-role"
      + name_prefix           = (known after apply)
      + path                  = "/"
      + tags                  = {
          + "Env"       = "dev"
          + "ManagedBy" = "Terraform"
          + "Project"   = "xfusion"
          + "RoleType"  = "EC2"
          + "Team"      = "dev-team"
        }
      + tags_all              = {
          + "Env"       = "dev"
          + "ManagedBy" = "Terraform"
          + "Project"   = "xfusion"
          + "RoleType"  = "EC2"
          + "Team"      = "dev-team"
        }
      + unique_id             = (known after apply)

      + inline_policy (known after apply)
    }

  # aws_iam_user.project_user will be created
  + resource "aws_iam_user" "project_user" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "xfusion-dev-team-user"
      + path          = "/"
      + tags          = {
          + "Env"       = "dev"
          + "ManagedBy" = "Terraform"
          + "Project"   = "xfusion"
          + "Team"      = "dev-team"
        }
      + tags_all      = {
          + "Env"       = "dev"
          + "ManagedBy" = "Terraform"
          + "Project"   = "xfusion"
          + "Team"      = "dev-team"
        }
      + unique_id     = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_role_name    = "xfusion-dev-team-role"
  + kke_tags_applied = {
      + Env       = "dev"
      + ManagedBy = "Terraform"
      + Project   = "xfusion"
      + Team      = "dev-team"
    }
  + kke_user_name    = "xfusion-dev-team-user"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_iam_user.project_user: Creating...
aws_iam_role.project_role: Creating...
aws_iam_user.project_user: Creation complete after 0s [id=xfusion-dev-team-user]
aws_iam_role.project_role: Creation complete after 0s [id=xfusion-dev-team-role]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

Outputs:

kke_role_name = "xfusion-dev-team-role"
kke_tags_applied = tomap({
  "Env" = "dev"
  "ManagedBy" = "Terraform"
  "Project" = "xfusion"
  "Team" = "dev-team"
})
kke_user_name = "xfusion-dev-team-user"
```

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What You’re Solving

The DevOps team wants all IAM resources to follow the same naming and tagging standards, regardless of who creates them.
This prevents confusion, improves governance, and keeps AWS resources easy to manage.

Terraform helps enforce these rules automatically.

How Naming Is Enforced

Instead of hardcoding names, the project and team values are provided as inputs.

Example inputs:

Project = xfusion
Team    = dev-team


Terraform then normalizes these values by:

Converting all characters to lowercase

Replacing underscores (_) with hyphens (-)

A single name prefix is built from these cleaned values and reused everywhere.

Final resource names become:

xfusion-dev-team-user
xfusion-dev-team-role


This guarantees consistent, lowercase, hyphenated names for IAM resources.

Why Locals Are Used

Terraform locals allow naming and tagging logic to be defined once and reused.

This means:

Naming rules are centralized

Changes are made in one place

Copy-paste errors are avoided

This approach reflects real-world enterprise Terraform practices.

Why Tags Are Centralized

A shared common_tags block ensures every resource includes:

Project ownership

Team ownership

Environment

Terraform management tracking

This improves:

Cost allocation

Auditing

Resource traceability

The IAM role also receives an additional tag:

RoleType = EC2


to clearly describe its purpose.

Why Input Validation Is Important

Terraform validates inputs before creating anything in AWS.

This prevents:

Empty project names

Invalid team values

Errors are caught early, making deployments safer and more predictable.

Common Problems This Design Prevents

❌ Uppercase IAM resource names

❌ Inconsistent naming across teams

❌ Missing or incorrect tags

❌ Manual naming mistakes

---