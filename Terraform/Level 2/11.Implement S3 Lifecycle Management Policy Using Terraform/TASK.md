The Nautilus DevOps team is implementing lifecycle policies to manage object storage efficiently in AWS. They want to create an S3 bucket with a specific lifecycle rule that transitions objects to infrequent access (IA) storage after 30 days and deletes them after 365 days.

    Create an S3 bucket named devops-lifecycle-721.

    Enable the S3 Versioning on the bucket.

    Add a lifecycle rule named devops-lifecycle-rule with:
        Transition to STANDARD_IA storage class after 30 days.
        Expiration of objects after 365 days.

    Use the main.tf file (do not create a separate .tf file) to provision the S3 bucket.

    Use the variable name KKE_bucket_name in the outputs.tf file to output the created bucket name.
