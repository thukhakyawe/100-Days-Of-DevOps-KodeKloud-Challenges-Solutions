🧭 Part 1: Lab Step-by-Step Guidelines


Step 1 — Create Workspaces

From /home/bob/terraform:

terraform init
terraform workspace new dev
terraform workspace new prod


Verify:

terraform workspace list

Step 2 — Define Variables

📄 variables.tf

```
variable "KKE_API_NAMES" {
  description = "Names of API Gateways to create"
  type        = list(string)
}
```

Step 3 — Provide Values

📄 terraform.tfvars

```
KKE_API_NAMES = ["xfusion-api-1", "xfusion-api-2"]
```
Step 4 — Create Infrastructure

📄 main.tf

```
locals {
  workspace_prefix = terraform.workspace
}

# -----------------------------
# API Gateway REST APIs
# -----------------------------
resource "aws_api_gateway_rest_api" "kke_api" {
  count = length(var.KKE_API_NAMES)

  name = "${local.workspace_prefix}-${var.KKE_API_NAMES[count.index]}"

  provisioner "local-exec" {
    command = "echo Created API Gateway ${local.workspace_prefix}-${var.KKE_API_NAMES[count.index]} in workspace ${terraform.workspace} >> /home/bob/terraform/apigateway.log"
  }
}

# -----------------------------
# CloudWatch Log Groups
# -----------------------------
resource "aws_cloudwatch_log_group" "kke_logs" {
  count = length(var.KKE_API_NAMES)

  name = "/aws/apigateway/${local.workspace_prefix}-${var.KKE_API_NAMES[count.index]}"

  provisioner "local-exec" {
    command = "echo Created Log Group /aws/apigateway/${local.workspace_prefix}-${var.KKE_API_NAMES[count.index]} in workspace ${terraform.workspace} >> /home/bob/terraform/loggroups.log"
  }
}
```

Step 5 — Define Outputs

📄 outputs.tf

```
output "kke_api_gateway_names" {
  description = "Names of API Gateways created"
  value       = aws_api_gateway_rest_api.kke_api[*].name
}

output "kke_log_group_names" {
  description = "Names of matching CloudWatch Log Groups"
  value       = aws_cloudwatch_log_group.kke_logs[*].name
}
```


Step 6 — Deploy in DEV Workspace
terraform workspace select dev
terraform apply -auto-approve


Output

