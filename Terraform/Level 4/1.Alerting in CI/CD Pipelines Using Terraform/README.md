🧭 Part 1: Lab Step-by-Step Guidelines


Step 1 — Define Variables

📄 variables.tf

```
variable "KKE_STAGING_BUCKET_NAME" {
  type = string
}

variable "KKE_FIREHOSE_ROLE_NAME" {
  type = string
}

variable "KKE_FIREHOSE_POLICY_NAME" {
  type = string
}

variable "KKE_FIREHOSE_NAME" {
  type = string
}

variable "KKE_SNS_TOPIC_NAME" {
  type = string
}

variable "KKE_CLOUDWATCH_ALARM_NAME" {
  type = string
}

variable "KKE_ALERT_EMAIL" {
  type = string
}
```

Step 2 — Provide Values

📄 terraform.tfvars

```
KKE_STAGING_BUCKET_NAME     = "datacenter-staging-32186"
KKE_FIREHOSE_ROLE_NAME      = "datacenter-firehose-role"
KKE_FIREHOSE_POLICY_NAME    = "datacenter-firehose-policy"
KKE_FIREHOSE_NAME           = "datacenter-firehose"
KKE_SNS_TOPIC_NAME          = "datacenter-alert-topic"
KKE_CLOUDWATCH_ALARM_NAME   = "datacenter-firehose-failures"
KKE_ALERT_EMAIL             = "datacenter@example.com"
```


Step 3 — Create All Infrastructure

📄 main.tf

```
# -----------------------------
# S3 Bucket (Private)
# -----------------------------
resource "aws_s3_bucket" "staging" {
  bucket = var.KKE_STAGING_BUCKET_NAME

  tags = {
    Project = "DataPipeline"
  }
}

resource "aws_s3_bucket_acl" "private_acl" {
  bucket = aws_s3_bucket.staging.id
  acl    = "private"
}

# -----------------------------
# IAM Role for Firehose
# -----------------------------
resource "aws_iam_role" "firehose_role" {
  name = var.KKE_FIREHOSE_ROLE_NAME

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect = "Allow"
      Principal = {
        Service = "firehose.amazonaws.com"
      }
      Action = "sts:AssumeRole"
    }]
  })
}

resource "aws_iam_policy" "firehose_policy" {
  name = var.KKE_FIREHOSE_POLICY_NAME

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "s3:PutObject",
          "s3:GetBucketLocation",
          "s3:ListBucket"
        ]
        Resource = [
          aws_s3_bucket.staging.arn,
          "${aws_s3_bucket.staging.arn}/*"
        ]
      }
    ]
  })
}

resource "aws_iam_role_policy_attachment" "attach_policy" {
  role       = aws_iam_role.firehose_role.name
  policy_arn = aws_iam_policy.firehose_policy.arn
}

# -----------------------------
# Kinesis Firehose
# -----------------------------
resource "aws_kinesis_firehose_delivery_stream" "firehose" {
  name        = var.KKE_FIREHOSE_NAME
  destination = "extended_s3"

  extended_s3_configuration {
    role_arn   = aws_iam_role.firehose_role.arn
    bucket_arn = aws_s3_bucket.staging.arn

    buffering_interval = 60
    buffering_size     = 5

    compression_format = "UNCOMPRESSED"
  }
}

# -----------------------------
# SNS Topic
# -----------------------------
resource "aws_sns_topic" "alerts" {
  name = var.KKE_SNS_TOPIC_NAME
}

# -----------------------------
# SES Email Identity
# -----------------------------
resource "aws_ses_email_identity" "ses_identity" {
  email = var.KKE_ALERT_EMAIL
}

# -----------------------------
# SNS Subscription
# -----------------------------
resource "aws_sns_topic_subscription" "email_subscription" {
  topic_arn = aws_sns_topic.alerts.arn
  protocol  = "email"
  endpoint  = var.KKE_ALERT_EMAIL
}

# -----------------------------
# CloudWatch Alarm
# -----------------------------
resource "aws_cloudwatch_metric_alarm" "firehose_failures" {
  alarm_name          = var.KKE_CLOUDWATCH_ALARM_NAME
  comparison_operator = "GreaterThanThreshold"
  evaluation_periods  = 1
  metric_name         = "DeliveryToS3.Failures"
  namespace           = "AWS/Firehose"
  period              = 60
  statistic           = "Sum"
  threshold           = 0

  dimensions = {
    DeliveryStreamName = aws_kinesis_firehose_delivery_stream.firehose.name
  }

  alarm_actions = [aws_sns_topic.alerts.arn]
}
```


