# Step 1: Create Main Terraform Configuration

```
# main.tf

resource "aws_s3_bucket" "s3_ran_bucket" {
  bucket = "datacenter-s3-9431"
  acl    = "private"

  tags = {
    Name        = "datacenter-s3-9431"
  }
}

# Enable versioning for the S3 bucket
resource "aws_s3_bucket_versioning" "s3_ran_bucket_versioning" {
  bucket = aws_s3_bucket.s3_ran_bucket.id

  versioning_configuration {
    status = "Enabled"
  }
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
aws_s3_bucket.s3_ran_bucket: Refreshing state... [id=datacenter-s3-9431]

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_s3_bucket_versioning.s3_ran_bucket_versioning will be created
  + resource "aws_s3_bucket_versioning" "s3_ran_bucket_versioning" {
      + bucket = "datacenter-s3-9431"
      + id     = (known after apply)

      + versioning_configuration {
          + mfa_delete = (known after apply)
          + status     = "Enabled"
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.
╷
│ Warning: Argument is deprecated
│ 
│   with aws_s3_bucket.s3_ran_bucket,
│   on main.tf line 3, in resource "aws_s3_bucket" "s3_ran_bucket":
│    3:   acl    = "private"
│ 
│ acl is deprecated. Use the aws_s3_bucket_acl resource instead.
│ 
│ (and one more similar warning elsewhere)
╵

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
aws_s3_bucket.s3_ran_bucket: Refreshing state... [id=datacenter-s3-9431]

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_s3_bucket_versioning.s3_ran_bucket_versioning will be created
  + resource "aws_s3_bucket_versioning" "s3_ran_bucket_versioning" {
      + bucket = "datacenter-s3-9431"
      + id     = (known after apply)

      + versioning_configuration {
          + mfa_delete = (known after apply)
          + status     = "Enabled"
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.
╷
│ Warning: Argument is deprecated
│ 
│   with aws_s3_bucket.s3_ran_bucket,
│   on main.tf line 3, in resource "aws_s3_bucket" "s3_ran_bucket":
│    3:   acl    = "private"
│ 
│ acl is deprecated. Use the aws_s3_bucket_acl resource instead.
│ 
│ (and one more similar warning elsewhere)
╵

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_s3_bucket_versioning.s3_ran_bucket_versioning: Creating...
aws_s3_bucket_versioning.s3_ran_bucket_versioning: Creation complete after 2s [id=datacenter-s3-9431]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

***
