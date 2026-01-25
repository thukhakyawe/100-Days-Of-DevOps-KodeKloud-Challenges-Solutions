The monitoring team wants to improve observability into the streaming infrastructure. Your task is to implement a solution using Amazon Kinesis and CloudWatch. The team wants to ensure that if write throughput exceeds provisioned limits, an alert is triggered immediately.

As a member of the Nautilus DevOps Team, perform the following tasks using Terraform:

    Create a Kinesis Data Stream: Name the stream xfusion-kinesis-stream with a shard count of 1.

    Enable Monitoring: Enable shard-level metrics for the stream to track ingestion and throughput errors.

    Create a CloudWatch Alarm: Name the alarm xfusion-kinesis-alarm and monitor the WriteProvisionedThroughputExceeded metric. The alarm should trigger if the metric exceeds a threshold of 1.

    Ensure Alerting: Configure the CloudWatch alarm to detect write throughput issues exceeding provisioned limits.

    Create the main.tf file (do not create a separate .tf file) to provision the Kinesis stream, CloudWatch alarm, and ensure alerting.

    Create the outputs.tf file with the following variable names to output:

        kke_kinesis_stream_name for the Kinesis stream name.

        kke_kinesis_alarm_name for the CloudWatch alarm name.
