# Task 1

The DevOps team has been tasked with creating a secure DynamoDB table and enforcing fine-grained access control using IAM. This setup will allow secure and restricted access to the table from trusted AWS services only.

As a member of the Nautilus DevOps Team, your task is to perform the following using Terraform:

    Create a DynamoDB Table: Create a table named devops-table-t1q4 with minimal configuration.

    Create an IAM Role: Create an IAM role named devops-role-t1q4 that will be allowed to access the table.

    Create an IAM Policy: Create a policy named devops-readonly-policy-t1q4 that should grant read-only access (GetItem, Scan, Query) to the specific DynamoDB table and attach it to the role.

    Create the main.tf file (do not create a separate .tf file) to provision the table, role, and policy.

    Create the variables.tf file with the following variables:
        KKE_TABLE_NAME: name of the DynamoDB table
        KKE_ROLE_NAME: name of the IAM role
        KKE_POLICY_NAME: name of the IAM policy

    Create the outputs.tf file with the following outputs:
        kke_dynamodb_table: name of the DynamoDB table
        kke_iam_role_name: name of the IAM role
        kke_iam_policy_name: name of the IAM policy

    Define the actual values for these variables in the terraform.tfvars file.

    Ensure that the IAM policy allows only read access and restricts it to the specific DynamoDB table created.

---
# Task 2

The Nautilus DevOps team is expanding their AWS infrastructure and requires the setup of a CloudWatch alarm and SNS integration for monitoring EC2 instances. The team needs to configure an SNS topic for CloudWatch to publish notifications when an EC2 instance’s CPU utilization exceeds 80%. The alarm should trigger whenever the CPU utilization is greater than 80% and notify the SNS topic to alert the team.

    Create an SNS topic named devops-sns-topic-t1q2.

    Create a CloudWatch alarm named devops-cpu-alarm-t1q2 to monitor EC2 CPU utilization with the following conditions:
        Metric: CPUUtilization
        Threshold: 80%
        Actions enabled
        Alarm actions should be triggered to the SNS topic.

    Ensure that the SNS topic receives notifications from the CloudWatch alarm when it is triggered.

    Update the main.tf file (do not create a different .tf file) to create SNS Topic and Cloudwatch Alarm.

    Create an outputs.tf file to output the following values:

    KKE_sns_topic_name for the SNS topic name.
    KKE_cloudwatch_alarm_name for the CloudWatch alarm name. 

---

# Task 3

The Nautilus DevOps team is expanding their AWS infrastructure and requires the setup of a private Virtual Private Cloud (VPC) along with a subnet. This VPC and subnet configuration will ensure that resources deployed within them remain isolated from external networks and can only communicate within the VPC. Additionally, the team needs to provision an EC2 instance under the newly created private VPC. This instance should be accessible only from within the VPC, allowing for secure communication and resource management within the AWS environment.

    Create a VPC named devops-priv-vpc-t2q3 with the CIDR block 10.0.0.0/16.

    Create a subnet named devops-priv-subnet-t2q3 inside the VPC with the CIDR block 10.0.1.0/24 and auto-assign IP option must not be enabled.

    Create an EC2 instance named devops-priv-ec2-t2q3 inside the subnet and instance type must be t2.micro.

    Ensure the security group of the EC2 instance allows access only from within the VPC's CIDR block.

    Create the main.tf file (do not create a separate .tf file) to provision the VPC, subnet and EC2 instance.

    Use variables.tf file with the following variable names:
        KKE_VPC_CIDR for the VPC CIDR block.
        KKE_SUBNET_CIDR for the subnet CIDR block.

    Use the outputs.tf file with the following variable names:
        KKE_vpc_name for the name of the VPC.
        KKE_subnet_name for the name of the subnet.
        KKE_ec2_private for the name of the EC2 instance.

--- 

# Task 4

To test resilience and recreation behavior in Terraform, the DevOps team needs to demonstrate the use of the -replace option to forcefully recreate an EC2 instance without changing its configuration. Please complete the following tasks:

    Use the Terraform CLI -replace option to destroy and recreate the EC2 instance devops-ec2-t2q1, even though the configuration remains unchanged.

    Ensure that the instance is recreated successfully.


