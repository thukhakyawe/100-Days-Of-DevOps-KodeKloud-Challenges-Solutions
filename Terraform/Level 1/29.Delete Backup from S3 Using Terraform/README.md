# Step 1: Create Main Terraform Configuration

```
# main.tf

# Execute AWS CLI commands to copy and delete S3 bucket
resource "null_resource" "s3_cleanup" {
  provisioner "local-exec" {
    command = <<EOT
      echo "Creating backup directory..."
      mkdir -p /opt/s3-backup/

      echo "Copying contents from S3 bucket to local directory..."
      aws s3 cp s3://nautilus-bck-29479 /opt/s3-backup/ --recursive

      echo "Deleting the S3 bucket..."
      aws s3 rb s3://nautilus-bck-29479 --force
    EOT
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

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # null_resource.s3_cleanup will be created
  + resource "null_resource" "s3_cleanup" {
      + id = (known after apply)
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

  # null_resource.s3_cleanup will be created
  + resource "null_resource" "s3_cleanup" {
      + id = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

null_resource.s3_cleanup: Creating...
null_resource.s3_cleanup: Provisioning with 'local-exec'...
null_resource.s3_cleanup (local-exec): Executing: ["/bin/sh" "-c" "      echo \"Creating backup directory...\"\n      mkdir -p /opt/s3-backup/\n\n      echo \"Copying contents from S3 bucket to local directory...\"\n      aws s3 cp s3://nautilus-bck-29479 /opt/s3-backup/ --recursive\n\n      echo \"Deleting the S3 bucket...\"\n      aws s3 rb s3://nautilus-bck-29479 --force\n"]
null_resource.s3_cleanup (local-exec): Creating backup directory...
null_resource.s3_cleanup (local-exec): Copying contents from S3 bucket to local directory...
null_resource.s3_cleanup (local-exec): Completed 27 Bytes/27 Bytes (2.7 KiB/s) with 1 file(s) remaining
null_resource.s3_cleanup (local-exec): download: s3://nautilus-bck-29479/nautilus.txt to ../../../opt/s3-backup/nautilus.txt
null_resource.s3_cleanup (local-exec): Deleting the S3 bucket...
null_resource.s3_cleanup (local-exec): delete: s3://nautilus-bck-29479/nautilus.txt
null_resource.s3_cleanup (local-exec): remove_bucket: nautilus-bck-29479
null_resource.s3_cleanup: Creation complete after 1s [id=4571548781870379384]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

***
