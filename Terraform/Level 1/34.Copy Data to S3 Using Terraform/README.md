# Step 1: Update Main Terraform Configuration

```
# main.tf

resource "aws_s3_bucket" "my_bucket" {
  bucket = "datacenter-cp-13069"
  acl    = "private"

  tags = {
    Name        = "datacenter-cp-13069"
  }
}

# Upload file to S3 bucket
resource "aws_s3_object" "upload_file" {
  bucket = aws_s3_bucket.my_bucket.bucket
  key    = "datacenter.txt"
  source = "/tmp/datacenter.txt"
  etag   = filemd5("/tmp/datacenter.txt")
}
```



# Check Existing Resources

```
ls -l /tmp/datacenter.txt 
```

Output

```
bob@iac-server ~/terraform via 💠 default ➜  ls -l /tmp/datacenter.txt 
-rw-rw-r-- 1 bob bob 27 Nov  8 15:11 /tmp/datacenter.txt
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



# Plan the configuration:

```
terraform plan
```

Output

```
bob@iac-server ~/terraform via 💠 default ✖ terraform plan
aws_s3_bucket.my_bucket: Refreshing state... [id=datacenter-cp-13069]

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_s3_object.upload_file will be created
  + resource "aws_s3_object" "upload_file" {
      + acl                    = (known after apply)
      + arn                    = (known after apply)
      + bucket                 = "datacenter-cp-13069"
      + bucket_key_enabled     = (known after apply)
      + checksum_crc32         = (known after apply)
      + checksum_crc32c        = (known after apply)
      + checksum_crc64nvme     = (known after apply)
      + checksum_sha1          = (known after apply)
      + checksum_sha256        = (known after apply)
      + content_type           = (known after apply)
      + etag                   = "628f77ec27c0e6eb1e0c6543cc3dd865"
      + force_destroy          = false
      + id                     = (known after apply)
      + key                    = "datacenter.txt"
      + kms_key_id             = (known after apply)
      + server_side_encryption = (known after apply)
      + source                 = "/tmp/datacenter.txt"
      + storage_class          = (known after apply)
      + tags_all               = (known after apply)
      + version_id             = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.
╷
│ Warning: Argument is deprecated
│ 
│   with aws_s3_bucket.my_bucket,
│   on main.tf line 3, in resource "aws_s3_bucket" "my_bucket":
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


# Plan the configuration:

```
terraform apply
```

Then type `yes` when prompted to confirm the creation of the snapshot.


Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply
aws_s3_bucket.my_bucket: Refreshing state... [id=datacenter-cp-13069]

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_s3_object.upload_file will be created
  + resource "aws_s3_object" "upload_file" {
      + acl                    = (known after apply)
      + arn                    = (known after apply)
      + bucket                 = "datacenter-cp-13069"
      + bucket_key_enabled     = (known after apply)
      + checksum_crc32         = (known after apply)
      + checksum_crc32c        = (known after apply)
      + checksum_crc64nvme     = (known after apply)
      + checksum_sha1          = (known after apply)
      + checksum_sha256        = (known after apply)
      + content_type           = (known after apply)
      + etag                   = "628f77ec27c0e6eb1e0c6543cc3dd865"
      + force_destroy          = false
      + id                     = (known after apply)
      + key                    = "datacenter.txt"
      + kms_key_id             = (known after apply)
      + server_side_encryption = (known after apply)
      + source                 = "/tmp/datacenter.txt"
      + storage_class          = (known after apply)
      + tags_all               = (known after apply)
      + version_id             = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.
╷
│ Warning: Argument is deprecated
│ 
│   with aws_s3_bucket.my_bucket,
│   on main.tf line 3, in resource "aws_s3_bucket" "my_bucket":
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

aws_s3_object.upload_file: Creating...
aws_s3_object.upload_file: Creation complete after 0s [id=datacenter.txt]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

***
