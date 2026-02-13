The Nautilus DevOps team has been tasked with creating an internal information portal for public access. As part of this project, they need to host a static website on AWS using an S3 bucket. The S3 bucket must be configured for public access to allow external users to access the static website directly via the S3 website URL.

Your task is to create a Terraform module named s3-static-site to handle the creation and configuration of the S3 bucket. For uploading the index.html file, you may use either Terraform or the AWS CLI.

Task Requirements:

The module directory /home/bob/terraform/modules/s3-static-site/ is already created, configure the module to perform the following tasks:

    Create an S3 bucket named nautilus-web-28947.

    Configure the S3 bucket for static website hosting with index.html as the index document.

    Allow public access to the bucket by attaching the appropriate bucket policy.

    Within the module, use a variables.tf file that must define the following variables: bucket_name and index_document. These values should not be hardcoded directly into resource definitions. You may add other variables if needed to avoid hardcoding. Use these variables in main.tf for configuring the bucket.

    Within the module use outputs.tf file to output the following:
        website_url: S3 static website url

    Your S3 website url should look something like the following, aws:4566 refers to the mock AWS endpoint configured in your environment (e.g., using LocalStack):
        http://aws:4566/<bucketname>/index.html

    The S3 bucket must be tagged with the key Project and the value StaticWeb.

    In the root main.tf, call the s3-static-site module using the required input variables (bucket_name, index_document).

    Upload the index.html file from /home/bob/terraform directory to the S3 bucket. This can be done using either the AWS CLI or Terraform (aws_s3_object).
