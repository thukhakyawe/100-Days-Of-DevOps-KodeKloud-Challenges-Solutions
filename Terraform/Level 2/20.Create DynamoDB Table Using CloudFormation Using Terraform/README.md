1️⃣ locals.tf (ALREADY PROVIDED BY LAB)

⚠️ Do NOT modify this file




2️⃣ variables.tf

```
variable "KKE_DYNAMODB_TABLE_NAME" {
  type = string
}
```


3️⃣ main.tf

```
resource "aws_cloudformation_stack" "xfusion_dynamodb_stack" {
  name          = "xfusion-dynamodb-stack"
  template_body = local.cf_template_body

  lifecycle {
    ignore_changes = [parameters]
  }
}
```

4️⃣ outputs.tf

```
output "KKE_stack_name" {
  value = aws_cloudformation_stack.xfusion_dynamodb_stack.name
}
```

5️⃣ terraform.tfvars

```
KKE_DYNAMODB_TABLE_NAME = "xfusion-cf-dynamodb-table"
```

6️⃣ Terraform Commands (Run in Order)
terraform init
terraform validate
terraform apply


Type:

yes

✅ Expected Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_cloudformation_stack.xfusion_dynamodb_stack will be created
  + resource "aws_cloudformation_stack" "xfusion_dynamodb_stack" {
      + id            = (known after apply)
      + name          = "xfusion-dynamodb-stack"
      + outputs       = (known after apply)
      + parameters    = (known after apply)
      + policy_body   = (known after apply)
      + tags_all      = (known after apply)
      + template_body = jsonencode(
            {
              + AWSTemplateFormatVersion = "2010-09-09"
              + Resources                = {
                  + MyDynamoDBTable = {
                      + Properties = {
                          + AttributeDefinitions  = [
                              + {
                                  + AttributeName = "ID"
                                  + AttributeType = "S"
                                },
                            ]
                          + KeySchema             = [
                              + {
                                  + AttributeName = "ID"
                                  + KeyType       = "HASH"
                                },
                            ]
                          + ProvisionedThroughput = {
                              + ReadCapacityUnits  = 5
                              + WriteCapacityUnits = 5
                            }
                          + TableName             = "xfusion-cf-dynamodb-table"
                        }
                      + Type       = "AWS::DynamoDB::Table"
                    }
                }
            }
        )
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + KKE_stack_name = "xfusion-dynamodb-stack"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_cloudformation_stack.xfusion_dynamodb_stack: Creating...
aws_cloudformation_stack.xfusion_dynamodb_stack: Still creating... [10s elapsed]
aws_cloudformation_stack.xfusion_dynamodb_stack: Creation complete after 11s [id=arn:aws:cloudformation:us-east-1:000000000000:stack/xfusion-dynamodb-stack/7ef3e5b5-3134-4c81-a4c8-21a3a80a036b]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

KKE_stack_name = "xfusion-dynamodb-stack"
```

---

🧠 Step-by-Step Explanation (Simple & Clear)
🔹 What problem is this lab solving?

The team wants to:

Use CloudFormation, not direct Terraform resources

Automate DynamoDB creation

Manage CloudFormation using Terraform

So Terraform becomes the orchestrator, not the resource creator.

🔹 Why use CloudFormation inside Terraform?

Terraform can:

Create CloudFormation stacks

Track stack lifecycle

Handle stack updates and deletes

But the actual AWS resources (DynamoDB here) are created by CloudFormation.

Flow:

Terraform → CloudFormation Stack → DynamoDB Table

🔹 Why locals.tf is provided?

locals.tf stores:

The CloudFormation template body

As a HEREDOC string

With variable interpolation

This keeps:

Terraform clean

Template reusable

Lab controlled (you don’t write CF logic)

🔹 Why variables.tf is required?

The table name must be:

Injected into the CloudFormation template

Controlled via Terraform variable

Exact match required by grader

That’s why:

${var.KKE_DYNAMODB_TABLE_NAME}


is used inside the template.

🔹 Why ignore_changes = [parameters]?

CloudFormation stacks can mutate parameters internally.

Without this lifecycle rule:

Terraform may detect false drift

terraform plan may never be clean

Lab may fail grading

This line tells Terraform:

“Do NOT re-evaluate parameter changes.”

🔹 Why no DynamoDB Terraform resource?

The lab explicitly says:

Use CloudFormation to create DynamoDB

Creating:

aws_dynamodb_table


would fail the lab ❌

🔹 What happens during terraform apply?

1️⃣ Terraform creates CloudFormation stack
2️⃣ CloudFormation reads template
3️⃣ CloudFormation creates DynamoDB table
4️⃣ Terraform tracks stack state
5️⃣ Output returns stack name

🧠 Easy Memory Model

Terraform = 🧭 controller

CloudFormation = 🏗 builder

DynamoDB = 📦 result

locals.tf = 📜 template storage

lifecycle ignore = 🧯 drift protection

🚨 Common Mistakes 

❌ Creating DynamoDB directly in Terraform
❌ Editing locals.tf
❌ Missing lifecycle ignore_changes
❌ Outputting table name instead of stack name
❌ Using parameters block unnecessarily

---