✅ Part 1: Lab Step-by-Step Guidelines

Context

You will use Terraform to provision an Amazon DynamoDB table and insert items using the aws_dynamodb_table and aws_dynamodb_table_item resources.

Step 1: Create main.tf


```
resource "aws_dynamodb_table" "nautilus_tasks" {
  name         = var.KKE_TABLE_NAME
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "taskId"

  attribute {
    name = "taskId"
    type = "S"
  }
}

resource "aws_dynamodb_table_item" "task_1" {
  table_name = aws_dynamodb_table.nautilus_tasks.name
  hash_key   = aws_dynamodb_table.nautilus_tasks.hash_key

  item = <<ITEM
{
  "taskId": {"S": "1"},
  "description": {"S": "Learn DynamoDB"},
  "status": {"S": "completed"}
}
ITEM
}

resource "aws_dynamodb_table_item" "task_2" {
  table_name = aws_dynamodb_table.nautilus_tasks.name
  hash_key   = aws_dynamodb_table.nautilus_tasks.hash_key

  item = <<ITEM
{
  "taskId": {"S": "2"},
  "description": {"S": "Build To-Do App"},
  "status": {"S": "in-progress"}
}
ITEM
}
```

Step 2: Create variables.tf

```
variable "KKE_TABLE_NAME" {
  description = "Name of the DynamoDB table"
  type        = string
}
```

Step 3: Create terraform.tfvars

```
KKE_TABLE_NAME = "nautilus-tasks"
```

Step 4: Create outputs.tf

```
output "kke_dynamodb_table_name" {
  description = "Name of the DynamoDB table created"
  value       = aws_dynamodb_table.nautilus_tasks.name
}
```


Step 5: Initialize and Apply

```
terraform init
terraform validate
terraform apply
```

Confirm with yes when prompted.

Step 6: Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_dynamodb_table.nautilus_tasks will be created
  + resource "aws_dynamodb_table" "nautilus_tasks" {
      + arn              = (known after apply)
      + billing_mode     = "PAY_PER_REQUEST"
      + hash_key         = "taskId"
      + id               = (known after apply)
      + name             = "nautilus-tasks"
      + read_capacity    = (known after apply)
      + stream_arn       = (known after apply)
      + stream_label     = (known after apply)
      + stream_view_type = (known after apply)
      + tags_all         = (known after apply)
      + write_capacity   = (known after apply)

      + attribute {
          + name = "taskId"
          + type = "S"
        }

      + point_in_time_recovery (known after apply)

      + server_side_encryption (known after apply)

      + ttl (known after apply)
    }

  # aws_dynamodb_table_item.task_1 will be created
  + resource "aws_dynamodb_table_item" "task_1" {
      + hash_key   = "taskId"
      + id         = (known after apply)
      + item       = jsonencode(
            {
              + description = {
                  + S = "Learn DynamoDB"
                }
              + status      = {
                  + S = "completed"
                }
              + taskId      = {
                  + S = "1"
                }
            }
        )
      + table_name = "nautilus-tasks"
    }

  # aws_dynamodb_table_item.task_2 will be created
  + resource "aws_dynamodb_table_item" "task_2" {
      + hash_key   = "taskId"
      + id         = (known after apply)
      + item       = jsonencode(
            {
              + description = {
                  + S = "Build To-Do App"
                }
              + status      = {
                  + S = "in-progress"
                }
              + taskId      = {
                  + S = "2"
                }
            }
        )
      + table_name = "nautilus-tasks"
    }

Plan: 3 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_dynamodb_table_name = "nautilus-tasks"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_dynamodb_table.nautilus_tasks: Creating...
aws_dynamodb_table.nautilus_tasks: Creation complete after 4s [id=nautilus-tasks]
aws_dynamodb_table_item.task_1: Creating...
aws_dynamodb_table_item.task_2: Creating...
aws_dynamodb_table_item.task_1: Creation complete after 0s [id=nautilus-tasks|taskId|1]
aws_dynamodb_table_item.task_2: Creation complete after 0s [id=nautilus-tasks|taskId|2]

Apply complete! Resources: 3 added, 0 changed, 0 destroyed.

Outputs:

kke_dynamodb_table_name = "nautilus-tasks"
```



🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
What You’re Building

You are creating a cloud-based task list database using Terraform to automate everything.

Why DynamoDB?

No servers to manage

Fast lookups using a primary key

Perfect for small apps like a To-Do list

What Each File Does
main.tf

Connects Terraform to AWS

Creates a DynamoDB table named nautilus-tasks

Defines taskId as the unique identifier

Inserts two tasks automatically after the table is created

Think of this file as the blueprint + the data loader.

variables.tf

Lets you avoid hardcoding the table name

Makes your configuration reusable

terraform.tfvars

Supplies the actual table name value

Terraform reads this automatically

outputs.tf

Displays the DynamoDB table name after creation

Useful for verification and grading

How Verification Works

Because the tasks are inserted using Terraform:

If terraform apply succeeds

The tasks must exist with the correct statuses

No extra scripting required.

Common Mistakes to Avoid

❌ Creating multiple .tf files for resources

❌ Forgetting billing_mode = "PAY_PER_REQUEST"

❌ Using numeric DynamoDB types (N) instead of strings (S)

---