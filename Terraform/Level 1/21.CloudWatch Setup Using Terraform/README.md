# Step 1: Create Main Terraform Configuration

```
# main.tf

# Create CloudWatch log group for organizing log data
resource "aws_cloudwatch_log_group" "xfusion-log-group" {
  name = "xfusion-log-group"
}

# Create CloudWatch log stream for storing log events
resource "aws_cloudwatch_log_stream" "xfusion-log-stream" {
  name           = "xfusion-log-stream"
  log_group_name = aws_cloudwatch_log_group.xfusion-log-group.name
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

  # aws_cloudwatch_log_group.xfusion-log-group will be created
  + resource "aws_cloudwatch_log_group" "xfusion-log-group" {
      + arn               = (known after apply)
      + id                = (known after apply)
      + log_group_class   = (known after apply)
      + name              = "xfusion-log-group"
      + name_prefix       = (known after apply)
      + retention_in_days = 0
      + skip_destroy      = false
      + tags_all          = (known after apply)
    }

  # aws_cloudwatch_log_stream.xfusion-log-stream will be created
  + resource "aws_cloudwatch_log_stream" "xfusion-log-stream" {
      + arn            = (known after apply)
      + id             = (known after apply)
      + log_group_name = "xfusion-log-group"
      + name           = "xfusion-log-stream"
    }

Plan: 2 to add, 0 to change, 0 to destroy.

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

  # aws_cloudwatch_log_group.xfusion-log-group will be created
  + resource "aws_cloudwatch_log_group" "xfusion-log-group" {
      + arn               = (known after apply)
      + id                = (known after apply)
      + log_group_class   = (known after apply)
      + name              = "xfusion-log-group"
      + name_prefix       = (known after apply)
      + retention_in_days = 0
      + skip_destroy      = false
      + tags_all          = (known after apply)
    }

  # aws_cloudwatch_log_stream.xfusion-log-stream will be created
  + resource "aws_cloudwatch_log_stream" "xfusion-log-stream" {
      + arn            = (known after apply)
      + id             = (known after apply)
      + log_group_name = "xfusion-log-group"
      + name           = "xfusion-log-stream"
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_cloudwatch_log_group.xfusion-log-group: Creating...
aws_cloudwatch_log_group.xfusion-log-group: Creation complete after 1s [id=xfusion-log-group]
aws_cloudwatch_log_stream.xfusion-log-stream: Creating...
aws_cloudwatch_log_stream.xfusion-log-stream: Creation complete after 0s [id=xfusion-log-stream]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

***
