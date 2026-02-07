✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Create main.tf


```
# -------------------------
# S3 Bucket
# -------------------------
resource "aws_s3_bucket" "firehose_bucket" {
  bucket = var.KKE_S3_BUCKET_NAME
}

# -------------------------
# IAM Role for Firehose (STS Assume Role)
# -------------------------
resource "aws_iam_role" "firehose_role" {
  name = var.KKE_FIREHOSE_ROLE_NAME

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Principal = {
          Service = "firehose.amazonaws.com"
        }
        Action = "sts:AssumeRole"
      }
    ]
  })
}

# -------------------------
# IAM Policy for S3 Access
# -------------------------
resource "aws_iam_policy" "firehose_s3_policy" {
  name = "firehose-s3-policy"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "s3:PutObject",
          "s3:PutObjectAcl",
          "s3:GetBucketLocation",
          "s3:ListBucket"
        ]
        Resource = [
          aws_s3_bucket.firehose_bucket.arn,
          "${aws_s3_bucket.firehose_bucket.arn}/*"
        ]
      }
    ]
  })
}

resource "aws_iam_role_policy_attachment" "firehose_attach" {
  role       = aws_iam_role.firehose_role.name
  policy_arn = aws_iam_policy.firehose_s3_policy.arn
}

# -------------------------
# Kinesis Firehose Delivery Stream
# -------------------------
resource "aws_kinesis_firehose_delivery_stream" "firehose_stream" {
  name        = var.KKE_FIREHOSE_STREAM_NAME
  destination = "extended_s3"

  depends_on = [
    aws_iam_role_policy_attachment.firehose_attach
  ]

  extended_s3_configuration {
    role_arn   = aws_iam_role.firehose_role.arn
    bucket_arn = aws_s3_bucket.firehose_bucket.arn

    buffering_size     = 5
    buffering_interval = 300

    processing_configuration {
      enabled = true

      processors {
        type = "AppendDelimiterToRecord"

        parameters {
          parameter_name  = "Delimiter"
          parameter_value = "\n"
        }
      }
    }
  }
}
```

Step 2: Create variables.tf

```
variable "KKE_S3_BUCKET_NAME" {
  description = "Name of the S3 bucket"
  type        = string
}

variable "KKE_FIREHOSE_STREAM_NAME" {
  description = "Name of the Firehose delivery stream"
  type        = string
}

variable "KKE_FIREHOSE_ROLE_NAME" {
  description = "Name of the Firehose IAM role"
  type        = string
}
```


Step 3: Create terraform.tfvars

```
KKE_S3_BUCKET_NAME           = "xfusion-stream-bucket-28590"
KKE_FIREHOSE_STREAM_NAME    = "xfusion-firehose-stream"
KKE_FIREHOSE_ROLE_NAME      = "firehose-sts-role"
```

Step 4: Create outputs.tf

```
output "kke_firehose_stream_name" {
  value       = aws_kinesis_firehose_delivery_stream.firehose_stream.name
  description = "Name of the Firehose stream created"
}

output "kke_s3_bucket_name" {
  value       = aws_s3_bucket.firehose_bucket.bucket
  description = "Name of the S3 bucket created"
}

output "kke_firehose_role_arn" {
  value       = aws_iam_role.firehose_role.arn
  description = "ARN of the Firehose IAM role"
}
```

Step 5: Initialize and Apply

terraform init
terraform validate
terraform apply


