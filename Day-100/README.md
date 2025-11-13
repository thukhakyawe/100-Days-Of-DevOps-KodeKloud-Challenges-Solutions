


# Step 1: Create Main Terraform Configuration

```
# main.tf

# Exist SNS topic
resource "aws_sns_topic" "xfusion_sns_topic" {
  name = "xfusion-sns-topic"
}

# Create security group for EC2 instance
resource "aws_security_group" "xfusion_sg" {
  name        = "xfusion-sg"
  description = "Security group for xfusion-ec2 instance"

  # SSH access
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  # Allow all outbound traffic
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "xfusion-sg"
  }
}

# Create EC2 instance
resource "aws_instance" "xfusion_ec2" {
  ami           = "ami-0c02fb55956c7d316"  # Ubuntu AMI
  instance_type = "t2.micro"
  vpc_security_group_ids = [aws_security_group.xfusion_sg.id]

  tags = {
    Name = "xfusion-ec2"
  }
}

# Create CloudWatch alarm for CPU utilization
resource "aws_cloudwatch_metric_alarm" "xfusion_alarm" {
  alarm_name          = "xfusion-alarm"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = 1
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"
  period              = 300  # 5 minutes
  statistic           = "Average"
  threshold           = 90
  alarm_description   = "Alarm when CPU utilization exceeds 90% for 5 minutes"
  alarm_actions       = [aws_sns_topic.xfusion_sns_topic.arn]

  dimensions = {
    InstanceId = aws_instance.xfusion_ec2.id
  }

  tags = {
    Name = "xfusion-alarm"
  }
}
```

# Step 2: Create Outputs Terraform Configuration


```
# outputs.tf

output "KKE_instance_name" {
  description = "Name of the EC2 instance"
  value       = aws_instance.xfusion_ec2.tags.Name
}

output "KKE_alarm_name" {
  description = "Name of the CloudWatch alarm"
  value       = aws_cloudwatch_metric_alarm.xfusion_alarm.alarm_name
}
```



# Step 3: To deploy this configuration

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

# Plan the deployment to verify the configuration:

```
terraform plan
```



# Apply the configuration:

```
terraform apply
```

Then type `yes` when prompted to confirm the creation of the snapshot.


***


