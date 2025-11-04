# Step 1: Create Main Terraform Configuration

```
# main.tf

# Create SSM parameter for storing configuration data
resource "aws_ssm_parameter" "devops-ssm-parameter" {
  name  = "devops-ssm-parameter"
  type  = "String"
  value = "devops-value"
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

# Plan the deployment to verify the configuration:

```
terraform plan
```

Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform plan

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_ssm_parameter.devops-ssm-parameter will be created
  + resource "aws_ssm_parameter" "devops-ssm-parameter" {
      + arn            = (known after apply)
      + data_type      = (known after apply)
      + has_value_wo   = (known after apply)
      + id             = (known after apply)
      + insecure_value = (known after apply)
      + key_id         = (known after apply)
      + name           = "devops-ssm-parameter"
      + tags_all       = (known after apply)
      + tier           = (known after apply)
      + type           = "String"
      + value          = (sensitive value)
      + value_wo       = (write-only attribute)
      + version        = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take
exactly these actions if you run "terraform apply" now.
```


# Apply the configuration:

```
terraform apply
```

Then type `yes` when prompted to confirm the creation of the snapshot.


Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_ssm_parameter.devops-ssm-parameter will be created
  + resource "aws_ssm_parameter" "devops-ssm-parameter" {
      + arn            = (known after apply)
      + data_type      = (known after apply)
      + has_value_wo   = (known after apply)
      + id             = (known after apply)
      + insecure_value = (known after apply)
      + key_id         = (known after apply)
      + name           = "devops-ssm-parameter"
      + tags_all       = (known after apply)
      + tier           = (known after apply)
      + type           = "String"
      + value          = (sensitive value)
      + value_wo       = (write-only attribute)
      + version        = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_ssm_parameter.devops-ssm-parameter: Creating...
aws_ssm_parameter.devops-ssm-parameter: Creation complete after 1s [id=devops-ssm-parameter]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

***