Approve with yes.

Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_iam_policy.firehose_s3_policy will be created
  + resource "aws_iam_policy" "firehose_s3_policy" {
      + arn              = (known after apply)
      + attachment_count = (known after apply)
      + id               = (known after apply)
      + name             = "firehose-s3-policy"
      + name_prefix      = (known after apply)
      + path             = "/"
      + policy           = (known after apply)
      + policy_id        = (known after apply)
      + tags_all         = (known after apply)
    }

  # aws_iam_role.firehose_role will be created
  + resource "aws_iam_role" "firehose_role" {
      + arn                   = (known after apply)
      + assume_role_policy    = jsonencode(
            {
              + Statement = [
                  + {
                      + Action    = "sts:AssumeRole"
                      + Effect    = "Allow"
                      + Principal = {
                          + Service = "firehose.amazonaws.com"
                        }
                    },
                ]
              + Version   = "2012-10-17"
            }
        )
      + create_date           = (known after apply)
      + force_detach_policies = false
      + id                    = (known after apply)
      + managed_policy_arns   = (known after apply)
      + max_session_duration  = 3600
      + name                  = "firehose-sts-role"
      + name_prefix           = (known after apply)
      + path                  = "/"
      + tags_all              = (known after apply)
      + unique_id             = (known after apply)

      + inline_policy (known after apply)
    }

  # aws_iam_role_policy_attachment.firehose_attach will be created
  + resource "aws_iam_role_policy_attachment" "firehose_attach" {
      + id         = (known after apply)
      + policy_arn = (known after apply)
      + role       = "firehose-sts-role"
    }

  # aws_kinesis_firehose_delivery_stream.firehose_stream will be created
  + resource "aws_kinesis_firehose_delivery_stream" "firehose_stream" {
      + arn            = (known after apply)
      + destination    = "extended_s3"
      + destination_id = (known after apply)
      + id             = (known after apply)
      + name           = "xfusion-firehose-stream"
      + tags_all       = (known after apply)
      + version_id     = (known after apply)

      + extended_s3_configuration {
          + bucket_arn         = (known after apply)
          + buffering_interval = 300
          + buffering_size     = 5
          + compression_format = "UNCOMPRESSED"
          + custom_time_zone   = "UTC"
          + role_arn           = (known after apply)
          + s3_backup_mode     = "Disabled"

          + cloudwatch_logging_options (known after apply)

          + processing_configuration {
              + enabled = true

              + processors {
                  + type = "AppendDelimiterToRecord"

                  + parameters {
                      + parameter_name  = "Delimiter"
                      + parameter_value = <<-EOT
                            
                        EOT
                    }
                }
            }
        }
    }

  # aws_s3_bucket.firehose_bucket will be created
  + resource "aws_s3_bucket" "firehose_bucket" {
      + acceleration_status         = (known after apply)
      + acl                         = (known after apply)
      + arn                         = (known after apply)
      + bucket                      = "xfusion-stream-bucket-28590"
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

Plan: 5 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_firehose_role_arn    = (known after apply)
  + kke_firehose_stream_name = "xfusion-firehose-stream"
  + kke_s3_bucket_name       = "xfusion-stream-bucket-28590"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_iam_role.firehose_role: Creating...
aws_s3_bucket.firehose_bucket: Creating...
aws_iam_role.firehose_role: Creation complete after 0s [id=firehose-sts-role]
aws_s3_bucket.firehose_bucket: Creation complete after 0s [id=xfusion-stream-bucket-28590]
aws_iam_policy.firehose_s3_policy: Creating...
aws_iam_policy.firehose_s3_policy: Creation complete after 1s [id=arn:aws:iam::000000000000:policy/firehose-s3-policy]
aws_iam_role_policy_attachment.firehose_attach: Creating...
aws_iam_role_policy_attachment.firehose_attach: Creation complete after 0s [id=firehose-sts-role-20260207054339516100000001]
aws_kinesis_firehose_delivery_stream.firehose_stream: Creating...
aws_kinesis_firehose_delivery_stream.firehose_stream: Creation complete after 0s [id=arn:aws:firehose:us-east-1:000000000000:deliverystream/xfusion-firehose-stream]

Apply complete! Resources: 5 added, 0 changed, 0 destroyed.

Outputs:

kke_firehose_role_arn = "arn:aws:iam::000000000000:role/firehose-sts-role"
kke_firehose_stream_name = "xfusion-firehose-stream"
kke_s3_bucket_name = "xfusion-stream-bucket-28590"
```

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What You’re Building

You are creating a streaming pipeline where incoming data is:

Sent to Firehose

Buffered

Written into S3 files automatically

Why These Services Are Used

Firehose: Handles streaming data without managing servers

S3: Cheap, durable storage for logs and events

IAM + STS: Securely lets Firehose write to S3

How the Pipeline Works

Firehose assumes an IAM role

It buffers data until:

5 MB is reached OR

300 seconds pass

Each record gets \n appended

Data is delivered to S3

Why depends_on Is Important

Terraform may try to create Firehose before the role policy is attached.
depends_on guarantees the correct order and avoids failures.

What the Delimiter Does

Without the delimiter:

record1record2record3


With \n enabled:

record1
record2
record3


This is critical for logs, analytics, and Athena queries.

Common Mistakes to Avoid

❌ Forgetting sts:AssumeRole

❌ Missing depends_on

❌ Using incorrect processor type

❌ Buffering size not in MB

---