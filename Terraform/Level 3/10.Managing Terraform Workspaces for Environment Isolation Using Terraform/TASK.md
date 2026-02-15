The DevOps team is tasked with provisioning multiple API Gateway REST APIs and corresponding CloudWatch Log Groups using the following Terraform features:

    Create two workspaces named dev and prod.

    Create API Gateways named dev-xfusion-api-1 and prod-xfusion-api-2.

    Create matching CloudWatch Log Groups named /aws/apigateway/dev-xfusion-api-1 and /aws/apigateway/prod-xfusion-api-2.

    Use the count meta-argument to create multiple API Gateway REST APIs and matching log groups.

    Leverage terraform workspaces to differentiate API Gateway names per environment.

    Use local-exec provisioner to write a confirmation message to a log file once each resource is created.(e.g., Created API Gateway dev-xfusion-api-2 in workspace dev).

    Create two different files apigateway.log and loggroups.log in /home/bob/terraform to log the creation of each resource in their respective files.

    Use a list variable KKE_API_NAMES to define API names (e.g., ["xfusion-api-1", "xfusion-api-2"]).

    Createmain.tf file (do not create a separate .tf file) to provision the api gateway with matching log groups in different workspaces.

    Use variables.tf file with the following:
        KKE_API_NAMES = Names of API Gateways to create. 

    Use terraform.tfvars file to input the names of the API Gateways.

    Use outputs.tf file to output the following in the two different workspces ( devand prod).
        kke_api_gateway_names= name of the api gateway created.
        kke_log_group_names= name of the matching logroups created.