```
bob@iac-server ~/terraform via 💠 dev ➜  terraform apply -auto-approve

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_api_gateway_rest_api.kke_api[0] will be created
  + resource "aws_api_gateway_rest_api" "kke_api" {
      + api_key_source               = (known after apply)
      + arn                          = (known after apply)
      + binary_media_types           = (known after apply)
      + created_date                 = (known after apply)
      + description                  = (known after apply)
      + disable_execute_api_endpoint = (known after apply)
      + execution_arn                = (known after apply)
      + id                           = (known after apply)
      + minimum_compression_size     = (known after apply)
      + name                         = "dev-xfusion-api-1"
      + policy                       = (known after apply)
      + root_resource_id             = (known after apply)
      + tags_all                     = (known after apply)

      + endpoint_configuration (known after apply)
    }

  # aws_api_gateway_rest_api.kke_api[1] will be created
  + resource "aws_api_gateway_rest_api" "kke_api" {
      + api_key_source               = (known after apply)
      + arn                          = (known after apply)
      + binary_media_types           = (known after apply)
      + created_date                 = (known after apply)
      + description                  = (known after apply)
      + disable_execute_api_endpoint = (known after apply)
      + execution_arn                = (known after apply)
      + id                           = (known after apply)
      + minimum_compression_size     = (known after apply)
      + name                         = "dev-xfusion-api-2"
      + policy                       = (known after apply)
      + root_resource_id             = (known after apply)
      + tags_all                     = (known after apply)

      + endpoint_configuration (known after apply)
    }

  # aws_cloudwatch_log_group.kke_logs[0] will be created
  + resource "aws_cloudwatch_log_group" "kke_logs" {
      + arn               = (known after apply)
      + id                = (known after apply)
      + log_group_class   = (known after apply)
      + name              = "/aws/apigateway/dev-xfusion-api-1"
      + name_prefix       = (known after apply)
      + retention_in_days = 0
      + skip_destroy      = false
      + tags_all          = (known after apply)
    }

  # aws_cloudwatch_log_group.kke_logs[1] will be created
  + resource "aws_cloudwatch_log_group" "kke_logs" {
      + arn               = (known after apply)
      + id                = (known after apply)
      + log_group_class   = (known after apply)
      + name              = "/aws/apigateway/dev-xfusion-api-2"
      + name_prefix       = (known after apply)
      + retention_in_days = 0
      + skip_destroy      = false
      + tags_all          = (known after apply)
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_api_gateway_names = [
      + "dev-xfusion-api-1",
      + "dev-xfusion-api-2",
    ]
  + kke_log_group_names   = [
      + "/aws/apigateway/dev-xfusion-api-1",
      + "/aws/apigateway/dev-xfusion-api-2",
    ]
aws_cloudwatch_log_group.kke_logs[0]: Creating...
aws_cloudwatch_log_group.kke_logs[1]: Creating...
aws_api_gateway_rest_api.kke_api[1]: Creating...
aws_api_gateway_rest_api.kke_api[0]: Creating...
aws_cloudwatch_log_group.kke_logs[1]: Provisioning with 'local-exec'...
aws_cloudwatch_log_group.kke_logs[0]: Provisioning with 'local-exec'...
aws_cloudwatch_log_group.kke_logs[1] (local-exec): Executing: ["/bin/sh" "-c" "echo Created Log Group /aws/apigateway/dev-xfusion-api-2 in workspace dev >> /home/bob/terraform/loggroups.log"]
aws_cloudwatch_log_group.kke_logs[0] (local-exec): Executing: ["/bin/sh" "-c" "echo Created Log Group /aws/apigateway/dev-xfusion-api-1 in workspace dev >> /home/bob/terraform/loggroups.log"]
aws_cloudwatch_log_group.kke_logs[0]: Creation complete after 1s [id=/aws/apigateway/dev-xfusion-api-1]
aws_cloudwatch_log_group.kke_logs[1]: Creation complete after 1s [id=/aws/apigateway/dev-xfusion-api-2]
aws_api_gateway_rest_api.kke_api[0]: Provisioning with 'local-exec'...
aws_api_gateway_rest_api.kke_api[0] (local-exec): Executing: ["/bin/sh" "-c" "echo Created API Gateway dev-xfusion-api-1 in workspace dev >> /home/bob/terraform/apigateway.log"]
aws_api_gateway_rest_api.kke_api[0]: Creation complete after 3s [id=y32lm3wk1k]
aws_api_gateway_rest_api.kke_api[1]: Provisioning with 'local-exec'...
aws_api_gateway_rest_api.kke_api[1] (local-exec): Executing: ["/bin/sh" "-c" "echo Created API Gateway dev-xfusion-api-2 in workspace dev >> /home/bob/terraform/apigateway.log"]
aws_api_gateway_rest_api.kke_api[1]: Creation complete after 3s [id=5nh4lghesg]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

Outputs:

kke_api_gateway_names = [
  "dev-xfusion-api-1",
  "dev-xfusion-api-2",
]
kke_log_group_names = [
  "/aws/apigateway/dev-xfusion-api-1",
  "/aws/apigateway/dev-xfusion-api-2",
]
```

Step 7 — Deploy in PROD Workspace
terraform workspace select prod
terraform apply -auto-approve


Output

