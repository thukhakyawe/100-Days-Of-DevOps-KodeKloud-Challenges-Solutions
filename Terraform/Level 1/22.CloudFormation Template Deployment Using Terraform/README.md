# Step 1: Create Main Terraform Configuration

```
# main.tf

# CloudFormation stack that creates an S3 bucket with versioning
resource "aws_cloudformation_stack" "nautilus_stack" {
  name = "nautilus-stack"

  template_body = jsonencode({
    AWSTemplateFormatVersion = "2010-09-09"
    Resources = {
      NautilusBucket = {
        Type = "AWS::S3::Bucket"
        Properties = {
          BucketName = "nautilus-bucket-18142"
          VersioningConfiguration = {
            Status = "Enabled"
          }
        }
      }
    }
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

  # aws_cloudformation_stack.nautilus_stack will be created
  + resource "aws_cloudformation_stack" "nautilus_stack" {
      + id            = (known after apply)
      + name          = "nautilus-stack"
      + outputs       = (known after apply)
      + parameters    = (known after apply)
      + policy_body   = (known after apply)
      + tags_all      = (known after apply)
      + template_body = jsonencode(
            {
              + AWSTemplateFormatVersion = "2010-09-09"
              + Resources                = {
                  + NautilusBucket = {
                      + Properties = {
                          + BucketName              = "nautilus-bucket-18142"
                          + VersioningConfiguration = {
                              + Status = "Enabled"
                            }
                        }
                      + Type       = "AWS::S3::Bucket"
                    }
                }
            }
        )
    }

Plan: 1 to add, 0 to change, 0 to destroy.

────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take
exactly these actions if you run "terraform apply" now.
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

  # aws_cloudformation_stack.nautilus_stack will be created
  + resource "aws_cloudformation_stack" "nautilus_stack" {
      + id            = (known after apply)
      + name          = "nautilus-stack"
      + outputs       = (known after apply)
      + parameters    = (known after apply)
      + policy_body   = (known after apply)
      + tags_all      = (known after apply)
      + template_body = jsonencode(
            {
              + AWSTemplateFormatVersion = "2010-09-09"
              + Resources                = {
                  + NautilusBucket = {
                      + Properties = {
                          + BucketName              = "nautilus-bucket-18142"
                          + VersioningConfiguration = {
                              + Status = "Enabled"
                            }
                        }
                      + Type       = "AWS::S3::Bucket"
                    }
                }
            }
        )
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_cloudformation_stack.nautilus_stack: Creating...
aws_cloudformation_stack.nautilus_stack: Still creating... [10s elapsed]
aws_cloudformation_stack.nautilus_stack: Creation complete after 11s [id=arn:aws:cloudformation:us-east-1:000000000000:stack/nautilus-stack/52879faf-5378-450e-a41f-b3baf6fa01eb]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

***
