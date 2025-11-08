# Step 1: Check Main Terraform Configuration

```
# main.tf

resource "aws_iam_group" "this" {
  name = "iamgroup_james"
}

```

# Check IAM Group

```
aws iam get-group --group-name iamgroup_james
```

Output

```
bob@iac-server ~/terraform via 💠 default ➜  aws iam get-group --group-name iamgroup_james
{
    "Users": [],
    "Group": {
        "Path": "/",
        "GroupName": "iamgroup_james",
        "GroupId": "qy0dq13t40i9exy3xtsb",
        "Arn": "arn:aws:iam::000000000000:group/iamgroup_james",
        "CreateDate": "2025-11-08T09:39:53.135474Z"
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
terraform destroy -target=aws_iam_group.this
```

Then type `yes` when prompted to confirm the creation of the snapshot.


Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform destroy -target=aws_iam_group.this
aws_iam_group.this: Refreshing state... [id=iamgroup_james]

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # aws_iam_group.this will be destroyed
  - resource "aws_iam_group" "this" {
      - arn       = "arn:aws:iam::000000000000:group/iamgroup_james" -> null
      - id        = "iamgroup_james" -> null
      - name      = "iamgroup_james" -> null
      - path      = "/" -> null
      - unique_id = "qy0dq13t40i9exy3xtsb" -> null
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

aws_iam_group.this: Destroying... [id=iamgroup_james]
aws_iam_group.this: Destruction complete after 0s
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
