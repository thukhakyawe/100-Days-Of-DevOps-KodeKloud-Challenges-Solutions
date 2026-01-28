1️⃣ main.tf



```
# SNS Topic
resource "aws_sns_topic" "devops_sns_topic" {
  name = "devops-sns-topic"
}

# SQS Queue
resource "aws_sqs_queue" "devops_sqs_queue" {
  name = "devops-sqs-queue"
}

# Allow SNS to send messages to SQS
resource "aws_sqs_queue_policy" "sqs_policy" {
  queue_url = aws_sqs_queue.devops_sqs_queue.id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect = "Allow"
      Principal = "*"
      Action = "sqs:SendMessage"
      Resource = aws_sqs_queue.devops_sqs_queue.arn
      Condition = {
        ArnEquals = {
          "aws:SourceArn" = aws_sns_topic.devops_sns_topic.arn
        }
      }
    }]
  })
}

# SNS Subscription (SNS → SQS)
resource "aws_sns_topic_subscription" "sns_to_sqs" {
  topic_arn = aws_sns_topic.devops_sns_topic.arn
  protocol  = "sqs"
  endpoint  = aws_sqs_queue.devops_sqs_queue.arn
}
```

⚠️ Important:
Without the aws_sqs_queue_policy, SNS cannot deliver messages to SQS.


2️⃣ outputs.tf


```
output "kke_sns_topic_arn" {
  value = aws_sns_topic.devops_sns_topic.arn
}

output "kke_sqs_queue_url" {
  value = aws_sqs_queue.devops_sqs_queue.url
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

  # aws_sns_topic.devops_sns_topic will be created
  + resource "aws_sns_topic" "devops_sns_topic" {
      + arn                         = (known after apply)
      + beginning_archive_time      = (known after apply)
      + content_based_deduplication = false
      + fifo_topic                  = false
      + id                          = (known after apply)
      + name                        = "devops-sns-topic"
      + name_prefix                 = (known after apply)
      + owner                       = (known after apply)
      + policy                      = (known after apply)
      + signature_version           = (known after apply)
      + tags_all                    = (known after apply)
      + tracing_config              = (known after apply)
    }

  # aws_sns_topic_subscription.sns_to_sqs will be created
  + resource "aws_sns_topic_subscription" "sns_to_sqs" {
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

  # aws_sqs_queue.devops_sqs_queue will be created
  + resource "aws_sqs_queue" "devops_sqs_queue" {
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
      + name                              = "devops-sqs-queue"
      + name_prefix                       = (known after apply)
      + policy                            = (known after apply)
      + receive_wait_time_seconds         = 0
      + redrive_allow_policy              = (known after apply)
      + redrive_policy                    = (known after apply)
      + sqs_managed_sse_enabled           = (known after apply)
      + tags_all                          = (known after apply)
      + url                               = (known after apply)
      + visibility_timeout_seconds        = 30
    }

  # aws_sqs_queue_policy.sqs_policy will be created
  + resource "aws_sqs_queue_policy" "sqs_policy" {
      + id        = (known after apply)
      + policy    = (known after apply)
      + queue_url = (known after apply)
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_sns_topic_arn = (known after apply)
  + kke_sqs_queue_url = (known after apply)

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_sqs_queue.devops_sqs_queue: Creating...
aws_sns_topic.devops_sns_topic: Creating...
aws_sns_topic.devops_sns_topic: Creation complete after 1s [id=arn:aws:sns:us-east-1:000000000000:devops-sns-topic]
aws_sqs_queue.devops_sqs_queue: Still creating... [10s elapsed]
aws_sqs_queue.devops_sqs_queue: Still creating... [20s elapsed]
aws_sqs_queue.devops_sqs_queue: Creation complete after 26s [id=http://sqs.us-east-1.localhost.localstack.cloud:4566/000000000000/devops-sqs-queue]
aws_sqs_queue_policy.sqs_policy: Creating...
aws_sns_topic_subscription.sns_to_sqs: Creating...
aws_sns_topic_subscription.sns_to_sqs: Creation complete after 0s [id=arn:aws:sns:us-east-1:000000000000:devops-sns-topic:748fb525-7f72-49e8-96be-4547f1d65500]
aws_sqs_queue_policy.sqs_policy: Still creating... [10s elapsed]
aws_sqs_queue_policy.sqs_policy: Still creating... [20s elapsed]
aws_sqs_queue_policy.sqs_policy: Creation complete after 25s [id=http://sqs.us-east-1.localhost.localstack.cloud:4566/000000000000/devops-sqs-queue]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

Outputs:

kke_sns_topic_arn = "arn:aws:sns:us-east-1:000000000000:devops-sns-topic"
kke_sqs_queue_url = "http://sqs.us-east-1.localhost.localstack.cloud:4566/000000000000/devops-sqs-queue"
```

🧠 Step-by-Step Explanation (Simple & Clear)

Let’s explain this in plain English.

🔹 What problem is this lab solving?

The team wants:

One system to publish messages

Another system to process messages

Loose coupling (systems don’t talk directly)

SNS + SQS is perfect for this.

🔹 Roles in this setup

Think of it like this:

SNS Topic = 📣 broadcaster

SQS Queue = 📬 mailbox

Subscription = 🔌 connection

When SNS gets a message → it forwards it to SQS.

🔹 Step 1: Create SNS Topic
aws_sns_topic


This is where messages are published.

Example:

aws sns publish --topic-arn ... --message "Hello"

🔹 Step 2: Create SQS Queue
aws_sqs_queue


This stores messages until a consumer reads them.

Reliable, durable, retry-friendly.

🔹 Step 3: Why do we need a Queue Policy?

By default:
❌ SNS is not allowed to send messages to SQS.

So we explicitly say:

“SNS topic X is allowed to send messages to this queue.”

That’s what this does:

aws_sqs_queue_policy


Security + least privilege.

🔹 Step 4: Subscribe SQS to SNS
aws_sns_topic_subscription


This creates the actual connection:

Protocol = sqs

Endpoint = SQS queue ARN

Now the pipeline is live 🔥

🔹 What happens during terraform apply?

1️⃣ SNS topic is created
2️⃣ SQS queue is created
3️⃣ Queue policy allows SNS
4️⃣ SNS subscription is created
5️⃣ Terraform outputs ARN & URL

From now on:

Publish to SNS

Message appears in SQS

Consumer processes it

🧠 Easy Memory Model

SNS = 📣 push

SQS = 📥 pull

Subscription = 🔁 bridge

Policy = 🔐 permission

🚨 Common Mistakes 

❌ Forgetting SQS policy
❌ Using queue URL instead of ARN in subscription
❌ Wrong protocol (email instead of sqs)
❌ Outputting wrong attributes

---