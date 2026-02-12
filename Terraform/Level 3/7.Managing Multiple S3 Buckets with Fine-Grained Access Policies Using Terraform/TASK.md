The Nautilus DevOps team needs to set up three S3 buckets for different environments with backup and policy configurations. Follow the steps below:

    Create three S3 buckets using for_each for environments: Dev, Staging, and Prod.

    Name the buckets using the following naming convention:
        datacenter-dev-bucket-16801
        datacenter-staging-bucket-16801
        datacenter-prod-bucket-16801

    Add the following tags to each bucket with the corresponding values:

    a.) For datacenter-dev-bucket-16801:
        Name = datacenter-dev-bucket-16801
        Environment = Dev
        Owner = Alice

    b.) For datacenter-staging-bucket-16801:
        Name = datacenter-staging-bucket-16801
        Environment = Staging
        Owner = Bob

    c.) For datacenter-prod-bucket-16801:
        Environment = Prod
        Owner = Carol

    For the staging and prod buckets, set Backup = true and add a lifecycle rule with ID MoveToGlacier to transition objects to Glacier after 30 days.

    Use the lifecycle block with ignore_changes to protect the tags.

    Create a bucket policy that allows public read access to all objects in the bucket.

    Use depends_on to ensure the policy is only applied after the bucket has been created.

    Implement the entire configuration in a single main.tf file (do not create a separate .tf file) to provision multiple S3 buckets with the specified configurations.

    Use variables.tf with the following variable:
        KKE_ENV_TAGS. KKE_ENV_TAGS is a map that holds environment-specific metadata such as bucket name, owner, and backup flag.

    Use outputs.tf file to output the following:
        kke_bucket_names: output the names of the bucket created. 
