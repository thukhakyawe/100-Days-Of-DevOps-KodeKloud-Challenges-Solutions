# Step 1: Create Main Terraform Configuration

```
# main.tf

# Create OpenSearch domain for log storage and search
resource "aws_opensearch_domain" "xfusion" {
  domain_name = "xfusion-es"
  engine_version = "OpenSearch_2.11"
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

  # aws_opensearch_domain.xfusion will be created
  + resource "aws_opensearch_domain" "xfusion" {
      + access_policies                   = (known after apply)
      + advanced_options                  = (known after apply)
      + arn                               = (known after apply)
      + dashboard_endpoint                = (known after apply)
      + dashboard_endpoint_v2             = (known after apply)
      + domain_endpoint_v2_hosted_zone_id = (known after apply)
      + domain_id                         = (known after apply)
      + domain_name                       = "xfusion-es"
      + endpoint                          = (known after apply)
      + endpoint_v2                       = (known after apply)
      + engine_version                    = "OpenSearch_2.11"
      + id                                = (known after apply)
      + ip_address_type                   = (known after apply)
      + kibana_endpoint                   = (known after apply)
      + tags_all                          = (known after apply)

      + advanced_security_options (known after apply)

      + auto_tune_options (known after apply)

      + cluster_config (known after apply)

      + domain_endpoint_options (known after apply)

      + ebs_options (known after apply)

      + encrypt_at_rest (known after apply)

      + node_to_node_encryption (known after apply)

      + off_peak_window_options (known after apply)

      + software_update_options (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

─────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to
take exactly these actions if you run "terraform apply" now.
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

  # aws_opensearch_domain.xfusion will be created
  + resource "aws_opensearch_domain" "xfusion" {
      + access_policies                   = (known after apply)
      + advanced_options                  = (known after apply)
      + arn                               = (known after apply)
      + dashboard_endpoint                = (known after apply)
      + dashboard_endpoint_v2             = (known after apply)
      + domain_endpoint_v2_hosted_zone_id = (known after apply)
      + domain_id                         = (known after apply)
      + domain_name                       = "xfusion-es"
      + endpoint                          = (known after apply)
      + endpoint_v2                       = (known after apply)
      + engine_version                    = "OpenSearch_2.11"
      + id                                = (known after apply)
      + ip_address_type                   = (known after apply)
      + kibana_endpoint                   = (known after apply)
      + tags_all                          = (known after apply)

      + advanced_security_options (known after apply)

      + auto_tune_options (known after apply)

      + cluster_config (known after apply)

      + domain_endpoint_options (known after apply)

      + ebs_options (known after apply)

      + encrypt_at_rest (known after apply)

      + node_to_node_encryption (known after apply)

      + off_peak_window_options (known after apply)

      + software_update_options (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_opensearch_domain.xfusion: Creating...
aws_opensearch_domain.xfusion: Still creating... [10s elapsed]
aws_opensearch_domain.xfusion: Still creating... [20s elapsed]
aws_opensearch_domain.xfusion: Still creating... [30s elapsed]
aws_opensearch_domain.xfusion: Still creating... [40s elapsed]
aws_opensearch_domain.xfusion: Still creating... [50s elapsed]
aws_opensearch_domain.xfusion: Still creating... [1m0s elapsed]
aws_opensearch_domain.xfusion: Still creating... [1m10s elapsed]
aws_opensearch_domain.xfusion: Still creating... [1m20s elapsed]
aws_opensearch_domain.xfusion: Still creating... [1m30s elapsed]
aws_opensearch_domain.xfusion: Still creating... [1m40s elapsed]
aws_opensearch_domain.xfusion: Still creating... [1m50s elapsed]
aws_opensearch_domain.xfusion: Still creating... [2m0s elapsed]
aws_opensearch_domain.xfusion: Still creating... [2m10s elapsed]
aws_opensearch_domain.xfusion: Still creating... [2m20s elapsed]
aws_opensearch_domain.xfusion: Still creating... [2m30s elapsed]
aws_opensearch_domain.xfusion: Still creating... [2m40s elapsed]
aws_opensearch_domain.xfusion: Still creating... [2m50s elapsed]
aws_opensearch_domain.xfusion: Still creating... [3m0s elapsed]
aws_opensearch_domain.xfusion: Still creating... [3m10s elapsed]
aws_opensearch_domain.xfusion: Still creating... [3m20s elapsed]
aws_opensearch_domain.xfusion: Still creating... [3m30s elapsed]
aws_opensearch_domain.xfusion: Still creating... [3m40s elapsed]
aws_opensearch_domain.xfusion: Still creating... [3m50s elapsed]
aws_opensearch_domain.xfusion: Still creating... [4m0s elapsed]
aws_opensearch_domain.xfusion: Still creating... [4m10s elapsed]
aws_opensearch_domain.xfusion: Still creating... [4m20s elapsed]
aws_opensearch_domain.xfusion: Still creating... [4m30s elapsed]
aws_opensearch_domain.xfusion: Still creating... [4m40s elapsed]
aws_opensearch_domain.xfusion: Still creating... [4m50s elapsed]
aws_opensearch_domain.xfusion: Still creating... [5m0s elapsed]
aws_opensearch_domain.xfusion: Still creating... [5m10s elapsed]
aws_opensearch_domain.xfusion: Still creating... [5m20s elapsed]
aws_opensearch_domain.xfusion: Still creating... [5m30s elapsed]
aws_opensearch_domain.xfusion: Still creating... [5m40s elapsed]
aws_opensearch_domain.xfusion: Still creating... [5m50s elapsed]
aws_opensearch_domain.xfusion: Still creating... [6m0s elapsed]
aws_opensearch_domain.xfusion: Still creating... [6m10s elapsed]
aws_opensearch_domain.xfusion: Still creating... [6m20s elapsed]
aws_opensearch_domain.xfusion: Still creating... [6m30s elapsed]
aws_opensearch_domain.xfusion: Still creating... [6m40s elapsed]
aws_opensearch_domain.xfusion: Still creating... [6m50s elapsed]
aws_opensearch_domain.xfusion: Still creating... [7m0s elapsed]
aws_opensearch_domain.xfusion: Still creating... [7m10s elapsed]
aws_opensearch_domain.xfusion: Still creating... [7m20s elapsed]
aws_opensearch_domain.xfusion: Still creating... [7m30s elapsed]
aws_opensearch_domain.xfusion: Still creating... [7m40s elapsed]
aws_opensearch_domain.xfusion: Still creating... [7m50s elapsed]
aws_opensearch_domain.xfusion: Still creating... [8m0s elapsed]
aws_opensearch_domain.xfusion: Still creating... [8m10s elapsed]
aws_opensearch_domain.xfusion: Still creating... [8m20s elapsed]
aws_opensearch_domain.xfusion: Still creating... [8m30s elapsed]
aws_opensearch_domain.xfusion: Still creating... [8m40s elapsed]
aws_opensearch_domain.xfusion: Still creating... [8m50s elapsed]
aws_opensearch_domain.xfusion: Still creating... [9m0s elapsed]
aws_opensearch_domain.xfusion: Still creating... [9m10s elapsed]
aws_opensearch_domain.xfusion: Still creating... [9m20s elapsed]
aws_opensearch_domain.xfusion: Still creating... [9m30s elapsed]
aws_opensearch_domain.xfusion: Still creating... [9m40s elapsed]
aws_opensearch_domain.xfusion: Still creating... [9m50s elapsed]
aws_opensearch_domain.xfusion: Still creating... [10m0s elapsed]
aws_opensearch_domain.xfusion: Creation complete after 10m0s [id=arn:aws:es:us-east-1:000000000000:domain/xfusion-es]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

***
