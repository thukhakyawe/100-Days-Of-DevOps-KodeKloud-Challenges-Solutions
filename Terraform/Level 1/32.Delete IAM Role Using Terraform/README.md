# Step 1: Check Main Terraform Configuration

```
# main.tf

resource "aws_iam_role" "role" {
  name = "iamrole_john"

  assume_role_policy = jsonencode({
    Version = "2012-10-17",
    Statement = [
      {
        Effect    = "Allow"
        Principal = {
          Service = "ec2.amazonaws.com"
        }
        Action = "sts:AssumeRole"
      }
    ]
  })

  tags = {
    Name        = "iamrole_john"
  }
}
```

# Check IAM Role

```
aws iam get-role --role-name iamrole_john
```

Output

```
bob@iac-server ~/terraform via 💠 default ✖ aws iam get-role --role-name iamrole_john
{
    "Role": {
        "Path": "/",
        "RoleName": "iamrole_john",
        "RoleId": "AROAQAAAAAAAGLSAGK2JA",
        "Arn": "arn:aws:iam::000000000000:role/iamrole_john",
        "CreateDate": "2025-11-08T09:52:01.238539Z",
        "AssumeRolePolicyDocument": {
            "Statement": [
                {
                    "Action": "sts:AssumeRole",
                    "Effect": "Allow",
                    "Principal": {
                        "Service": "ec2.amazonaws.com"
                    }
                }
            ],
            "Version": "2012-10-17"
        },
        "MaxSessionDuration": 3600,
        "Tags": [
            {
                "Key": "Name",
                "Value": "iamrole_john"
            }
        ],
        "RoleLastUsed": {}
    }
}

```

# Step2: To deploy this configuration

# Navigate to the Terraform directory:

```
cd /home/bob/terraform
```

# Initialize Terraform:

```
terraform init
```

Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform init
Initializing the backend...
Initializing provider plugins...
- Finding hashicorp/aws versions matching "5.91.0"...
- Installing hashicorp/aws v5.91.0...
- Installed hashicorp/aws v5.91.0 (signed by HashiCorp)
Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```



# Destroy the configuration:

```
terraform destroy -target=aws_iam_role.role
```

Then type `yes` when prompted to confirm the creation of the snapshot.


Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform destroy -target=aws_iam_role.role
aws_iam_role.role: Refreshing state... [id=iamrole_john]

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # aws_iam_role.role will be destroyed
  - resource "aws_iam_role" "role" {
      - arn                   = "arn:aws:iam::000000000000:role/iamrole_john" -> null
      - assume_role_policy    = jsonencode(
            {
              - Statement = [
                  - {
                      - Action    = "sts:AssumeRole"
                      - Effect    = "Allow"
                      - Principal = {
                          - Service = "ec2.amazonaws.com"
                        }
                    },
                ]
              - Version   = "2012-10-17"
            }
        ) -> null
      - create_date           = "2025-11-08T09:52:01Z" -> null
      - force_detach_policies = false -> null
      - id                    = "iamrole_john" -> null
      - managed_policy_arns   = [] -> null
      - max_session_duration  = 3600 -> null
      - name                  = "iamrole_john" -> null
      - path                  = "/" -> null
      - tags                  = {
          - "Name" = "iamrole_john"
        } -> null
      - tags_all              = {
          - "Name" = "iamrole_john"
        } -> null
      - unique_id             = "AROAQAAAAAAAGLSAGK2JA" -> null
        # (3 unchanged attributes hidden)
    }

Plan: 0 to add, 0 to change, 1 to destroy.
╷
│ Warning: Resource targeting is in effect
│ 
│ You are creating a plan with the -target option, which means that the result of this
│ plan may not represent all of the changes requested by the current configuration.
│ 
│ The -target option is not for routine use, and is provided only for exceptional
│ situations such as recovering from errors or mistakes, or when Terraform specifically
│ suggests to use it as part of an error message.
╵

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

aws_iam_role.role: Destroying... [id=iamrole_john]
aws_iam_role.role: Destruction complete after 0s
╷
│ Warning: Applied changes may be incomplete
│ 
│ The plan was created with the -target option in effect, so some changes requested in
│ the configuration may have been ignored and the output values may not be fully updated.
│ Run the following command to verify that no other changes are pending:
│     terraform plan
│ 
│ Note that the -target option is not suitable for routine use, and is provided only for
│ exceptional situations such as recovering from errors or mistakes, or when Terraform
│ specifically suggests to use it as part of an error message.
╵

Destroy complete! Resources: 1 destroyed.
```

***
