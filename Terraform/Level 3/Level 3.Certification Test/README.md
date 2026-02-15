Task 1

🧭 Part 1: Lab Step-by-Step Guidelines


Step 1 — Create Required Files

Inside /home/bob/terraform:

main.tf
variables.tf
terraform.tfvars
outputs.tf

Step 2 — Define Variable

📄 variables.tf

```
variable "KKE_TABLE_NAME" {
  description = "Name of the DynamoDB table"
  type        = string
}
```

Step 3 — Provide Table Name

📄 terraform.tfvars

```
KKE_TABLE_NAME = "devops-tasks-t1q1"
```

Step 4 — Create DynamoDB Table & Insert Items

📄 main.tf

```
# -----------------------------
# DynamoDB Table
# -----------------------------
resource "aws_dynamodb_table" "kke_tasks" {
  name           = var.KKE_TABLE_NAME
  billing_mode   = "PAY_PER_REQUEST"
  hash_key       = "taskId"

  attribute {
    name = "taskId"
    type = "S"
  }

  tags = {
    Project = "ToDoApp"
  }
}

# -----------------------------
# Insert Task 1
# -----------------------------
resource "aws_dynamodb_table_item" "task1" {
  table_name = aws_dynamodb_table.kke_tasks.name
  hash_key   = aws_dynamodb_table.kke_tasks.hash_key

  item = <<ITEM
{
  "taskId": {"S": "1"},
  "description": {"S": "Learn DynamoDB"},
  "status": {"S": "completed"}
}
ITEM
}

# -----------------------------
# Insert Task 2
# -----------------------------
resource "aws_dynamodb_table_item" "task2" {
  table_name = aws_dynamodb_table.kke_tasks.name
  hash_key   = aws_dynamodb_table.kke_tasks.hash_key

  item = <<ITEM
{
  "taskId": {"S": "2"},
  "description": {"S": "Build To-Do App"},
  "status": {"S": "in-progress"}
}
ITEM
}
```

Step 5 — Define Outputs

📄 outputs.tf

```
output "kke_dynamodb_table_name" {
  description = "Name of the DynamoDB table created"
  value       = aws_dynamodb_table.kke_tasks.name
}
```


Step 6 — Deploy

From /home/bob/terraform/t1q1:

terraform init
terraform validate
terraform apply -auto-approve

Step 7 — Verify Items



Expected output 

```
bob@iac-server ~/terraform/t1q1 via 💠 default ➜  terraform apply -auto-approve

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_dynamodb_table.kke_tasks will be created
  + resource "aws_dynamodb_table" "kke_tasks" {
      + arn              = (known after apply)
      + billing_mode     = "PAY_PER_REQUEST"
      + hash_key         = "taskId"
      + id               = (known after apply)
      + name             = "devops-tasks-t1q1"
      + read_capacity    = (known after apply)
      + stream_arn       = (known after apply)
      + stream_label     = (known after apply)
      + stream_view_type = (known after apply)
      + tags             = {
          + "Project" = "ToDoApp"
        }
      + tags_all         = {
          + "Project" = "ToDoApp"
        }
      + write_capacity   = (known after apply)

      + attribute {
          + name = "taskId"
          + type = "S"
        }

      + point_in_time_recovery (known after apply)

      + server_side_encryption (known after apply)

      + ttl (known after apply)
    }

  # aws_dynamodb_table_item.task1 will be created
  + resource "aws_dynamodb_table_item" "task1" {
      + hash_key   = "taskId"
      + id         = (known after apply)
      + item       = jsonencode(
            {
              + description = {
                  + S = "Learn DynamoDB"
                }
              + status      = {
                  + S = "completed"
                }
              + taskId      = {
                  + S = "1"
                }
            }
        )
      + table_name = "devops-tasks-t1q1"
    }

  # aws_dynamodb_table_item.task2 will be created
  + resource "aws_dynamodb_table_item" "task2" {
      + hash_key   = "taskId"
      + id         = (known after apply)
      + item       = jsonencode(
            {
              + description = {
                  + S = "Build To-Do App"
                }
              + status      = {
                  + S = "in-progress"
                }
              + taskId      = {
                  + S = "2"
                }
            }
        )
      + table_name = "devops-tasks-t1q1"
    }

Plan: 3 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_dynamodb_table_name = "devops-tasks-t1q1"
aws_dynamodb_table.kke_tasks: Creating...
aws_dynamodb_table.kke_tasks: Creation complete after 2s [id=devops-tasks-t1q1]
aws_dynamodb_table_item.task2: Creating...
aws_dynamodb_table_item.task1: Creating...
aws_dynamodb_table_item.task1: Creation complete after 1s [id=devops-tasks-t1q1|taskId|1]
aws_dynamodb_table_item.task2: Creation complete after 1s [id=devops-tasks-t1q1|taskId|2]

Apply complete! Resources: 3 added, 0 changed, 0 destroyed.

Outputs:

kke_dynamodb_table_name = "devops-tasks-t1q1"
```

