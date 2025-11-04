# Step 1: Create Main Terraform Configuration

```
# main.tf

# Create IAM policy for EC2 read-only access
resource "aws_iam_policy" "iampolicy_kirsty" {
  name        = "iampolicy_kirsty"
  description = "Read-only access to EC2 console (instances, AMIs, and snapshots)"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "ec2:DescribeInstances",
          "ec2:DescribeImages",
          "ec2:DescribeSnapshots",
          "ec2:DescribeRegions",
          "ec2:DescribeAvailabilityZones",
          "ec2:DescribeKeyPairs",
          "ec2:DescribeSecurityGroups",
          "ec2:DescribeVolumes",
          "ec2:DescribeVpcs",
          "ec2:DescribeSubnets"
        ]
        Resource = "*"
      }
    ]
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
bob@iac-server ~/terraform via 💠 default ✖ terraform plan

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_iam_policy.iampolicy_kirsty will be created
  + resource "aws_iam_policy" "iampolicy_kirsty" {
      + arn              = (known after apply)
      + attachment_count = (known after apply)
      + description      = "Read-only access to EC2 console (instances, AMIs, and snapshots)"
      + id               = (known after apply)
      + name             = "iampolicy_kirsty"
      + name_prefix      = (known after apply)
      + path             = "/"
      + policy           = jsonencode(
            {
              + Statement = [
                  + {
                      + Action   = [
                          + "ec2:DescribeInstances",
                          + "ec2:DescribeImages",
                          + "ec2:DescribeSnapshots",
                          + "ec2:DescribeRegions",
                          + "ec2:DescribeAvailabilityZones",
                          + "ec2:DescribeKeyPairs",
                          + "ec2:DescribeSecurityGroups",
                          + "ec2:DescribeVolumes",
                          + "ec2:DescribeVpcs",
                          + "ec2:DescribeSubnets",
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

  # aws_iam_policy.iampolicy_kirsty will be created
  + resource "aws_iam_policy" "iampolicy_kirsty" {
      + arn              = (known after apply)
      + attachment_count = (known after apply)
      + description      = "Read-only access to EC2 console (instances, AMIs, and snapshots)"
      + id               = (known after apply)
      + name             = "iampolicy_kirsty"
      + name_prefix      = (known after apply)
      + path             = "/"
      + policy           = jsonencode(
            {
              + Statement = [
                  + {
                      + Action   = [
                          + "ec2:DescribeInstances",
                          + "ec2:DescribeImages",
                          + "ec2:DescribeSnapshots",
                          + "ec2:DescribeRegions",
                          + "ec2:DescribeAvailabilityZones",
                          + "ec2:DescribeKeyPairs",
                          + "ec2:DescribeSecurityGroups",
                          + "ec2:DescribeVolumes",
                          + "ec2:DescribeVpcs",
                          + "ec2:DescribeSubnets",
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

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_iam_policy.iampolicy_kirsty: Creating...
aws_iam_policy.iampolicy_kirsty: Creation complete after 1s [id=arn:aws:iam::000000000000:policy/iampolicy_kirsty]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

***
