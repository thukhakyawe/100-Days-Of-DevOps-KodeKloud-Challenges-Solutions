To enable secure inter-service communication, the DevOps team needs to configure access to an SNS topic using IAM roles and policies. The objective is to allow EC2 instances to publish messages to the topic using proper permissions and role assumptions. Please complete the following tasks:

    Create an SNS topic named datacenter-sns-topic.

    Create an IAM role named datacenter-sns-role with EC2 as the trusted entity.

    Attach an IAM policy named datacenter-sns-policy that grants permission to publish messages to the SNS topic.

    Use the main.tf file (do not create a separate .tf file) to provision the sns-topic, role and policy.

    Create the locals.tfwith the following names:
        KKE_SNS_TOPIC_NAME:name of the sns topic created.
        KKE_ROLE_NAME: name of the role created.
        KKE_POLICY_NAME: name of the policy created.

    Create the outputs.tf file to the output the following:

        The name of the SNS topic using the output variable kke_sns_topic_name.

        The name of the role using the output variable kke_role_name.

        The name of the policy using the output variable kke_policy_name.
