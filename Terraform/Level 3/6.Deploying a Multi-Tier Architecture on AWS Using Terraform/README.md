✅ Part 1: Lab Step-by-Step Guidelines



📄 Step 1: Create main.tf

```
# -------------------------
# DynamoDB Table
# -------------------------
resource "aws_dynamodb_table" "app_table" {
  name         = var.KKE_DYNAMODB_TABLE_NAME
  billing_mode = "PAY_PER_REQUEST"

  hash_key = "id"

  attribute {
    name = "id"
    type = "S"
  }

  tags = {
    Environment = var.KKE_ENVIRONMENT
  }
}


# -------------------------
# SNS Topic
# -------------------------
resource "aws_sns_topic" "app_topic" {
  name = var.KKE_SNS_TOPIC_NAME

  tags = {
    Environment = var.KKE_ENVIRONMENT
  }
}


# -------------------------
# SSM Parameter
# -------------------------
resource "aws_ssm_parameter" "app_config" {
  name  = var.KKE_SSM_PARAM_NAME
  type  = "SecureString"
  value = "sensitive-config-value"

  tags = {
    Environment = var.KKE_ENVIRONMENT
  }
}
```

📄 Step 2: Create variables.tf

```
variable "KKE_ENVIRONMENT" {
  type = string
}

variable "KKE_DYNAMODB_TABLE_NAME" {
  type = string
}

variable "KKE_SNS_TOPIC_NAME" {
  type = string
}

variable "KKE_SSM_PARAM_NAME" {
  type = string
}
```

📄 Step 3: Create terraform.tfvars

```
KKE_ENVIRONMENT          = "devEnvironment"
KKE_DYNAMODB_TABLE_NAME = "xfusion-app-table"
KKE_SNS_TOPIC_NAME      = "xfusion-app-topic"
KKE_SSM_PARAM_NAME      = "/xfusion/app/config"
```


📄 Step 4: Create outputs.tf

```
output "kke_dynamodb_table_name" {
  value = aws_dynamodb_table.app_table.name
}

output "kke_sns_topic_arn" {
  value = aws_sns_topic.app_topic.arn
}

output "kke_ssm_parameter_name" {
  value = aws_ssm_parameter.app_config.name
}
```

▶️ Step 5: Apply Terraform
terraform init
terraform validate
terraform apply 
type - yes

✅ Expected Result

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply 

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_dynamodb_table.app_table will be created
  + resource "aws_dynamodb_table" "app_table" {
      + arn              = (known after apply)
      + billing_mode     = "PAY_PER_REQUEST"
      + hash_key         = "id"
      + id               = (known after apply)
      + name             = "xfusion-app-table"
      + read_capacity    = (known after apply)
      + stream_arn       = (known after apply)
      + stream_label     = (known after apply)
      + stream_view_type = (known after apply)
      + tags             = {
          + "Environment" = "devEnvironment"
        }
      + tags_all         = {
          + "Environment" = "devEnvironment"
        }
      + write_capacity   = (known after apply)

      + attribute {
          + name = "id"
          + type = "S"
        }

      + point_in_time_recovery (known after apply)

      + server_side_encryption (known after apply)

      + ttl (known after apply)
    }

  # aws_sns_topic.app_topic will be created
  + resource "aws_sns_topic" "app_topic" {
      + arn                         = (known after apply)
      + beginning_archive_time      = (known after apply)
      + content_based_deduplication = false
      + fifo_topic                  = false
      + id                          = (known after apply)
      + name                        = "xfusion-app-topic"
      + name_prefix                 = (known after apply)
      + owner                       = (known after apply)
      + policy                      = (known after apply)
      + signature_version           = (known after apply)
      + tags                        = {
          + "Environment" = "devEnvironment"
        }
      + tags_all                    = {
          + "Environment" = "devEnvironment"
        }
      + tracing_config              = (known after apply)
    }

  # aws_ssm_parameter.app_config will be created
  + resource "aws_ssm_parameter" "app_config" {
      + arn            = (known after apply)
      + data_type      = (known after apply)
      + has_value_wo   = (known after apply)
      + id             = (known after apply)
      + insecure_value = (known after apply)
      + key_id         = (known after apply)
      + name           = "/xfusion/app/config"
      + tags           = {
          + "Environment" = "devEnvironment"
        }
      + tags_all       = {
          + "Environment" = "devEnvironment"
        }
      + tier           = (known after apply)
      + type           = "SecureString"
      + value          = (sensitive value)
      + value_wo       = (write-only attribute)
      + version        = (known after apply)
    }

Plan: 3 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_dynamodb_table_name = "xfusion-app-table"
  + kke_sns_topic_arn       = (known after apply)
  + kke_ssm_parameter_name  = "/xfusion/app/config"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_ssm_parameter.app_config: Creating...
aws_sns_topic.app_topic: Creating...
aws_dynamodb_table.app_table: Creating...
aws_sns_topic.app_topic: Creation complete after 2s [id=arn:aws:sns:us-east-1:000000000000:xfusion-app-topic]
aws_ssm_parameter.app_config: Creation complete after 2s [id=/xfusion/app/config]
aws_dynamodb_table.app_table: Creation complete after 4s [id=xfusion-app-table]

Apply complete! Resources: 3 added, 0 changed, 0 destroyed.

Outputs:

kke_dynamodb_table_name = "xfusion-app-table"
kke_sns_topic_arn = "arn:aws:sns:us-east-1:000000000000:xfusion-app-topic"
kke_ssm_parameter_name = "/xfusion/app/config"
```

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
What This Lab Is About

You are building foundational cloud infrastructure for a modern application:

DynamoDB → Database layer

SNS → Messaging and notifications

SSM Parameter Store → Secure configuration storage

Step 1: DynamoDB Table

We create a simple NoSQL table with:

Primary key: id

On-demand billing

Minimal setup

This allows the application to store and retrieve data.

Step 2: SNS Topic

SNS allows applications to:

Send notifications

Publish events

Trigger subscribers

The topic acts as a communication channel.

Step 3: SSM Parameter

Sensitive configuration values (like API keys or secrets) should not be hardcoded.

We store the value securely as a:

SecureString


This ensures encryption at rest.

🔑 Key DevOps Concept

Infrastructure is modular:

Storage layer (DynamoDB)

Messaging layer (SNS)

Configuration layer (SSM)

Each component is independent and reusable.

---