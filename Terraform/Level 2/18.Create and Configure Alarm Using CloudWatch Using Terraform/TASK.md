The Nautilus DevOps team has been tasked with setting up an EC2 instance for their application. To ensure the application performs optimally, they also need to create a CloudWatch alarm to monitor the instance's CPU utilization. The alarm should trigger if the CPU utilization exceeds 90% for one consecutive 5-minute period. To send notifications, use the SNS topic named devops-sns-topic, which is already created.

    Launch EC2 Instance: Create an EC2 instance named devops-ec2 using any appropriate Ubuntu AMI (you can use AMI ami-0c02fb55956c7d316).

    Create CloudWatch Alarm: Create a CloudWatch alarm named devops-alarm with the following specifications:
        Statistic: Average
        Metric: CPU Utilization
        Threshold: >= 90% for 1 consecutive 5-minute period
        Alarm Actions: Send a notification to the devops-sns-topic SNS topic.

    Update the main.tf file (do not create a separate .tf file) to create a EC2 Instance and CloudWatch Alarm.

    Create an outputs.tf file to output the following values:

    KKE_instance_name for the EC2 instance name.
    KKE_alarm_name for the CloudWatch alarm name.
