1️⃣ main.tf

```
resource "aws_s3_bucket" "lifecycle_bucket" {
  bucket = "devops-lifecycle-721"
}

# Enable versioning
resource "aws_s3_bucket_versioning" "versioning" {
  bucket = aws_s3_bucket.lifecycle_bucket.id

  versioning_configuration {
    status = "Enabled"
  }
}

# Lifecycle rule
resource "aws_s3_bucket_lifecycle_configuration" "lifecycle_rule" {
  bucket = aws_s3_bucket.lifecycle_bucket.id

  rule {
    id     = "devops-lifecycle-rule"
    status = "Enabled"

    filter {
      prefix = ""
    }

    transition {
      days          = 30
      storage_class = "STANDARD_IA"
    }

    expiration {
      days = 365
    }
  }
}
```



2️⃣ outputs.tf

```
output "KKE_bucket_name" {
  value = aws_s3_bucket.lifecycle_bucket.bucket
}
```

3️⃣ Terraform Commands (Run in Order)

terraform init
terraform validate
terraform apply


Type:

yes

✅ Expected Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_s3_bucket.lifecycle_bucket will be created
  + resource "aws_s3_bucket" "lifecycle_bucket" {
      + acceleration_status         = (known after apply)
      + acl                         = (known after apply)
      + arn                         = (known after apply)
      + bucket                      = "devops-lifecycle-721"
      + bucket_domain_name          = (known after apply)
      + bucket_prefix               = (known after apply)
      + bucket_regional_domain_name = (known after apply)
      + force_destroy               = false
      + hosted_zone_id              = (known after apply)
      + id                          = (known after apply)
      + object_lock_enabled         = (known after apply)
      + policy                      = (known after apply)
      + region                      = (known after apply)
      + request_payer               = (known after apply)
      + tags_all                    = (known after apply)
      + website_domain              = (known after apply)
      + website_endpoint            = (known after apply)

      + cors_rule (known after apply)

      + grant (known after apply)

      + lifecycle_rule (known after apply)

      + logging (known after apply)

      + object_lock_configuration (known after apply)

      + replication_configuration (known after apply)

      + server_side_encryption_configuration (known after apply)

      + versioning (known after apply)

      + website (known after apply)
    }

  # aws_s3_bucket_lifecycle_configuration.lifecycle_rule will be created
  + resource "aws_s3_bucket_lifecycle_configuration" "lifecycle_rule" {
      + bucket                                 = (known after apply)
      + expected_bucket_owner                  = (known after apply)
      + id                                     = (known after apply)
      + transition_default_minimum_object_size = "all_storage_classes_128K"

      + rule {
          + id     = "devops-lifecycle-rule"
          + prefix = (known after apply)
          + status = "Enabled"

          + expiration {
              + days                         = 365
              + expired_object_delete_marker = (known after apply)
            }

          + filter {
              + object_size_greater_than = (known after apply)
              + object_size_less_than    = (known after apply)
                # (1 unchanged attribute hidden)
            }

          + transition {
              + days          = 30
              + storage_class = "STANDARD_IA"
            }
        }
    }

  # aws_s3_bucket_versioning.versioning will be created
  + resource "aws_s3_bucket_versioning" "versioning" {
      + bucket = (known after apply)
      + id     = (known after apply)

      + versioning_configuration {
          + mfa_delete = (known after apply)
          + status     = "Enabled"
        }
    }

Plan: 3 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + KKE_bucket_name = "devops-lifecycle-721"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_s3_bucket.lifecycle_bucket: Creating...
aws_s3_bucket.lifecycle_bucket: Creation complete after 1s [id=devops-lifecycle-721]
aws_s3_bucket_versioning.versioning: Creating...
aws_s3_bucket_lifecycle_configuration.lifecycle_rule: Creating...
aws_s3_bucket_versioning.versioning: Creation complete after 1s [id=devops-lifecycle-721]
aws_s3_bucket_lifecycle_configuration.lifecycle_rule: Still creating... [10s elapsed]
aws_s3_bucket_lifecycle_configuration.lifecycle_rule: Still creating... [20s elapsed]
aws_s3_bucket_lifecycle_configuration.lifecycle_rule: Creation complete after 20s [id=devops-lifecycle-721]

Apply complete! Resources: 3 added, 0 changed, 0 destroyed.

Outputs:

KKE_bucket_name = "devops-lifecycle-721"
```

---

🧠 Step-by-Step Explanation (Simple & Clear)

Let’s break down why each piece exists.

🔹 What problem is this lab solving?

S3 buckets can grow endlessly and become expensive.

So the DevOps team wants:

Frequently used data → normal storage

Old data → cheaper storage

Very old data → deleted automatically

This is cost optimization + automation.

🔹 Why enable S3 versioning?

Versioning means:

Every change creates a new version

Protects against accidental overwrites/deletes

Required in many real-world lifecycle setups

Terraform block:

aws_s3_bucket_versioning


This tells AWS:

“Keep versions of objects in this bucket.”

🔹 What is a lifecycle rule?

A lifecycle rule tells S3:

“Do this automatically when objects get old.”

No scripts. No cron jobs. AWS does it for you.

🔹 Transition after 30 days
transition {
  days          = 30
  storage_class = "STANDARD_IA"
}


Meaning:

After 30 days

Move objects to Infrequent Access

Cheaper storage, slightly higher retrieval cost

Perfect for logs, backups, archives.

🔹 Expire after 365 days
expiration {
  days = 365
}


Meaning:

After 1 year

Delete the object automatically

No manual cleanup needed

🔹 Why separate resources?

AWS provider best practice:

Bucket → aws_s3_bucket

Versioning → aws_s3_bucket_versioning

Lifecycle → aws_s3_bucket_lifecycle_configuration

This avoids deprecated arguments and grading issues.

🔹 What happens during terraform apply?

1️⃣ Terraform creates the S3 bucket
2️⃣ Enables versioning on it
3️⃣ Adds lifecycle rule
4️⃣ AWS starts tracking object age
5️⃣ Terraform outputs bucket name

From now on:

Day 30 → objects move to IA

Day 365 → objects deleted

Automatically.

🧠 Easy Memory Model

Bucket = 📦 storage

Versioning = 🕒 history

Lifecycle rule = 🤖 automation

Transition = 💰 cost saving

Expiration = 🧹 cleanup

🚨 Common Mistakes 

❌ Putting lifecycle inside aws_s3_bucket
❌ Forgetting versioning
❌ Wrong storage class name
❌ Output variable name mismatch
❌ Extra .tf files

---