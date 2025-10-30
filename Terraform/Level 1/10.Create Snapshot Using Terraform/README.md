# Update the main.tf file with the complete solution:

```
# main.tf

resource "aws_ebs_volume" "k8s_volume" {
  availability_zone = "us-east-1a"
  size              = 5
  type              = "gp2"

  tags = {
    Name        = "nautilus-vol"
  }
}

# Create snapshot of the existing volume
resource "aws_ebs_snapshot" "nautilus_snapshot" {
  volume_id   = aws_ebs_volume.k8s_volume.id
  description = "Nautilus Snapshot"
  
  tags = {
    Name = "nautilus-vol-ss"
  }
}
```


To deploy this configuration:

Navigate to the Terraform directory:

```
cd /home/bob/terraform
```

Initialize Terraform:

```
terraform init
```

Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform init
Initializing the backend...
Initializing provider plugins...
- Reusing previous version of hashicorp/aws from the dependency lock file
- Using previously-installed hashicorp/aws v5.91.0

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

Plan the deployment to verify the configuration:

```
terraform plan
```

Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform plan
aws_ebs_volume.k8s_volume: Refreshing state... [id=vol-523a81e2e11961950]

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_ebs_snapshot.nautilus_snapshot will be created
  + resource "aws_ebs_snapshot" "nautilus_snapshot" {
      + arn                    = (known after apply)
      + data_encryption_key_id = (known after apply)
      + description            = "Nautilus Snapshot"
      + encrypted              = (known after apply)
      + id                     = (known after apply)
      + kms_key_id             = (known after apply)
      + owner_alias            = (known after apply)
      + owner_id               = (known after apply)
      + storage_tier           = (known after apply)
      + tags                   = {
          + "Name" = "nautilus-vol-ss"
        }
      + tags_all               = {
          + "Name" = "nautilus-vol-ss"
        }
      + volume_id              = "vol-523a81e2e11961950"
      + volume_size            = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

─────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to
take exactly these actions if you run "terraform apply" now.
```


Apply the configuration:

```
terraform apply
```

Then type `yes` when prompted to confirm the creation of the snapshot.


Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply
aws_ebs_volume.k8s_volume: Refreshing state... [id=vol-523a81e2e11961950]

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_ebs_snapshot.nautilus_snapshot will be created
  + resource "aws_ebs_snapshot" "nautilus_snapshot" {
      + arn                    = (known after apply)
      + data_encryption_key_id = (known after apply)
      + description            = "Nautilus Snapshot"
      + encrypted              = (known after apply)
      + id                     = (known after apply)
      + kms_key_id             = (known after apply)
      + owner_alias            = (known after apply)
      + owner_id               = (known after apply)
      + storage_tier           = (known after apply)
      + tags                   = {
          + "Name" = "nautilus-vol-ss"
        }
      + tags_all               = {
          + "Name" = "nautilus-vol-ss"
        }
      + volume_id              = "vol-523a81e2e11961950"
      + volume_size            = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_ebs_snapshot.nautilus_snapshot: Creating...
aws_ebs_snapshot.nautilus_snapshot: Creation complete after 0s [id=snap-9c45cd1ddd58dd6a5]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

***