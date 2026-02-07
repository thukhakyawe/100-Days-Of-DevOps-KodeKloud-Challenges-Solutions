The DevOps team needs to create a data ingestion pipeline using AWS Kinesis Firehose to deliver streaming data into an S3 bucket. The Firehose delivery stream must assume an IAM role using STS, deliver data to an S3 bucket, and add a newline delimiter after each record. Buffering settings should be configured to deliver data either when the buffer reaches 5 MB or after 300 seconds, whichever comes first.

You are required to complete this task using Terraform.

Task Requirements:

    Create an S3 bucket named xfusion-stream-bucket-28590 using Terraform.

    Create an IAM role named firehose-sts-role and policy that allows Kinesis Firehose to put objects into the S3 bucket.

    The Firehose delivery stream must use the IAM role via STS assume role.

    Use depends_on in the Firehose resource to ensure it waits for the IAM role policy attachment.

    Create a Firehose delivery stream named xfusion-firehose-stream to deliver data to the S3 bucket.

    Configure buffering with size 5 MB and interval 300 seconds.

    Enable record processing by setting the Delimiter parameter to \n to append a newline after each record.

    Create main.tf file to create a S3 bucket,IAM role and Firehose delivery stream.

    Use variables.tf file with the following variables:
        KKE_S3_BUCKET_NAME: name of the bucket.
        KKE_FIREHOSE_STREAM_NAME: name of the firehose stream.
        KKE_FIREHOSE_ROLE_NAME : name of the firehose role 

    Use outputs.tf file to output the following:
        kke_firehose_stream_name: name of the firehose stream created.
        kke_s3_bucket_name: name of the bucket created.
        kke_firehose_role_arn: arn of the created firehose role.

    Send test data to the Firehose stream and verify that each record in the S3 files ends with a newline character.
