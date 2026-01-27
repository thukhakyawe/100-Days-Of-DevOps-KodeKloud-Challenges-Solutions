The Nautilus DevOps team wants to set up EC2 instances that securely upload application logs to S3 using IAM roles.

    Create an EC2 instance named xfusion-ec2 that can access an S3 bucket securely.

    Create an S3 bucket named xfusion-logs-8687.

    Create an IAM role named xfusion-role with a policy named xfusion-access-policy allowing S3 PutObject on the above bucket.

    Attach the IAM role to the EC2 instance to allow it to upload logs to the bucket.

    Create the main.tf (do not create a separate .tf file) to provision the EC2, s3, role and policy.

    Create the variables.tffile to declare the following:
        KKE_BUCKET_NAME: name of the bucket.
        KKE_POLICY_NAME: name of the policy.
        KKE_ROLE_NAME: name of the role.

    Create the terraform.tfvars file to assign values to variables.

    Create a data.tf file to fetch the latest Amazon Linux 2 AMI.
