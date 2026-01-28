The Nautilus DevOps team is implementing a messaging system in AWS. They want to create an SNS topic and an SQS queue. The team needs to subscribe the SQS queue to the SNS topic so that any messages sent to the SNS topic will be delivered to the SQS queue.

    Create an SNS topic named devops-sns-topic.

    Create an SQS queue named devops-sqs-queue.

    Subscribe the SQS queue to the SNS topic.

    Use the main.tf file (do not create a separate .tf file) to provision the SNS topic and SQS queue.

    Create the outputs.tf file, and use the following:

        The ARN of the SNS topic using the output variable kke_sns_topic_arn.

        The URL of the SQS queue using the output variable kke_sqs_queue_url.