Step 4 — Outputs

📄 outputs.tf

```
output "kke_staging_bucket_name" {
  value = aws_s3_bucket.staging.bucket
}

output "kke_firehose_name" {
  value = aws_kinesis_firehose_delivery_stream.firehose.name
}

output "kke_sns_topic_name" {
  value = aws_sns_topic.alerts.name
}

output "kke_cloudwatch_alarm_name" {
  value = aws_cloudwatch_metric_alarm.firehose_failures.alarm_name
}

output "kke_ses_identity" {
  value = aws_ses_email_identity.ses_identity.email
}
```


Step 5 — Deploy
terraform init
terraform validate
terraform apply -auto-approve


After apply:

Confirm SES email verification

Confirm SNS subscription via email confirmation link


Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply --auto-approve

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_cloudwatch_metric_alarm.firehose_failures will be created
  + resource "aws_cloudwatch_metric_alarm" "firehose_failures" {
      + actions_enabled                       = true
      + alarm_actions                         = (known after apply)
      + alarm_name                            = "datacenter-firehose-failures"
      + arn                                   = (known after apply)
      + comparison_operator                   = "GreaterThanThreshold"
      + dimensions                            = {
          + "DeliveryStreamName" = "datacenter-firehose"
        }
      + evaluate_low_sample_count_percentiles = (known after apply)
      + evaluation_periods                    = 1
      + id                                    = (known after apply)
      + metric_name                           = "DeliveryToS3.Failures"
      + namespace                             = "AWS/Firehose"
      + period                                = 60
      + statistic                             = "Sum"
      + tags_all                              = (known after apply)
      + threshold                             = 0
      + treat_missing_data                    = "missing"
    }

  # aws_iam_policy.firehose_policy will be created
  + resource "aws_iam_policy" "firehose_policy" {
      + arn              = (known after apply)
      + attachment_count = (known after apply)
      + id               = (known after apply)
      + name             = "datacenter-firehose-policy"
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
      + name                  = "datacenter-firehose-role"
      + name_prefix           = (known after apply)
      + path                  = "/"
      + tags_all              = (known after apply)
      + unique_id             = (known after apply)

      + inline_policy (known after apply)
    }

  # aws_iam_role_policy_attachment.attach_policy will be created
  + resource "aws_iam_role_policy_attachment" "attach_policy" {
      + id         = (known after apply)
      + policy_arn = (known after apply)
      + role       = "datacenter-firehose-role"
    }

  # aws_kinesis_firehose_delivery_stream.firehose will be created
  + resource "aws_kinesis_firehose_delivery_stream" "firehose" {
      + arn            = (known after apply)
      + destination    = "extended_s3"
      + destination_id = (known after apply)
      + id             = (known after apply)
      + name           = "datacenter-firehose"
      + tags_all       = (known after apply)
      + version_id     = (known after apply)

      + extended_s3_configuration {
          + bucket_arn         = (known after apply)
          + buffering_interval = 60
          + buffering_size     = 5
          + compression_format = "UNCOMPRESSED"
          + custom_time_zone   = "UTC"
          + role_arn           = (known after apply)
          + s3_backup_mode     = "Disabled"

          + cloudwatch_logging_options (known after apply)
        }
    }

  # aws_s3_bucket.staging will be created
  + resource "aws_s3_bucket" "staging" {
      + acceleration_status         = (known after apply)
      + acl                         = (known after apply)
      + arn                         = (known after apply)
      + bucket                      = "datacenter-staging-32186"
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
          + "Project" = "DataPipeline"
        }
      + tags_all                    = {
          + "Project" = "DataPipeline"
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

  # aws_s3_bucket_acl.private_acl will be created
  + resource "aws_s3_bucket_acl" "private_acl" {
      + acl    = "private"
      + bucket = (known after apply)
      + id     = (known after apply)

      + access_control_policy (known after apply)
    }

  # aws_ses_email_identity.ses_identity will be created
  + resource "aws_ses_email_identity" "ses_identity" {
      + arn   = (known after apply)
      + email = "datacenter@example.com"
      + id    = (known after apply)
    }

  # aws_sns_topic.alerts will be created
  + resource "aws_sns_topic" "alerts" {
      + arn                         = (known after apply)
      + beginning_archive_time      = (known after apply)
      + content_based_deduplication = false
      + fifo_topic                  = false
      + id                          = (known after apply)
      + name                        = "datacenter-alert-topic"
      + name_prefix                 = (known after apply)
      + owner                       = (known after apply)
      + policy                      = (known after apply)
      + signature_version           = (known after apply)
      + tags_all                    = (known after apply)
      + tracing_config              = (known after apply)
    }

  # aws_sns_topic_subscription.email_subscription will be created
  + resource "aws_sns_topic_subscription" "email_subscription" {
      + arn                             = (known after apply)
      + confirmation_timeout_in_minutes = 1
      + confirmation_was_authenticated  = (known after apply)
      + endpoint                        = "datacenter@example.com"
      + endpoint_auto_confirms          = false
      + filter_policy_scope             = (known after apply)
      + id                              = (known after apply)
      + owner_id                        = (known after apply)
      + pending_confirmation            = (known after apply)
      + protocol                        = "email"
      + raw_message_delivery            = false
      + topic_arn                       = (known after apply)
    }

Plan: 10 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_cloudwatch_alarm_name = "datacenter-firehose-failures"
  + kke_firehose_name         = "datacenter-firehose"
  + kke_ses_identity          = "datacenter@example.com"
  + kke_sns_topic_name        = "datacenter-alert-topic"
  + kke_staging_bucket_name   = "datacenter-staging-32186"
aws_ses_email_identity.ses_identity: Creating...
aws_iam_role.firehose_role: Creating...
aws_sns_topic.alerts: Creating...
aws_s3_bucket.staging: Creating...
aws_iam_role.firehose_role: Creation complete after 2s [id=datacenter-firehose-role]
aws_ses_email_identity.ses_identity: Creation complete after 2s [id=datacenter@example.com]
aws_sns_topic.alerts: Creation complete after 2s [id=arn:aws:sns:us-east-1:000000000000:datacenter-alert-topic]
aws_sns_topic_subscription.email_subscription: Creating...
aws_sns_topic_subscription.email_subscription: Creation complete after 0s [id=arn:aws:sns:us-east-1:000000000000:datacenter-alert-topic:ac710ea0-b3c4-42a0-b25d-006f299a9499]
aws_s3_bucket.staging: Creation complete after 2s [id=datacenter-staging-32186]
aws_iam_policy.firehose_policy: Creating...
aws_s3_bucket_acl.private_acl: Creating...
aws_iam_policy.firehose_policy: Creation complete after 0s [id=arn:aws:iam::000000000000:policy/datacenter-firehose-policy]
aws_s3_bucket_acl.private_acl: Creation complete after 0s [id=datacenter-staging-32186,private]
aws_kinesis_firehose_delivery_stream.firehose: Creating...
aws_iam_role_policy_attachment.attach_policy: Creating...
aws_iam_role_policy_attachment.attach_policy: Creation complete after 0s [id=datacenter-firehose-role-20260216032624000500000001]
aws_kinesis_firehose_delivery_stream.firehose: Creation complete after 0s [id=arn:aws:firehose:us-east-1:000000000000:deliverystream/datacenter-firehose]
aws_cloudwatch_metric_alarm.firehose_failures: Creating...
aws_cloudwatch_metric_alarm.firehose_failures: Creation complete after 0s [id=datacenter-firehose-failures]

Apply complete! Resources: 10 added, 0 changed, 0 destroyed.

Outputs:

kke_cloudwatch_alarm_name = "datacenter-firehose-failures"
kke_firehose_name = "datacenter-firehose"
kke_ses_identity = "datacenter@example.com"
kke_sns_topic_name = "datacenter-alert-topic"
kke_staging_bucket_name = "datacenter-staging-32186"
```


🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
What We Built

We built a complete data pipeline that:

Collects streaming data

Stores it in S3

Monitors delivery problems

Sends email alerts if something fails

How Data Flows

Imagine data coming from servers:

Servers
→ Firehose
→ S3 Bucket

Firehose automatically delivers streaming data to S3.

Why We Needed IAM Role

Firehose cannot write to S3 unless allowed.

So we created:

A role Firehose can assume

A policy allowing:

PutObject

ListBucket

GetBucketLocation

This follows least privilege.

How Failure Monitoring Works

CloudWatch watches this metric:

DeliveryToS3.Failures


If value becomes greater than 0:

Alarm triggers

SNS sends notification

Email is delivered

What SNS + SES Do

SNS = Notification system
SES = Email verification & sending service

When alarm triggers:

CloudWatch
→ SNS
→ Your verified email

Final Architecture

Streaming Data
→ Firehose
→ S3
→ CloudWatch monitors
→ SNS alerts
→ SES sends email

That is a real production-style monitoring pipeline.