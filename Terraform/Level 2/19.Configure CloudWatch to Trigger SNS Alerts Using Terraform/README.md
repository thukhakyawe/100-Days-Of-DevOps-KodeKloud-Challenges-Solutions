1️⃣ locals.tf
```
locals {
  KKE_SNS_TOPIC_NAME        = "nautilus-sns-topic"
  KKE_CLOUDWATCH_ALARM_NAME = "nautilus-cpu-alarm"
}
```


2️⃣ main.tf

```
# SNS Topic
resource "aws_sns_topic" "nautilus_topic" {
  name = local.KKE_SNS_TOPIC_NAME
}

# CloudWatch Alarm for EC2 CPU Utilization
resource "aws_cloudwatch_metric_alarm" "nautilus_cpu_alarm" {
  alarm_name          = local.KKE_CLOUDWATCH_ALARM_NAME
  comparison_operator = "GreaterThanThreshold"
  evaluation_periods  = 1
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"
  period              = 300
  statistic           = "Average"
  threshold           = 80
  actions_enabled     = true

  alarm_actions = [
    aws_sns_topic.nautilus_topic.arn
  ]
}
```

3️⃣ outputs.tf
```
output "KKE_sns_topic_name" {
  value = aws_sns_topic.nautilus_topic.name
}

output "KKE_cloudwatch_alarm_name" {
  value = aws_cloudwatch_metric_alarm.nautilus_cpu_alarm.alarm_name
}
```

4️⃣ Terraform Commands (Run in Order)
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

  # aws_cloudwatch_metric_alarm.nautilus_cpu_alarm will be created
  + resource "aws_cloudwatch_metric_alarm" "nautilus_cpu_alarm" {
      + actions_enabled                       = true
      + alarm_actions                         = (known after apply)
      + alarm_name                            = "nautilus-cpu-alarm"
      + arn                                   = (known after apply)
      + comparison_operator                   = "GreaterThanThreshold"
      + evaluate_low_sample_count_percentiles = (known after apply)
      + evaluation_periods                    = 1
      + id                                    = (known after apply)
      + metric_name                           = "CPUUtilization"
      + namespace                             = "AWS/EC2"
      + period                                = 300
      + statistic                             = "Average"
      + tags_all                              = (known after apply)
      + threshold                             = 80
      + treat_missing_data                    = "missing"
    }

  # aws_sns_topic.nautilus_topic will be created
  + resource "aws_sns_topic" "nautilus_topic" {
      + arn                         = (known after apply)
      + beginning_archive_time      = (known after apply)
      + content_based_deduplication = false
      + fifo_topic                  = false
      + id                          = (known after apply)
      + name                        = "nautilus-sns-topic"
      + name_prefix                 = (known after apply)
      + owner                       = (known after apply)
      + policy                      = (known after apply)
      + signature_version           = (known after apply)
      + tags_all                    = (known after apply)
      + tracing_config              = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + KKE_cloudwatch_alarm_name = "nautilus-cpu-alarm"
  + KKE_sns_topic_name        = "nautilus-sns-topic"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_sns_topic.nautilus_topic: Creating...
aws_sns_topic.nautilus_topic: Creation complete after 0s [id=arn:aws:sns:us-east-1:000000000000:nautilus-sns-topic]
aws_cloudwatch_metric_alarm.nautilus_cpu_alarm: Creating...
aws_cloudwatch_metric_alarm.nautilus_cpu_alarm: Creation complete after 1s [id=nautilus-cpu-alarm]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

Outputs:

KKE_cloudwatch_alarm_name = "nautilus-cpu-alarm"
KKE_sns_topic_name = "nautilus-sns-topic"
```

---

🧠 Step-by-Step Explanation (Simple & Clear)
🔹 What problem is this lab solving?

The team wants:

Automatic monitoring of EC2 CPU usage

Alerts when CPU usage becomes high

Notifications sent to a central alerting system

This is done using:

CloudWatch Alarm → SNS Topic → Notifications

🔹 Why use locals.tf?

locals are used because:

Names are fixed

No user input is required

Values are reused safely

Reduces typos and grading failures

Think of it as:

locals = constants inside Terraform

🔹 SNS Topic (aws_sns_topic)

This creates a notification channel.

CloudWatch does not send emails or messages directly —
it sends events to SNS, and SNS distributes them.

SNS = 📣 alert broadcaster

🔹 CloudWatch Alarm (aws_cloudwatch_metric_alarm)

This alarm:

Watches CPUUtilization

Uses Average CPU value

Checks every 5 minutes

Triggers if CPU is greater than 80%

Sends alert to SNS

🔹 Why no EC2 instance is created?

The lab does not ask for one.

CloudWatch alarms can exist without an instance.
Creating extra resources can cause grader failure.

👉 Minimal + exact = pass.

🔹 What happens during terraform apply?

1️⃣ SNS topic is created
2️⃣ CloudWatch alarm is created
3️⃣ Alarm is linked to SNS using ARN
4️⃣ AWS starts monitoring CPU metrics
5️⃣ Alerts are ready to be sent

🧠 Easy Memory Model

SNS topic = 📣 where alerts go

CloudWatch alarm = 🚨 condition checker

locals = 📌 fixed names

alarm_actions = 🔗 connection

🚨 Common Mistakes (You avoided them)

❌ Creating EC2 when not asked
❌ Using variables instead of locals
❌ Wrong output names
❌ Missing actions_enabled = true
❌ Using >= instead of correct comparator

---