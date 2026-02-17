🧭 Part 1: Lab Step-by-Step Guidelines

Step 1 — Define Variables with Validation

📄 variables.tf

```
variable "KKE_AWS_REGION" {
  type = string

  validation {
    condition     = var.KKE_AWS_REGION == "us-east-1"
    error_message = "Only us-east-1 region is allowed."
  }
}

variable "KKE_QUEUE_DEPTH_THRESHOLD" {
  type    = number
  default = 50

  validation {
    condition     = var.KKE_QUEUE_DEPTH_THRESHOLD >= 1 && var.KKE_QUEUE_DEPTH_THRESHOLD <= 1000
    error_message = "Queue depth threshold must be between 1 and 1000."
  }
}

variable "KKE_IAM_ACTIONS" {
  type = list(string)
}
```


Step 2 — Provide Values

📄 terraform.tfvars

```
KKE_AWS_REGION            = "us-east-1"
KKE_QUEUE_DEPTH_THRESHOLD = 50

KKE_IAM_ACTIONS = [
  "sqs:ReceiveMessage",
  "dynamodb:PutItem",
  "sns:Publish"
]
```


Step 3 — Main Infrastructure Configuration

📄 main.tf

```
# --------------------------------
# Locals
# --------------------------------
locals {
  project     = "devops"
  environment = "dev"
  name_prefix = "${local.project}-${local.environment}"

  default_tags = {
    Project     = local.project
    Environment = local.environment
    Owner       = "PlatformTeam"
    Team        = "DevOps"
  }
}

# --------------------------------
# SNS Topic
# --------------------------------
resource "aws_sns_topic" "topic" {
  name = "${local.name_prefix}-topic"
  tags = local.default_tags
}

# --------------------------------
# SQS Queue
# --------------------------------
resource "aws_sqs_queue" "queue" {
  name = "${local.name_prefix}-queue"
  tags = local.default_tags
}

# --------------------------------
# Allow SNS to send to SQS
# --------------------------------
resource "aws_sqs_queue_policy" "policy" {
  queue_url = aws_sqs_queue.queue.id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect = "Allow"
      Principal = {
        Service = "sns.amazonaws.com"
      }
      Action = "sqs:SendMessage"
      Resource = aws_sqs_queue.queue.arn
      Condition = {
        ArnEquals = {
          "aws:SourceArn" = aws_sns_topic.topic.arn
        }
      }
    }]
  })
}

# --------------------------------
# SNS Subscription
# --------------------------------
resource "aws_sns_topic_subscription" "subscription" {
  topic_arn = aws_sns_topic.topic.arn
  protocol  = "sqs"
  endpoint  = aws_sqs_queue.queue.arn

  depends_on = [
    aws_sqs_queue_policy.policy
  ]
}

# --------------------------------
# DynamoDB Table
# --------------------------------
resource "aws_dynamodb_table" "events" {
  name           = "${local.name_prefix}-events"
  billing_mode   = "PROVISIONED"
  read_capacity  = 5
  write_capacity = 5
  hash_key       = "event_id"

  attribute {
    name = "event_id"
    type = "S"
  }

  tags = local.default_tags
}

# --------------------------------
# IAM Role
# --------------------------------
resource "aws_iam_role" "role" {
  name = "${local.name_prefix}-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect = "Allow"
      Principal = {
        Service = "ec2.amazonaws.com"
      }
      Action = "sts:AssumeRole"
    }]
  })

  tags = local.default_tags
}

# Dynamic Inline Policy
resource "aws_iam_role_policy" "dynamic_policy" {
  name = "${local.name_prefix}-policy"
  role = aws_iam_role.role.id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect   = "Allow"
      Action   = var.KKE_IAM_ACTIONS
      Resource = "*"
    }]
  })
}

# --------------------------------
# CloudWatch Alarm
# --------------------------------
resource "aws_cloudwatch_metric_alarm" "queue_alarm" {
  alarm_name          = "${local.name_prefix}-alarm"
  comparison_operator = "GreaterThanThreshold"
  evaluation_periods  = 1
  metric_name         = "ApproximateNumberOfMessagesVisible"
  namespace           = "AWS/SQS"
  period              = 60
  statistic           = "Average"
  threshold           = var.KKE_QUEUE_DEPTH_THRESHOLD
  treat_missing_data  = "missing"

  dimensions = {
    QueueName = aws_sqs_queue.queue.name
  }

  lifecycle {
    ignore_changes = [tags, tags_all]
  }
}
```