Notes:

    The new instance created using the -replace option should have a different instance ID than the previously provisioned instance.

    The Terraform working directory is /home/bob/terraform/t2q1.

    Right-click under the EXPLORER section in VS Code and select Open in Integrated Terminal to launch the terminal.

    Before submitting the task, ensure that terraform plan returns No changes. Your infrastructure matches the configuration.

---

# Task 5

The Nautilus DevOps team wants to provision multiple EC2 instances in AWS using Terraform. Each instance should follow a consistent naming convention and be deployed using a modular and scalable setup.

Use Terraform to:

    Create 3 EC2 instances using the count parameter.

    Name each EC2 instance with the prefix devops-instance-t3q4 (e.g., devops-instance-t3q4-1).

    Instances should be t2.micro.

    The key named should be devops-key-t3q4.

    Create main.tf file (do not create a separate .tf file) to provision these instances.

    Use variables.tf file with the following:
        KKE_INSTANCE_COUNT: number of instances.
        KKE_INSTANCE_TYPE: type of the instance.
        KKE_KEY_NAME: name of key used.
        KKE_INSTANCE_PREFIX: name of the instnace.

    Use the locals.tf file to define a local variable named AMI_ID that retrieves the latest Amazon Linux 2 AMI using a data source.

    Use terraform.tfvars to assign values to the variables.

    Use outputs.tf file to output the following:
        kke_instance_names: names of the instances created.


---

# TASK 6

The Nautilus DevOps team is experimenting with Terraform provisioners. Your task is to create an IAM user and use a local-exec provisioner to log a confirmation message.

    Create an IAM user named iamuser_ravi_t3q2.

    Use a local-exec provisioner with the IAM user resource to log the message KKE iamuser_ravi_t3q2 has been created successfully! to a file called KKE_user_created.log under home/bob/terraform/t3q2.

    Create the main.tf file (do not create a separate .tf file) to provision an IAM user.

    Use variables.tf file with the following:
        KKE_USER_NAME: name of the IAM user.

    Use terraform.tfvars to input the name of the IAM user.

    Use outputs.tf file with the following:
        kke_iam_user_name: name of the IAM user.

---

# Task 7

As part of a data migration project, the team lead has tasked the team with migrating data from an existing S3 bucket to a new S3 bucket. The existing bucket contains a substantial amount of data that must be accurately transferred to the new bucket. The team is responsible for creating the new S3 bucket using Terraform and ensuring that all data from the existing bucket is copied or synced to the new bucket completely and accurately. It is imperative to perform thorough verification steps to confirm that all data has been successfully transferred to the new bucket without any loss or corruption.

As a member of the Nautilus DevOps Team, your task is to perform the following using Terraform:

    Create a New Private S3 Bucket: Name the bucket devops-sync-5915-t4q4 and store this bucket name in a variable named KKE_BUCKET.

    Data Migration: Migrate all data from the existing devops-s3-16124-t4q4 bucket to the new devops-sync-5915-t4q4 bucket.

    Ensure Data Consistency: Ensure that both buckets contain the same data after migration.

    Update the main.tf file (do not create a separate .tf file) to provision a new private S3 bucket and migrate the data.

    Use the variables.tf file with the following variable:
        KKE_BUCKET: The name for the new bucket created.

    Use the outputs.tf file with the following outputs:

        new_kke_bucket_name: The name of the new bucket created.

        new_kke_bucket_acl: The ACL of the new bucket created.

---

# TASK 8


To ensure secure and accidental-deletion-proof storage, the DevOps team must configure an S3 bucket using Terraform with strict lifecycle protections. The goal is to create a bucket that is dynamically named and protected from being destroyed by mistake. Please complete the following tasks:

    Create an S3 bucket named devops-s3-28270-t4q2.

    Apply the prevent_destroy lifecycle rule to protect the bucket.

    Create the main.tf file (do not create a separate .tf file) to provision a s3 bucket with prevent_destroy lifecycle rule.

    Use the variables.tf file with the following:
        KKE_BUCKET_NAME: name of the bucket.

    Use the terraform.tfvars file to input the name of the bucket.

    Use the outputs.tffile with the following:
        s3_bucket_name: name of the created bucket.


---