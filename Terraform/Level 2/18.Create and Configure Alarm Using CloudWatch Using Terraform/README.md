1️⃣ main.tf

```
# SNS topic already provided by lab
resource "aws_sns_topic" "sns_topic" {
  name = "devops-sns-topic"
}

# EC2 instance
resource "aws_instance" "devops_ec2" {
  ami           = "ami-0c02fb55956c7d316"
  instance_type = "t2.micro"

  tags = {
    Name = "devops-ec2"
  }
}

# CloudWatch alarm (WITH depends_on to avoid drift)
resource "aws_cloudwatch_metric_alarm" "devops_alarm" {
  depends_on = [
    aws_instance.devops_ec2
  ]

  alarm_name          = "devops-alarm"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = 1
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"
  period              = 300
  statistic           = "Average"
  threshold           = 90

  alarm_actions = [
    aws_sns_topic.sns_topic.arn
  ]

  dimensions = {
    InstanceId = aws_instance.devops_ec2.id
  }
}
```

2️⃣ outputs.tf

```
output "KKE_instance_name" {
  value = aws_instance.devops_ec2.tags["Name"]
}

output "KKE_alarm_name" {
  value = aws_cloudwatch_metric_alarm.devops_alarm.alarm_name
}
```


3️⃣ Terraform commands (recommended)
terraform init
terraform validate
terraform apply -auto-approve

✅ Expected output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply -auto-approve
aws_sns_topic.sns_topic: Refreshing state... [id=arn:aws:sns:us-east-1:000000000000:devops-sns-topic]

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_cloudwatch_metric_alarm.devops_alarm will be created
  + resource "aws_cloudwatch_metric_alarm" "devops_alarm" {
      + actions_enabled                       = true
      + alarm_actions                         = [
          + "arn:aws:sns:us-east-1:000000000000:devops-sns-topic",
        ]
      + alarm_name                            = "devops-alarm"
      + arn                                   = (known after apply)
      + comparison_operator                   = "GreaterThanOrEqualToThreshold"
      + dimensions                            = (known after apply)
      + evaluate_low_sample_count_percentiles = (known after apply)
      + evaluation_periods                    = 1
      + id                                    = (known after apply)
      + metric_name                           = "CPUUtilization"
      + namespace                             = "AWS/EC2"
      + period                                = 300
      + statistic                             = "Average"
      + tags_all                              = (known after apply)
      + threshold                             = 90
      + treat_missing_data                    = "missing"
    }

  # aws_instance.devops_ec2 will be created
  + resource "aws_instance" "devops_ec2" {
      + ami                                  = "ami-0c02fb55956c7d316"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      + disable_api_stop                     = (known after apply)
      + disable_api_termination              = (known after apply)
      + ebs_optimized                        = (known after apply)
      + enable_primary_ipv6                  = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      + host_resource_group_arn              = (known after apply)
      + iam_instance_profile                 = (known after apply)
      + id                                   = (known after apply)
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_lifecycle                   = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t2.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = (known after apply)
      + monitoring                           = (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      + placement_partition_number           = (known after apply)
      + primary_network_interface_id         = (known after apply)
      + private_dns                          = (known after apply)
      + private_ip                           = (known after apply)
      + public_dns                           = (known after apply)
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + spot_instance_request_id             = (known after apply)
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Name" = "devops-ec2"
        }
      + tags_all                             = {
          + "Name" = "devops-ec2"
        }
      + tenancy                              = (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = (known after apply)

      + capacity_reservation_specification (known after apply)

      + cpu_options (known after apply)

      + ebs_block_device (known after apply)

      + enclave_options (known after apply)

      + ephemeral_block_device (known after apply)

      + instance_market_options (known after apply)

      + maintenance_options (known after apply)

      + metadata_options (known after apply)

      + network_interface (known after apply)

      + private_dns_name_options (known after apply)

      + root_block_device (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + KKE_alarm_name    = "devops-alarm"
  + KKE_instance_name = "devops-ec2"
aws_instance.devops_ec2: Creating...
aws_instance.devops_ec2: Still creating... [10s elapsed]
aws_instance.devops_ec2: Creation complete after 11s [id=i-5f800061a5b43d973]
aws_cloudwatch_metric_alarm.devops_alarm: Creating...
aws_cloudwatch_metric_alarm.devops_alarm: Creation complete after 0s [id=devops-alarm]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

Outputs:

KKE_alarm_name = "devops-alarm"
KKE_instance_name = "devops-ec2"
```


---

🧠 Step-by-Step Explanation (Simple & Clear)
🔹 What problem is this lab solving?

The team wants:

An EC2 instance for their app

Automatic alerts if CPU usage becomes too high

Notifications sent via SNS

This is basic production monitoring.

🔹 Why use CloudWatch CPUUtilization?

CPUUtilization:

Is a default EC2 metric

Requires no agent

Is perfect for detecting performance issues

🔹 Why 90% threshold?
threshold = 90


This means:

If CPU usage is 90% or higher

For one full 5-minute period

Trigger the alarm

This avoids alerts from short spikes.

🔹 Why period = 300 and evaluation_periods = 1?
period             = 300
evaluation_periods = 1


Translation:

Measure CPU every 5 minutes

Alarm fires if one data point breaches the threshold

Exactly what the lab asked for.

🔹 Why use a data source for SNS?
data "aws_sns_topic" "datacenter_topic"


Because:

The SNS topic already exists

We don’t want to recreate it

Data sources let Terraform read existing resources

🔹 How does the alarm know which EC2 to monitor?
dimensions = {
  InstanceId = aws_instance.datacenter_ec2.id
}


This binds the alarm to:

Only this EC2 instance

Not all EC2s in the account

🔹 What happens during terraform apply?

1️⃣ Terraform launches the EC2 instance
2️⃣ Terraform looks up the SNS topic ARN
3️⃣ Terraform creates the CloudWatch alarm
4️⃣ AWS starts monitoring CPU usage
5️⃣ If CPU ≥ 90% for 5 minutes → SNS notification sent

🧠 Easy memory model

EC2 → 🖥️ application server

CloudWatch → 📊 monitoring

Alarm → 🚨 condition check

SNS → 📢 notification delivery

🚨 Common mistakes (you avoided them)

❌ Wrong metric namespace
❌ Missing dimensions
❌ Using wrong period units
❌ Recreating SNS instead of referencing it
❌ Outputting wrong values

---