✔ Confirm:

```
aws dynamodb scan \
  --table-name devops-tasks-t1q1 \
  --endpoint-url http://aws:4566
```

Output

```
bob@iac-server ~/terraform/t1q1 via 💠 default ➜  aws dynamodb scan \
  --table-name devops-tasks-t1q1 \
  --endpoint-url http://aws:4566
{
    "Items": [
        {
            "description": {
                "S": "Learn DynamoDB"
            },
            "taskId": {
                "S": "1"
            },
            "status": {
                "S": "completed"
            }
        },
        {
            "description": {
                "S": "Build To-Do App"
            },
            "taskId": {
                "S": "2"
            },
            "status": {
                "S": "in-progress"
            }
        }
    ],
    "Count": 2,
    "ScannedCount": 2,
    "ConsumedCapacity": null
}
```

Task 1 → status = completed

Task 2 → status = in-progress

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
What We Built

We created:

A NoSQL database table

Inserted two task records

Verified their values

What Is DynamoDB?

DynamoDB is:

A key-value / NoSQL database

Fully managed

Highly scalable

Instead of rows and columns like SQL,
it stores items with attributes.

How the Table Works

Primary Key:

taskId


This uniquely identifies each task.

So:

taskId = 1 → Task 1

taskId = 2 → Task 2

No two items can have the same taskId.

Why Billing Mode = PAY_PER_REQUEST?

This means:

No capacity planning

Pay only when used

Best for small applications or labs

What Does aws_dynamodb_table_item Do?

It inserts data directly into the table.

Terraform:

Creates table

Inserts items

Manages them as infrastructure

If you delete Terraform resources,
items will also be removed (state-managed).

What We Verified

We scanned the table and confirmed:

Task 1 → completed

Task 2 → in-progress

This proves:

Data was inserted correctly

Attributes are stored properly

Primary key works

Architecture Overview

Application
→ DynamoDB Table
→ Stores tasks
→ Retrieves by taskId

No servers.
No database maintenance.
Fully managed.

---

Task 2

🧭 Part 1: Lab Step-by-Step Guidelines



Step 1 — Verify File Exists

```
ls /home/bob/terraform/t2q1/SensitiveData.txt
```



Step 2 — Create Terraform Files

Inside:

/home/bob/terraform/t2q1/


Create:

main.tf
outputs.tf

Step 3 — Create KMS Key and Encryption Workflow

📄 main.tf

```
# -----------------------------
# Create Symmetric KMS Key
# -----------------------------
resource "aws_kms_key" "kke_kms" {
  description             = "devops-kms-key-t2q1"
  deletion_window_in_days = 7
  enable_key_rotation     = true
}

resource "aws_kms_alias" "kke_alias" {
  name          = "alias/devops-kms-key-t2q1"
  target_key_id = aws_kms_key.kke_kms.key_id
}

# -----------------------------
# Encrypt File
# -----------------------------
resource "null_resource" "encrypt_file" {

  provisioner "local-exec" {
    command = <<EOT
aws kms encrypt \
  --key-id ${aws_kms_key.kke_kms.key_id} \
  --plaintext fileb:///home/bob/terraform/t2q1/SensitiveData.txt \
  --query CiphertextBlob \
  --output text \
  --endpoint-url http://aws:4566 \
  | base64 --decode > /home/bob/terraform/t2q1/EncryptedData.bin
EOT
  }

  depends_on = [aws_kms_key.kke_kms]
}

# -----------------------------
# Decrypt File
# -----------------------------
resource "null_resource" "decrypt_file" {

  provisioner "local-exec" {
    command = <<EOT
aws kms decrypt \
  --ciphertext-blob fileb:///home/bob/terraform/t2q1/EncryptedData.bin \
  --query Plaintext \
  --output text \
  --endpoint-url http://aws:4566 \
  | base64 --decode > /home/bob/terraform/t2q1/DecryptedData.txt
EOT
  }

  depends_on = [null_resource.encrypt_file]
}
```