```
bob@iac-server ~/terraform via 💠 prod ➜  terraform apply -auto-approve

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_api_gateway_rest_api.kke_api[0] will be created
  + resource "aws_api_gateway_rest_api" "kke_api" {
      + api_key_source               = (known after apply)
      + arn                          = (known after apply)
      + binary_media_types           = (known after apply)
      + created_date                 = (known after apply)
      + description                  = (known after apply)
      + disable_execute_api_endpoint = (known after apply)
      + execution_arn                = (known after apply)
      + id                           = (known after apply)
      + minimum_compression_size     = (known after apply)
      + name                         = "prod-xfusion-api-1"
      + policy                       = (known after apply)
      + root_resource_id             = (known after apply)
      + tags_all                     = (known after apply)

      + endpoint_configuration (known after apply)
    }

  # aws_api_gateway_rest_api.kke_api[1] will be created
  + resource "aws_api_gateway_rest_api" "kke_api" {
      + api_key_source               = (known after apply)
      + arn                          = (known after apply)
      + binary_media_types           = (known after apply)
      + created_date                 = (known after apply)
      + description                  = (known after apply)
      + disable_execute_api_endpoint = (known after apply)
      + execution_arn                = (known after apply)
      + id                           = (known after apply)
      + minimum_compression_size     = (known after apply)
      + name                         = "prod-xfusion-api-2"
      + policy                       = (known after apply)
      + root_resource_id             = (known after apply)
      + tags_all                     = (known after apply)

      + endpoint_configuration (known after apply)
    }

  # aws_cloudwatch_log_group.kke_logs[0] will be created
  + resource "aws_cloudwatch_log_group" "kke_logs" {
      + arn               = (known after apply)
      + id                = (known after apply)
      + log_group_class   = (known after apply)
      + name              = "/aws/apigateway/prod-xfusion-api-1"
      + name_prefix       = (known after apply)
      + retention_in_days = 0
      + skip_destroy      = false
      + tags_all          = (known after apply)
    }

  # aws_cloudwatch_log_group.kke_logs[1] will be created
  + resource "aws_cloudwatch_log_group" "kke_logs" {
      + arn               = (known after apply)
      + id                = (known after apply)
      + log_group_class   = (known after apply)
      + name              = "/aws/apigateway/prod-xfusion-api-2"
      + name_prefix       = (known after apply)
      + retention_in_days = 0
      + skip_destroy      = false
      + tags_all          = (known after apply)
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_api_gateway_names = [
      + "prod-xfusion-api-1",
      + "prod-xfusion-api-2",
    ]
  + kke_log_group_names   = [
      + "/aws/apigateway/prod-xfusion-api-1",
      + "/aws/apigateway/prod-xfusion-api-2",
    ]
aws_cloudwatch_log_group.kke_logs[1]: Creating...
aws_cloudwatch_log_group.kke_logs[0]: Creating...
aws_api_gateway_rest_api.kke_api[0]: Creating...
aws_api_gateway_rest_api.kke_api[1]: Creating...
aws_cloudwatch_log_group.kke_logs[1]: Provisioning with 'local-exec'...
aws_cloudwatch_log_group.kke_logs[0]: Provisioning with 'local-exec'...
aws_cloudwatch_log_group.kke_logs[1] (local-exec): Executing: ["/bin/sh" "-c" "echo Created Log Group /aws/apigateway/prod-xfusion-api-2 in workspace prod >> /home/bob/terraform/loggroups.log"]
aws_cloudwatch_log_group.kke_logs[0] (local-exec): Executing: ["/bin/sh" "-c" "echo Created Log Group /aws/apigateway/prod-xfusion-api-1 in workspace prod >> /home/bob/terraform/loggroups.log"]
aws_cloudwatch_log_group.kke_logs[0]: Creation complete after 0s [id=/aws/apigateway/prod-xfusion-api-1]
aws_cloudwatch_log_group.kke_logs[1]: Creation complete after 0s [id=/aws/apigateway/prod-xfusion-api-2]
aws_api_gateway_rest_api.kke_api[0]: Provisioning with 'local-exec'...
aws_api_gateway_rest_api.kke_api[0] (local-exec): Executing: ["/bin/sh" "-c" "echo Created API Gateway prod-xfusion-api-1 in workspace prod >> /home/bob/terraform/apigateway.log"]
aws_api_gateway_rest_api.kke_api[0]: Creation complete after 0s [id=daqukfr3qq]
aws_api_gateway_rest_api.kke_api[1]: Provisioning with 'local-exec'...
aws_api_gateway_rest_api.kke_api[1] (local-exec): Executing: ["/bin/sh" "-c" "echo Created API Gateway prod-xfusion-api-2 in workspace prod >> /home/bob/terraform/apigateway.log"]
aws_api_gateway_rest_api.kke_api[1]: Creation complete after 0s [id=y9qivhdapj]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

Outputs:

kke_api_gateway_names = [
  "prod-xfusion-api-1",
  "prod-xfusion-api-2",
]
kke_log_group_names = [
  "/aws/apigateway/prod-xfusion-api-1",
  "/aws/apigateway/prod-xfusion-api-2",
]
```

Step 8 — Verify Log Files

Check:

/home/bob/terraform/apigateway.log
/home/bob/terraform/loggroups.log


Example contents:

Created API Gateway dev-xfusion-api-1 in workspace dev
Created API Gateway dev-xfusion-api-2 in workspace dev
Created API Gateway prod-xfusion-api-1 in workspace prod
Created API Gateway prod-xfusion-api-2 in workspace prod

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
What We Built

We created:

Multiple APIs automatically

Matching log groups automatically

Different environments (dev & prod)

Automatic logging to local files

All with one Terraform configuration.

What Is count Doing?

Instead of writing:

resource A
resource B


We used:

count = length(var.KKE_API_NAMES)


This means:

“Create one resource for each name in the list.”

If list has 2 names → create 2 APIs
If list has 5 names → create 5 APIs

Fully scalable.

What Is a Workspace?

A Terraform workspace is like:

A separate environment

A separate state file

Same code, different deployment

We used:

terraform.workspace


Which becomes:

dev

prod

So resource names automatically change.

How Naming Works

If workspace = dev
And API name = xfusion-api-1

Final name becomes:

dev-xfusion-api-1


In prod:

prod-xfusion-api-1


Same code. Different environment.

What Is local-exec Doing?

After each resource is created, Terraform runs:

echo Created API Gateway ...


This writes confirmation to:

apigateway.log

loggroups.log

It’s basically a post-create hook.

Real DevOps Concept Here

You demonstrated:

Environment isolation

Dynamic resource scaling

Workspace-driven naming

Provisioner hooks

Infrastructure reuse

This is multi-environment infrastructure design.

---