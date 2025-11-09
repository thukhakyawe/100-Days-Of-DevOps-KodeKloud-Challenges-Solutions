# Step 1: Create variables.tf

```
# variables.tf

# Define variable for IAM user name
variable "KKE_user" {
  description = "The name of the IAM user to create"
  type        = string
  default     = "iamuser_mark"
}
```

# Step 2: Create main.tf


```
# main.tf

# Create AWS IAM User with variable reference
resource "aws_iam_user" "this" {
  name = var.KKE_user
  
  tags = {
    Name = var.KKE_user
  }
}
```

# Step 3: To deploy this configuration

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



# Plan the configuration:

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

  # aws_iam_user.this will be created
  + resource "aws_iam_user" "this" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "iamuser_mark"
      + path          = "/"
      + tags          = {
          + "Name" = "iamuser_mark"
        }
      + tags_all      = {
          + "Name" = "iamuser_mark"
        }
      + unique_id     = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

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

  # aws_iam_user.this will be created
  + resource "aws_iam_user" "this" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "iamuser_mark"
      + path          = "/"
      + tags          = {
          + "Name" = "iamuser_mark"
        }
      + tags_all      = {
          + "Name" = "iamuser_mark"
        }
      + unique_id     = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_iam_user.this: Creating...
aws_iam_user.this: Creation complete after 0s [id=iamuser_mark]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

***
