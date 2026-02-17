The DevOps team is tasked with designing and implementing a production‑grade, event‑driven infrastructure entirely using Terraform. This initiative is part of our internal platform engineering efforts to standardize infrastructure as code (IaC) practices across the organization.

Task Requirements:

    Use a locals block in your Terraform configuration to define the following:

        project name ( devops).

        environment (dev),

        A common name prefix by combining the project name and environment ( devops-dev).

        A map of default tags ( Project, Environment, Owner, and Team).

        Then, reference these locals values across all resources in your configuration to ensure consistent naming and consistent tagging.

    Create a SNS topic named project name-environment-topic.

    Create a SQS queue named project name-environment-queue and subscribe it to the SNS topic.

    Use depends_on to ensure the SNS topic is created before subscription.

    Create a DynamoDB table named project name-environment-events with primary key event_id (HASH key) and provisioned throughput of 5 read capacity units and 5 write capacity units.

    Create an IAM Role named project name-environment-role with a `dynamic inline policy allowing:
        sqs:ReceiveMessage
        dynamodb:PutItem
        sns:Publish

    Use validation blocks invariables.tf to:
        Restrict allowed AWS regions to only us-east-1 (with error message)
        Ensure the SNS queue depth threshold is between 1 and 1000 (with the error message).

    Use main.tf file to organize all AWS resources in a clean, modular, and easily maintainable Terraform configuration.

    Create a CloudWatch alarm named project name-environment-alarm for the SQS queue when it contains more than 50 messages (threshold configurable).

    Use variables.tf file with the following variables:
        KKE_AWS_REGION:aws region used.
        KKE_QUEUE_DEPTH_THRESHOLD:CloudWatch alarm threshold for queue depth.(default=50).
        KKE_IAM_ACTIONS:IAM actions to allow in dynamic policy.

    Use outputs.tf file to output the following:

    kke_cloudwatch_alarm_name:name of the alarm created.
    kke_dynamodb_table_name:name of the table created.
    kke_iam_role_arn: arn of the role created.
    kke_sns_topic_arn: arn of the sns-topic created.
    kke_sqs_queue_url: url of the sqs-queue created.
