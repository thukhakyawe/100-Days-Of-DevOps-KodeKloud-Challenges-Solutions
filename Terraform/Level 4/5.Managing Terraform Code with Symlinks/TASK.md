The DevOps team is building a Terraform-based AWS pipeline using strict modular design, symbolic links for configuration reuse, and a sequential resource flow

1) Create modules under modules/ named:

    SNS Module: Create an SNS topic named datacenter-sns-topic.
    SSM Module: Create an SSM Parameter named datacenter-param storing the ARN of the SNS topic from the SNS module. The SSM parameter should be of type String.
    Step Functions Module: Create a Step Functions state machine named datacenter-stepfunction that retrieves the SNS topic ARN from the SSM Parameter. The Step Function should have an IAM role with a policy allowing ssm:GetParameter access to read the parameter.

2) Use symbolic links to reuse the root variables.tf file across all modules (no duplicated variable declarations inside module main.tf files). Ensure the symlink uses an absolute path.

3) Create a single main.tf in the root to orchestrate the module calls in sequence SNS → SSM → Step Functions. Pass the SNS ARN output from the SNS module to the SSM module, and the SSM parameter name output from the SSM module to the Step Functions module.

4) Use the depends_on feature so that SSM depends on SNS and StepFunctions depend on SSM.

5) Use variables.tf file with the following variable names:

    KKE_SNS_TOPIC_NAME: name of the SNS topic.
    KKE_SSM_PARAM_NAME: SSM parameter name.
    KKE_STEP_FUNCTION_NAME: Step Function name.

6) Use terraform.tfvars file to input the values of the variables.

7) Use outputs.tf file with the following variables:

    kke_sns_topic_name: name of the SNS topic created.
    kke_ssm_parameter_name: name of the SSM parameter created.
    kke_step_function_name: name of the Step Function created.

8) Additional implementation hints:

    SNS Module: output both name and ARN of the topic.
    SSM Module: set the value of the parameter to the SNS ARN received from the SNS module. Also, ensure the SSM parameter implementation creates a direct Terraform dependency on the SNS topic so that the dependency is visible in the Terraform graph.
    Step Functions Module: create an IAM role and policy allowing ssm:GetParameter, then assign the role to the state machine. The Step Function can use a simple placeholder definition (e.g., Pass state) for this task.
