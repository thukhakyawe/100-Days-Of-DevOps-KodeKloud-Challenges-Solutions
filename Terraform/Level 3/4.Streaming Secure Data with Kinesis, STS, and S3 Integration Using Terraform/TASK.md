The Nautilus DevOps team is working on a secure cloud-native architecture using Terraform. As part of this, they need to provision streaming and storage infrastructure using only the allowed AWS services supported by LocalStack.

Your task as a DevOps engineer is to complete the following:

    Create a Kinesis Stream: Provision a stream named xfusion-dev-stream with 1 shard and a 24-hour retention policy.

    Create an S3 Bucket: Create a bucket named xfusion-dev-17158.

    Use STS for Identity Check: Retrieve and print the current AWS Account ID using aws_caller_identity.

    Ensure that the resources kinesis stream and s3 bucket are tagged with following:

        Environment : dev (both the resources)

        Purpose : Stream ingestion (Kinesis Stream)

        Owner : xfusion (S3-bucket)

    Add local-exec provisioners to output the creation messages and save them under the /home/bob/terraform directory. Specifically:

        When creating the Kinesis stream, write the following message to a file named kinesis_creation.log:

        "Kinesis Stream xfusion-dev-stream created"

        When creating the S3 bucket, write the message to a file named s3_creation.log:

        "S3 Bucket xfusion-dev-17158 created"

        When retrieving the STS caller identity, write the following message to a file named account_identity.log:

        "Logged in as account ID:<AWS account ID>"

    Create main.tf file (do not create a separate .tf file) to provision the kinesis stream, s3-bucket and retrieve the Current AWS Account ID.

    Use variables.tf file with the following variables:

        KKE_ENVIRONMENT: dev

        KKE_KINESIS_STREAM_NAME: Name of the Kinesis Stream (non-empty)

        KKE_S3_BUCKET_NAME: Name of the S3 bucket.

    Use terraform.tfvars to input the variable values.

    Use outputs.tf to output the following:

        kke_caller_identity_account_id: current AWS account ID.

        kke_kinesis_stream_name: name of the stream created.

        kke_s3_bucket_name: name of the bucket created.