Step 4 — Define Outputs

📄 outputs.tf

```
output "kke_kms_key_name" {
  description = "Name of the KMS key created"
  value       = "devops-kms-key-t2q1"
}
```

Step 5 — Deploy

From:

/home/bob/terraform/t2q1


Run:

terraform init
terraform validate
terraform apply -auto-approve

Expected output

```
bob@iac-server ~/terraform/t2q1 via 💠 default ➜  terraform apply --auto-approve

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_kms_alias.kke_alias will be created
  + resource "aws_kms_alias" "kke_alias" {
      + arn            = (known after apply)
      + id             = (known after apply)
      + name           = "alias/devops-kms-key-t2q1"
      + name_prefix    = (known after apply)
      + target_key_arn = (known after apply)
      + target_key_id  = (known after apply)
    }

  # aws_kms_key.kke_kms will be created
  + resource "aws_kms_key" "kke_kms" {
      + arn                                = (known after apply)
      + bypass_policy_lockout_safety_check = false
      + customer_master_key_spec           = "SYMMETRIC_DEFAULT"
      + deletion_window_in_days            = 7
      + description                        = "devops-kms-key-t2q1"
      + enable_key_rotation                = true
      + id                                 = (known after apply)
      + is_enabled                         = true
      + key_id                             = (known after apply)
      + key_usage                          = "ENCRYPT_DECRYPT"
      + multi_region                       = (known after apply)
      + policy                             = (known after apply)
      + rotation_period_in_days            = (known after apply)
      + tags_all                           = (known after apply)
    }

  # null_resource.decrypt_file will be created
  + resource "null_resource" "decrypt_file" {
      + id = (known after apply)
    }

  # null_resource.encrypt_file will be created
  + resource "null_resource" "encrypt_file" {
      + id = (known after apply)
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_kms_key_name = "devops-kms-key-t2q1"
aws_kms_key.kke_kms: Creating...
aws_kms_key.kke_kms: Creation complete after 4s [id=b5e751b4-4596-4a20-adee-373ebe3fa150]
aws_kms_alias.kke_alias: Creating...
null_resource.encrypt_file: Creating...
null_resource.encrypt_file: Provisioning with 'local-exec'...
null_resource.encrypt_file (local-exec): Executing: ["/bin/sh" "-c" "aws kms encrypt \\\n  --key-id b5e751b4-4596-4a20-adee-373ebe3fa150 \\\n  --plaintext fileb:///home/bob/terraform/t2q1/SensitiveData.txt \\\n  --query CiphertextBlob \\\n  --output text \\\n  --endpoint-url http://aws:4566 \\\n  | base64 --decode > /home/bob/terraform/t2q1/EncryptedData.bin\n"]
aws_kms_alias.kke_alias: Creation complete after 0s [id=alias/devops-kms-key-t2q1]
null_resource.encrypt_file: Creation complete after 1s [id=1598877684342281592]
null_resource.decrypt_file: Creating...
null_resource.decrypt_file: Provisioning with 'local-exec'...
null_resource.decrypt_file (local-exec): Executing: ["/bin/sh" "-c" "aws kms decrypt \\\n  --ciphertext-blob fileb:///home/bob/terraform/t2q1/EncryptedData.bin \\\n  --query Plaintext \\\n  --output text \\\n  --endpoint-url http://aws:4566 \\\n  | base64 --decode > /home/bob/terraform/t2q1/DecryptedData.txt\n"]
null_resource.decrypt_file: Creation complete after 0s [id=2413391988620614901]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

Outputs:

kke_kms_key_name = "devops-kms-key-t2q1"
```

Step 6 — Verify Encryption Output

Check encrypted file exists:

ls EncryptedData.bin


Check decrypted file exists:

ls DecryptedData.txt

Step 7 — Validate Decryption Matches Original

Run:

diff SensitiveData.txt DecryptedData.txt


If nothing is returned → files match exactly.

✔ Encryption and decryption successful.

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🔐 What We Created

We created a secure encryption key in AWS KMS.

Think of this key like a special lock:

It can lock (encrypt) data

It can unlock (decrypt) data

The key itself stays safely inside AWS

We never download or see the actual key material.

📄 Step 1 — Starting With a Normal File

We had a plain text file:

SensitiveData.txt


Inside it:

This is a sensitive file.


Right now, anyone who opens it can read it.

🔒 Step 2 — Encrypting the File

When we encrypted the file:

We sent its contents to AWS KMS.

KMS used the secure key to scramble the data.

