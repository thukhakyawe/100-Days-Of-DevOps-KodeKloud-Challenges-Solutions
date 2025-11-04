# Step 1: Create Main Terraform Configuration

```
# main.tf

# Create Kinesis data stream for real-time data processing
resource "aws_kinesis_stream" "xfusion-stream" {
  name        = "xfusion-stream"
  shard_count = 1
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

  # aws_kinesis_stream.xfusion-stream will be created
  + resource "aws_kinesis_stream" "xfusion-stream" {
      + arn                       = (known after apply)
      + encryption_type           = "NONE"
      + enforce_consumer_deletion = false
      + id                        = (known after apply)
      + name                      = "xfusion-stream"
      + retention_period          = 24
      + shard_count               = 1
      + tags_all                  = (known after apply)

      + stream_mode_details (known after apply)
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

  # aws_kinesis_stream.xfusion-stream will be created
  + resource "aws_kinesis_stream" "xfusion-stream" {
      + arn                       = (known after apply)
      + encryption_type           = "NONE"
      + enforce_consumer_deletion = false
      + id                        = (known after apply)
      + name                      = "xfusion-stream"
      + retention_period          = 24
      + shard_count               = 1
      + tags_all                  = (known after apply)

      + stream_mode_details (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_kinesis_stream.xfusion-stream: Creating...
aws_kinesis_stream.xfusion-stream: Still creating... [10s elapsed]
aws_kinesis_stream.xfusion-stream: Still creating... [20s elapsed]
aws_kinesis_stream.xfusion-stream: Creation complete after 21s [id=arn:aws:kinesis:us-east-1:000000000000:stream/xfusion-stream]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

***
