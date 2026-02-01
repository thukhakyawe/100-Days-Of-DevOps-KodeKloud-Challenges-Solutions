The Nautilus DevOps team wants to automate infrastructure provisioning using CloudFormation. As part of the stack setup, they need to create a DynamoDB table.

    Create a CloudFormation stack named xfusion-dynamodb-stack.

    The stack must create a DynamoDB table named xfusion-cf-dynamodb-table.

    Use the main.tf file (do not create a separate .tf file) to provision a CloudFormation stack and DynamoDB table. Make sure to add a lifecycle block in main.tf to ignore changes to the parameters attribute.

    Use the variables.tf file with the following variable names:
        KKE_DYNAMODB_TABLE_NAME: Dynamodb table name.

    The locals.tf file is already provided and includes the following:
        cf_template_body: A local variable that stores the CloudFormation template body.

    Use the outputs.tf file to output the following:
        KKE_stack_name: CloudFormation stack name
