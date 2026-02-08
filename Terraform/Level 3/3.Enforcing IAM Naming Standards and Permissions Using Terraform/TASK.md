The Nautilus DevOps team is adopting strict naming conventions for all IAM resources using Terraform. They’ve asked for help enforcing lowercase, hyphenated names based on inputs like project and team.

Your task as a DevOps engineer is to complete the following using Terraform:

    Create an IAM User The user name must be derived using the format project-team-user, all lowercase, and non-alphanumeric characters (except dashes) replaced with -.

    Create an IAM Role Use the same naming logic for the role name, ending in -role, and attach an assume role policy for EC2.

    Tagging: Both resources must be tagged with:
        Project: xfusion
        Team: dev-team
        ManagedBy: Terraform
        Env: dev

    Additionally, the IAM role should have:
        RoleType: EC2

    Use locals block within main.tf to:
        Derive sanitized project/team names
        Create the resource name prefix
        Define reusable common tags

    Create the main.tf file (do not create a separate .tf file) to provision the IAM Role & User as per the required values.

    Use variables.tffile with the following:
        KKE_PROJECT: name of the project(must be non-empty).
        KKE_TEAM: name of the team (only letters, digits, dashes or underscores)
        KKE_ENVIRONMENT: name of the environment

    Use terraform.tfvarsfile to input the values.

    Use outputs.tffile to output the following:
        kke_user_name: name of the created user.
        kke_role_name: name of the created role.
        kke_tags_applied: tags applied to the IAM User.