It returned encrypted output.

We saved that encrypted result as:

EncryptedData.bin


Now if someone opens EncryptedData.bin, it looks like random unreadable data.

That means encryption worked.

🔓 Step 3 — Decrypting the File

Next, we reversed the process:

We sent the encrypted file back to KMS.

KMS used the same key to unlock the data.

It returned the original readable content.

We saved it as:

DecryptedData.txt


Now the file is readable again.

✅ Step 4 — Verifying Everything Worked

We compared:

SensitiveData.txt
DecryptedData.txt


If there is no difference, it means:

The encryption process did not damage the file.

The decryption restored it perfectly.

The system is working correctly.

🧠 What This Means Practically

You proved that:

Data can be securely encrypted.

Only someone with access to the KMS key can decrypt it.

The original content can be fully restored.

The process preserves data integrity.

🏗 In Real Life

This exact method is used for:

Protecting backups

Securing application secrets

Encrypting files before storing them in cloud storage

Protecting sensitive documents

You now understand the full encryption cycle:

Plain text → Encrypted data → Decrypted back to plain text

And everything matched correctly.

---

Task 3

🧭 Part 1: Lab Step-by-Step Guidelines


```
📁 Directory Structure
/home/bob/terraform/t3q1/
│
├── main.tf
├── index.html
└── modules/
    └── s3-static-site/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

Step 1 — Module: Define Variables

📄 /home/bob/terraform/t3q1/modules/s3-static-site/variables.tf

```
variable "bucket_name" {
  description = "Name of the S3 bucket"
  type        = string
}

variable "index_document" {
  description = "Index document for static website"
  type        = string
}
```

No hardcoding inside resource blocks.

Step 2 — Module: Create Bucket + Website + Policy

📄 /home/bob/terraform/t3q1/modules/s3-static-site/main.tf

```
resource "aws_s3_bucket" "static_site" {
  bucket = var.bucket_name

  tags = {
    Project = "StaticWeb"
  }
}

# Enable static website hosting
resource "aws_s3_bucket_website_configuration" "website" {
  bucket = aws_s3_bucket.static_site.id

  index_document {
    suffix = var.index_document
  }
}

# Disable public access block
resource "aws_s3_bucket_public_access_block" "public_access" {
  bucket = aws_s3_bucket.static_site.id

  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}

# Public read bucket policy
resource "aws_s3_bucket_policy" "public_policy" {
  bucket = aws_s3_bucket.static_site.id

  depends_on = [
    aws_s3_bucket_public_access_block.public_access
  ]

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid       = "PublicReadGetObject"
        Effect    = "Allow"
        Principal = "*"
        Action    = "s3:GetObject"
        Resource  = "${aws_s3_bucket.static_site.arn}/*"
      }
    ]
  })
}

# Upload index.html
resource "aws_s3_object" "index" {
  bucket       = aws_s3_bucket.static_site.id
  key          = var.index_document
  source       = "${path.root}/index.html"
  content_type = "text/html"

  depends_on = [
    aws_s3_bucket_website_configuration.website
  ]
}
```

Step 3 — Module Outputs

📄 /home/bob/terraform/t3q1/modules/s3-static-site/outputs.tf

```
output "website_url" {
  description = "S3 static website URL"
  value       = "http://aws:4566/${var.bucket_name}/${var.index_document}"
}
```


Step 4 — Root Configuration

📄 /home/bob/terraform/t3q1/main.tf

```
module "s3_static_site" {
  source         = "./modules/s3-static-site"
  bucket_name    = "devops-web-7009-t3q1"
  index_document = "index.html"
}