Step 4 — Outputs

📄 outputs.tf

```
output "kke_cloudwatch_alarm_name" {
  value = aws_cloudwatch_metric_alarm.queue_alarm.alarm_name
}

output "kke_dynamodb_table_name" {
  value = aws_dynamodb_table.events.name
}

output "kke_iam_role_arn" {
  value = aws_iam_role.role.arn
}

output "kke_sns_topic_arn" {
  value = aws_sns_topic.topic.arn
}

output "kke_sqs_queue_url" {
  value = aws_sqs_queue.queue.id
}
```

Step 5 — Deploy
terraform init
terraform validate
terraform apply -auto-approve

Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply -auto-approve

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_cloudwatch_metric_alarm.queue_alarm will be created
  + resource "aws_cloudwatch_metric_alarm" "queue_alarm" {
      + actions_enabled                       = true
      + alarm_name                            = "devops-dev-alarm"
      + arn                                   = (known after apply)
      + comparison_operator                   = "GreaterThanThreshold"
      + dimensions                            = {
          + "QueueName" = "devops-dev-queue"
        }
      + evaluate_low_sample_count_percentiles = (known after apply)
      + evaluation_periods                    = 1
      + id                                    = (known after apply)
      + metric_name                           = "ApproximateNumberOfMessagesVisible"
      + namespace                             = "AWS/SQS"
      + period                                = 60
      + statistic                             = "Average"
      + tags_all                              = (known after apply)
      + threshold                             = 50
      + treat_missing_data                    = "missing"
    }

  # aws_dynamodb_table.events will be created
  + resource "aws_dynamodb_table" "events" {
      + arn              = (known after apply)
      + billing_mode     = "PROVISIONED"
      + hash_key         = "event_id"
      + id               = (known after apply)
      + name             = "devops-dev-events"
      + read_capacity    = 5
      + stream_arn       = (known after apply)
      + stream_label     = (known after apply)
      + stream_view_type = (known after apply)
      + tags             = {
          + "Environment" = "dev"
          + "Owner"       = "PlatformTeam"
          + "Project"     = "devops"
          + "Team"        = "DevOps"
        }
      + tags_all         = {
          + "Environment" = "dev"
          + "Owner"       = "PlatformTeam"
          + "Project"     = "devops"
          + "Team"        = "DevOps"
        }
      + write_capacity   = 5

      + attribute {
          + name = "event_id"
          + type = "S"
        }

      + point_in_time_recovery (known after apply)

      + server_side_encryption (known after apply)

      + ttl (known after apply)
    }

  # aws_iam_role.role will be created
  + resource "aws_iam_role" "role" {
      + arn                   = (known after apply)
      + assume_role_policy    = jsonencode(
            {
              + Statement = [
                  + {
                      + Action    = "sts:AssumeRole"
                      + Effect    = "Allow"
                      + Principal = {
                          + Service = "ec2.amazonaws.com"
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
      + name                  = "devops-dev-role"
      + name_prefix           = (known after apply)
      + path                  = "/"
      + tags                  = {
          + "Environment" = "dev"
          + "Owner"       = "PlatformTeam"
          + "Project"     = "devops"
          + "Team"        = "DevOps"
        }
      + tags_all              = {
          + "Environment" = "dev"
          + "Owner"       = "PlatformTeam"
          + "Project"     = "devops"
          + "Team"        = "DevOps"
        }
      + unique_id             = (known after apply)

      + inline_policy (known after apply)
    }

  # aws_iam_role_policy.dynamic_policy will be created
  + resource "aws_iam_role_policy" "dynamic_policy" {
      + id          = (known after apply)
      + name        = "devops-dev-policy"
      + name_prefix = (known after apply)
      + policy      = jsonencode(
            {
              + Statement = [
                  + {
                      + Action   = [
                          + "sqs:ReceiveMessage",
                          + "dynamodb:PutItem",
                          + "sns:Publish",
                        ]
                      + Effect   = "Allow"
                      + Resource = "*"
                    },
                ]
              + Version   = "2012-10-17"
            }
        )
      + role        = (known after apply)
    }

  # aws_sns_topic.topic will be created
  + resource "aws_sns_topic" "topic" {
      + arn                         = (known after apply)
      + beginning_archive_time      = (known after apply)
      + content_based_deduplication = false
      + fifo_topic                  = false
      + id                          = (known after apply)
      + name                        = "devops-dev-topic"
      + name_prefix                 = (known after apply)
      + owner                       = (known after apply)
      + policy                      = (known after apply)
      + signature_version           = (known after apply)
      + tags                        = {
          + "Environment" = "dev"
          + "Owner"       = "PlatformTeam"
          + "Project"     = "devops"
          + "Team"        = "DevOps"
        }
      + tags_all                    = {
          + "Environment" = "dev"
          + "Owner"       = "PlatformTeam"
          + "Project"     = "devops"
          + "Team"        = "DevOps"
        }
      + tracing_config              = (known after apply)
    }

  # aws_sns_topic_subscription.subscription will be created
  + resource "aws_sns_topic_subscription" "subscription" {
      + arn                             = (known after apply)
      + confirmation_timeout_in_minutes = 1
      + confirmation_was_authenticated  = (known after apply)
      + endpoint                        = (known after apply)
      + endpoint_auto_confirms          = false
      + filter_policy_scope             = (known after apply)
      + id                              = (known after apply)
      + owner_id                        = (known after apply)
      + pending_confirmation            = (known after apply)
      + protocol                        = "sqs"
      + raw_message_delivery            = false
      + topic_arn                       = (known after apply)
    }

  # aws_sqs_queue.queue will be created
  + resource "aws_sqs_queue" "queue" {
      + arn                               = (known after apply)
      + content_based_deduplication       = false
      + deduplication_scope               = (known after apply)
      + delay_seconds                     = 0
      + fifo_queue                        = false
      + fifo_throughput_limit             = (known after apply)
      + id                                = (known after apply)
      + kms_data_key_reuse_period_seconds = (known after apply)
      + max_message_size                  = 262144
      + message_retention_seconds         = 345600
      + name                              = "devops-dev-queue"
      + name_prefix                       = (known after apply)
      + policy                            = (known after apply)
      + receive_wait_time_seconds         = 0
      + redrive_allow_policy              = (known after apply)
      + redrive_policy                    = (known after apply)
      + sqs_managed_sse_enabled           = (known after apply)
      + tags                              = {
          + "Environment" = "dev"
          + "Owner"       = "PlatformTeam"
          + "Project"     = "devops"
          + "Team"        = "DevOps"
        }
      + tags_all                          = {
          + "Environment" = "dev"
          + "Owner"       = "PlatformTeam"
          + "Project"     = "devops"
          + "Team"        = "DevOps"
        }
      + url                               = (known after apply)
      + visibility_timeout_seconds        = 30
    }

  # aws_sqs_queue_policy.policy will be created
  + resource "aws_sqs_queue_policy" "policy" {
      + id        = (known after apply)
      + policy    = (known after apply)
      + queue_url = (known after apply)
    }

Plan: 8 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_cloudwatch_alarm_name = "devops-dev-alarm"
  + kke_dynamodb_table_name   = "devops-dev-events"
  + kke_iam_role_arn          = (known after apply)
  + kke_sns_topic_arn         = (known after apply)
  + kke_sqs_queue_url         = (known after apply)
aws_iam_role.role: Creating...
aws_sqs_queue.queue: Creating...
aws_sns_topic.topic: Creating...
aws_dynamodb_table.events: Creating...
aws_iam_role.role: Creation complete after 0s [id=devops-dev-role]
aws_iam_role_policy.dynamic_policy: Creating...
aws_iam_role_policy.dynamic_policy: Creation complete after 0s [id=devops-dev-role:devops-dev-policy]
aws_sns_topic.topic: Creation complete after 0s [id=arn:aws:sns:us-east-1:000000000000:devops-dev-topic]
aws_dynamodb_table.events: Creation complete after 0s [id=devops-dev-events]
aws_sqs_queue.queue: Still creating... [10s elapsed]
aws_sqs_queue.queue: Still creating... [20s elapsed]
aws_sqs_queue.queue: Creation complete after 25s [id=http://sqs.us-east-1.localhost.localstack.cloud:4566/000000000000/devops-dev-queue]
aws_sqs_queue_policy.policy: Creating...
aws_cloudwatch_metric_alarm.queue_alarm: Creating...
aws_cloudwatch_metric_alarm.queue_alarm: Creation complete after 0s [id=devops-dev-alarm]
aws_sqs_queue_policy.policy: Still creating... [10s elapsed]
aws_sqs_queue_policy.policy: Still creating... [20s elapsed]
aws_sqs_queue_policy.policy: Creation complete after 25s [id=http://sqs.us-east-1.localhost.localstack.cloud:4566/000000000000/devops-dev-queue]
aws_sns_topic_subscription.subscription: Creating...
aws_sns_topic_subscription.subscription: Creation complete after 0s [id=arn:aws:sns:us-east-1:000000000000:devops-dev-topic:482a06ef-2a89-494b-9e30-5898489d307f]

Apply complete! Resources: 8 added, 0 changed, 0 destroyed.

Outputs:

kke_cloudwatch_alarm_name = "devops-dev-alarm"
kke_dynamodb_table_name = "devops-dev-events"
kke_iam_role_arn = "arn:aws:iam::000000000000:role/devops-dev-role"
kke_sns_topic_arn = "arn:aws:sns:us-east-1:000000000000:devops-dev-topic"
kke_sqs_queue_url = "http://sqs.us-east-1.localhost.localstack.cloud:4566/000000000000/devops-dev-queue"
```

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
What We Built

We created a small event-driven system:

SNS Topic
→ SQS Queue
→ DynamoDB Table
→ CloudWatch Monitoring
→ IAM Role for permissions

Why We Used Locals

Instead of hardcoding names everywhere, we defined:

project = devops
environment = dev


Then combined them:

devops-dev


Now all resources follow a consistent naming pattern:

devops-dev-topic

devops-dev-queue

devops-dev-events

devops-dev-role

devops-dev-alarm

This is how production environments stay organized.

How the Event Flow Works

A message is published to SNS.

SNS forwards it to SQS.

An application can read from SQS.

The application writes data into DynamoDB.

Why We Added CloudWatch Alarm

If too many messages build up in SQS:

It means consumers are slow or failing.

The alarm triggers when messages > 50.

This helps detect backlog problems early.

Why IAM Policy Is Dynamic

Instead of hardcoding actions, we used:

var.KKE_IAM_ACTIONS


That makes the role reusable.

If tomorrow you add:

"logs:CreateLogGroup"


You only change terraform.tfvars.

Why Validation Blocks Matter

They prevent misconfiguration:

If someone tries region = eu-west-1 → Terraform fails.

If threshold = 5000 → Terraform fails.

This protects production standards.

---