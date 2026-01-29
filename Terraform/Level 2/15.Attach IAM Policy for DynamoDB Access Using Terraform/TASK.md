The DevOps team has been tasked with creating a secure DynamoDB table and enforcing fine-grained access control using IAM. This setup will allow secure and restricted access to the table from trusted AWS services only.

As a member of the Nautilus DevOps Team, your task is to perform the following using Terraform:

    Create a DynamoDB Table: Create a table named datacenter-table with minimal configuration.

    Create an IAM Role: Create an IAM role named datacenter-role that will be allowed to access the table.

    Create an IAM Policy: Create a policy named datacenter-readonly-policy that should grant read-only access (GetItem, Scan, Query) to the specific DynamoDB table and attach it to the role.

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