output "website_url" {
  value = module.s3_static_site.website_url
}
```

Step 5 — Ensure index.html Exists
📄 /home/bob/terraform/t3q1/index.html

Example:

Welcome to KKE labs!

Step 6 — Deploy

From /home/bob/terraform/t3q1:

terraform init
terraform validate
terraform apply -auto-approve

Step 7 — Verify

Output

```
bob@iac-server ~/terraform/t3q1 via 💠 default ➜  terraform apply -auto-approve

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # module.s3_static_site.aws_s3_bucket.static_site will be created
  + resource "aws_s3_bucket" "static_site" {
      + acceleration_status         = (known after apply)
      + acl                         = (known after apply)
      + arn                         = (known after apply)
      + bucket                      = "devops-web-7009-t3q1"
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
      + tags                        = {
          + "Project" = "StaticWeb"
        }
      + tags_all                    = {
          + "Project" = "StaticWeb"
        }
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

  # module.s3_static_site.aws_s3_bucket_policy.public_policy will be created
  + resource "aws_s3_bucket_policy" "public_policy" {
      + bucket = (known after apply)
      + id     = (known after apply)
      + policy = (known after apply)
    }

  # module.s3_static_site.aws_s3_bucket_public_access_block.public_access will be created
  + resource "aws_s3_bucket_public_access_block" "public_access" {
      + block_public_acls       = false
      + block_public_policy     = false
      + bucket                  = (known after apply)
      + id                      = (known after apply)
      + ignore_public_acls      = false
      + restrict_public_buckets = false
    }

  # module.s3_static_site.aws_s3_bucket_website_configuration.website will be created
  + resource "aws_s3_bucket_website_configuration" "website" {
      + bucket           = (known after apply)
      + id               = (known after apply)
      + routing_rules    = (known after apply)
      + website_domain   = (known after apply)
      + website_endpoint = (known after apply)

      + index_document {
          + suffix = "index.html"
        }

      + routing_rule (known after apply)
    }

  # module.s3_static_site.aws_s3_object.index will be created
  + resource "aws_s3_object" "index" {
      + acl                    = (known after apply)
      + arn                    = (known after apply)
      + bucket                 = (known after apply)
      + bucket_key_enabled     = (known after apply)
      + checksum_crc32         = (known after apply)
      + checksum_crc32c        = (known after apply)
      + checksum_crc64nvme     = (known after apply)
      + checksum_sha1          = (known after apply)
      + checksum_sha256        = (known after apply)
      + content_type           = "text/html"
      + etag                   = (known after apply)
      + force_destroy          = false
      + id                     = (known after apply)
      + key                    = "index.html"
      + kms_key_id             = (known after apply)
      + server_side_encryption = (known after apply)
      + source                 = "./index.html"
      + storage_class          = (known after apply)
      + tags_all               = (known after apply)
      + version_id             = (known after apply)
    }

Plan: 5 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + website_url = "http://aws:4566/devops-web-7009-t3q1/index.html"
module.s3_static_site.aws_s3_bucket.static_site: Creating...
module.s3_static_site.aws_s3_bucket.static_site: Creation complete after 0s [id=devops-web-7009-t3q1]
module.s3_static_site.aws_s3_bucket_public_access_block.public_access: Creating...
module.s3_static_site.aws_s3_bucket_website_configuration.website: Creating...
module.s3_static_site.aws_s3_bucket_public_access_block.public_access: Creation complete after 0s [id=devops-web-7009-t3q1]
module.s3_static_site.aws_s3_bucket_policy.public_policy: Creating...
module.s3_static_site.aws_s3_bucket_website_configuration.website: Creation complete after 1s [id=devops-web-7009-t3q1]
module.s3_static_site.aws_s3_object.index: Creating...
module.s3_static_site.aws_s3_bucket_policy.public_policy: Creation complete after 0s [id=devops-web-7009-t3q1]
module.s3_static_site.aws_s3_object.index: Creation complete after 0s [id=index.html]

Apply complete! Resources: 5 added, 0 changed, 0 destroyed.

Outputs:

website_url = "http://aws:4566/devops-web-7009-t3q1/index.html"
```


Test:

curl http://aws:4566/devops-web-7009-t3q1/index.html


Expected: HTML content displayed.

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
What We Built

We created:

A storage bucket

Turned it into a mini website

Made it public

Uploaded a homepage file

All automatically using Terraform.

How S3 Static Hosting Works

Normally, S3 just stores files.

When we enable website hosting, S3 behaves like a simple web server.

When someone visits:

http://aws:4566/devops-web-7009-t3q1/index.html


S3 sends back the HTML file.

No EC2.
No web server software.
No load balancer.

Why We Needed a Public Policy

By default:

S3 blocks public access.

To make a public website, we had to:

Disable public access blocking.

Attach a policy that says:

Anyone can read objects.

That is what this rule does:

Action: s3:GetObject
Principal: *


It means:

Anyone on the internet can read files in this bucket.

Why We Used a Module

Instead of writing everything in one file, we created a module.

Think of a module like a reusable blueprint.

Next time, you could deploy:

bucket_name = "company-portal"


Without rewriting the logic.

This is how real infrastructure is designed.

What Terraform Did Automatically

When you ran:

terraform apply


Terraform:

Created the bucket

Enabled website hosting

Applied public policy

Uploaded the HTML file

Printed the website URL

All in the correct order.

---