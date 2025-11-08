# Step 1: Create Main Terraform Configuration

```
# main.tf

# Create IAM user
resource "aws_iam_user" "user" {
  name = "iamuser_james"

  tags = {
    Name = "iamuser_james"
  }
}

# Create IAM Policy
resource "aws_iam_policy" "policy" {
  name        = "iampolicy_james"
  description = "IAM policy allowing EC2 read actions for james"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect   = "Allow"
        Action   = ["ec2:Read*"]
        Resource = "*"
      }
    ]
  })
}


# Attach IAM policy to IAM user
resource "aws_iam_user_policy_attachment" "james_attach" {
  user       = aws_iam_user.user.name
  policy_arn = aws_iam_policy.policy.arn
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
aws_iam_policy.policy: Refreshing state... [id=arn:aws:iam::000000000000:policy/iampolicy_james]
aws_iam_user.user: Refreshing state... [id=iamuser_james]

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_iam_user_policy_attachment.james_attach will be created
  + resource "aws_iam_user_policy_attachment" "james_attach" {
      + id         = (known after apply)
      + policy_arn = "arn:aws:iam::000000000000:policy/iampolicy_james"
      + user       = "iamuser_james"
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
aws_iam_policy.policy: Refreshing state... [id=arn:aws:iam::000000000000:policy/iampolicy_james]
aws_iam_user.user: Refreshing state... [id=iamuser_james]

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_iam_user_policy_attachment.james_attach will be created
  + resource "aws_iam_user_policy_attachment" "james_attach" {
      + id         = (known after apply)
      + policy_arn = "arn:aws:iam::000000000000:policy/iampolicy_james"
      + user       = "iamuser_james"
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_iam_user_policy_attachment.james_attach: Creating...
aws_iam_user_policy_attachment.james_attach: Creation complete after 0s [id=iamuser_james-20251108045812663600000001]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

***
