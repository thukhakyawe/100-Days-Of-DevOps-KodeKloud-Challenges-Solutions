🧭 Part 1: Lab Step-by-Step Guidelines



📁 Final Required Directory Structure
/home/bob/terraform/
│
├── main.tf
├── variables.tf
├── modules/
│   ├── dynamodb/
│   ├── secretsmanager/
│   └── elasticsearch/
│
└── env/
    ├── dev/
    │   ├── main.tf -> /home/bob/terraform/main.tf
    │   ├── variables.tf -> /home/bob/terraform/variables.tf
    │   ├── modules -> /home/bob/terraform/modules
    │   ├── terraform_config.tf   (NOT symlink)
    │   └── dev.tfvars
    │
    └── prod/
        ├── main.tf -> /home/bob/terraform/main.tf
        ├── variables.tf -> /home/bob/terraform/variables.tf
        ├── modules -> /home/bob/terraform/modules
        ├── terraform_config.tf   (NOT symlink)
        └── prod.tfvars

Step 1 — Create Modules
=============================
MODULE 1: DYNAMODB
=============================
📄 modules/dynamodb/variables.tf

```
variable "KKE_DYNAMODB_TABLE_NAME" {
  type = string
}
```

📄 modules/dynamodb/main.tf

```
resource "aws_dynamodb_table" "table" {
  name           = var.KKE_DYNAMODB_TABLE_NAME
  billing_mode   = "PAY_PER_REQUEST"
  hash_key       = "id"

  attribute {
    name = "id"
    type = "S"
  }
}
```

📄 modules/dynamodb/outputs.tf

```
output "kke_table_name" {
  value = aws_dynamodb_table.table.name
}
```

=============================
MODULE 2: SECRETS MANAGER
=============================
📄 modules/secretsmanager/variables.tf

```
variable "KKE_SECRET_NAME" {
  type = string
}

variable "KKE_SECRET_VALUE" {
  type      = string
  sensitive = true
}
```

📄 modules/secretsmanager/main.tf

```
resource "aws_secretsmanager_secret" "secret" {
  name = var.KKE_SECRET_NAME
}

resource "aws_secretsmanager_secret_version" "secret_value" {
  secret_id     = aws_secretsmanager_secret.secret.id
  secret_string = var.KKE_SECRET_VALUE
}
```


📄 modules/secretsmanager/outputs.tf

```
output "kke_secret_arn" {
  value = aws_secretsmanager_secret.secret.arn
}
```

=============================
MODULE 3: ELASTICSEARCH
=============================

(Using OpenSearch-compatible domain)

📄 modules/elasticsearch/variables.tf

```
variable "KKE_ELASTICSEARCH_DOMAIN" {
  type = string
}
```


📄 modules/elasticsearch/main.tf

```
resource "aws_elasticsearch_domain" "es" {
  domain_name           = var.KKE_ELASTICSEARCH_DOMAIN
  elasticsearch_version = "7.10"

  cluster_config {
    instance_type = "t3.small.elasticsearch"
  }

  ebs_options {
    ebs_enabled = true
    volume_size = 10
  }
}
```

📄 modules/elasticsearch/outputs.tf

```
output "kke_elasticsearch_endpoint" {
  value = aws_elasticsearch_domain.es.endpoint
}
```

=============================
ROOT SHARED FILES
=============================
📄 /home/bob/terraform/variables.tf

```
variable "KKE_ENV" {
  type = string
}

variable "KKE_DYNAMODB_TABLE_NAME" {
  type = string
}

variable "KKE_SECRET_NAME" {
  type = string
}

variable "KKE_SECRET_VALUE" {
  type      = string
  sensitive = true
}

variable "KKE_ELASTICSEARCH_DOMAIN" {
  type = string
}
```


📄 /home/bob/terraform/main.tf

```
module "dynamodb" {
  source = "./modules/dynamodb"

  KKE_DYNAMODB_TABLE_NAME = var.KKE_DYNAMODB_TABLE_NAME
}

module "secretsmanager" {
  source = "./modules/secretsmanager"

  KKE_SECRET_NAME  = var.KKE_SECRET_NAME
  KKE_SECRET_VALUE = var.KKE_SECRET_VALUE
}

module "elasticsearch" {
  source = "./modules/elasticsearch"

  KKE_ELASTICSEARCH_DOMAIN = var.KKE_ELASTICSEARCH_DOMAIN
}
```

=============================
ENVIRONMENT CONFIG
=============================
DEV
📄 env/dev/terraform_config.tf

``
locals {
  env = "dev"
}
```

📄 env/dev/dev.tfvars

```
KKE_ENV                   = "dev"
KKE_DYNAMODB_TABLE_NAME   = "devops-dev-table"
KKE_SECRET_NAME           = "devops-dev-secret"
KKE_SECRET_VALUE          = "devops-dev-value"
KKE_ELASTICSEARCH_DOMAIN  = "devops-dev-es"
```


PROD

📄 env/prod/terraform_config.tf

```
locals {
  env = "prod"
}
```


📄 env/prod/prod.tfvars

```
KKE_ENV                   = "prod"
KKE_DYNAMODB_TABLE_NAME   = "devops-prod-table"
KKE_SECRET_NAME           = "devops-prod-secret"
KKE_SECRET_VALUE          = "devops-prod-value"
KKE_ELASTICSEARCH_DOMAIN  = "devops-prod-es"
```

Outputs (Add to root outputs.tf)

📄 /home/bob/terraform/outputs.tf

```
output "kke_table_name" {
  value = module.dynamodb.kke_table_name
}

output "kke_secret_arn" {
  value = module.secretsmanager.kke_secret_arn
}

output "kke_elasticsearch_endpoint" {
  value = module.elasticsearch.kke_elasticsearch_endpoint
}
```

Step 2 — Create Symbolic Links

```
cd /home/bob/terraform/env/dev
ln -s /home/bob/terraform/main.tf main.tf
ln -s /home/bob/terraform/variables.tf variables.tf
ln -s /home/bob/terraform/modules modules
ln -s /home/bob/terraform/outputs.tf outputs.tf
```

Repeat for prod.

```
cd /home/bob/terraform/env/prod
ln -s /home/bob/terraform/main.tf main.tf
ln -s /home/bob/terraform/variables.tf variables.tf
ln -s /home/bob/terraform/modules modules
ln -s /home/bob/terraform/outputs.tf outputs.tf
```

Step 3 — Deploy DEV

```
cd /home/bob/terraform/env/dev
terraform init
terraform validate
terraform apply -var-file=dev.tfvars -auto-approve
```
Step 4 — Deploy PROD
cd /home/bob/terraform/env/prod
terraform init
terraform apply -var-file=prod.tfvars -auto-approve



Step 4 — Deploy DEV

```
cd /home/bob/terraform/env/dev
terraform init
terraform validate
terraform apply -var-file=dev.tfvars -auto-approve
```

Output

```
bob@iac-server terraform/env/dev via 💠 default ➜  terraform apply -var-file=dev.tfvars -auto-approve

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # module.dynamodb.aws_dynamodb_table.table will be created
  + resource "aws_dynamodb_table" "table" {
      + arn              = (known after apply)
      + billing_mode     = "PAY_PER_REQUEST"
      + hash_key         = "id"
      + id               = (known after apply)
      + name             = "devops-dev-table"
      + read_capacity    = (known after apply)
      + region           = "us-east-1"
      + stream_arn       = (known after apply)
      + stream_label     = (known after apply)
      + stream_view_type = (known after apply)
      + tags_all         = (known after apply)
      + write_capacity   = (known after apply)

      + attribute {
          + name = "id"
          + type = "S"
        }

      + global_secondary_index (known after apply)

      + global_table_witness (known after apply)

      + point_in_time_recovery (known after apply)

      + server_side_encryption (known after apply)

      + ttl (known after apply)

      + warm_throughput (known after apply)
    }

  # module.elasticsearch.aws_elasticsearch_domain.es will be created
  + resource "aws_elasticsearch_domain" "es" {
      + access_policies       = (known after apply)
      + advanced_options      = (known after apply)
      + arn                   = (known after apply)
      + domain_id             = (known after apply)
      + domain_name           = "devops-dev-es"
      + elasticsearch_version = "7.10"
      + endpoint              = (known after apply)
      + id                    = (known after apply)
      + kibana_endpoint       = (known after apply)
      + region                = "us-east-1"
      + tags_all              = (known after apply)

      + advanced_security_options (known after apply)

      + auto_tune_options (known after apply)

      + cluster_config {
          + dedicated_master_enabled = false
          + instance_count           = 1
          + instance_type            = "t3.small.elasticsearch"

          + cold_storage_options (known after apply)
        }

      + domain_endpoint_options (known after apply)

      + ebs_options {
          + ebs_enabled = true
          + iops        = (known after apply)
          + throughput  = (known after apply)
          + volume_size = 10
          + volume_type = (known after apply)
        }

      + encrypt_at_rest (known after apply)

      + node_to_node_encryption (known after apply)
    }

  # module.secretsmanager.aws_secretsmanager_secret.secret will be created
  + resource "aws_secretsmanager_secret" "secret" {
      + arn                            = (known after apply)
      + force_overwrite_replica_secret = false
      + id                             = (known after apply)
      + name                           = "devops-dev-secret"
      + name_prefix                    = (known after apply)
      + policy                         = (known after apply)
      + recovery_window_in_days        = 30
      + region                         = "us-east-1"
      + tags_all                       = (known after apply)

      + replica (known after apply)
    }

  # module.secretsmanager.aws_secretsmanager_secret_version.secret_value will be created
  + resource "aws_secretsmanager_secret_version" "secret_value" {
      + arn                  = (known after apply)
      + has_secret_string_wo = (known after apply)
      + id                   = (known after apply)
      + region               = "us-east-1"
      + secret_id            = (known after apply)
      + secret_string        = (sensitive value)
      + secret_string_wo     = (write-only attribute)
      + version_id           = (known after apply)
      + version_stages       = (known after apply)
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_elasticsearch_endpoint = (known after apply)
  + kke_secret_arn             = (known after apply)
  + kke_table_name             = "devops-dev-table"
module.secretsmanager.aws_secretsmanager_secret.secret: Creating...
module.dynamodb.aws_dynamodb_table.table: Creating...
module.elasticsearch.aws_elasticsearch_domain.es: Creating...
module.secretsmanager.aws_secretsmanager_secret.secret: Creation complete after 0s [id=arn:aws:secretsmanager:us-east-1:000000000000:secret:devops-dev-secret-ykbSbg]
module.secretsmanager.aws_secretsmanager_secret_version.secret_value: Creating...
module.secretsmanager.aws_secretsmanager_secret_version.secret_value: Creation complete after 0s [id=arn:aws:secretsmanager:us-east-1:000000000000:secret:devops-dev-secret-ykbSbg|terraform-20260219040537778300000002]
module.dynamodb.aws_dynamodb_table.table: Creation complete after 1s [id=devops-dev-table]
module.elasticsearch.aws_elasticsearch_domain.es: Still creating... [10s elapsed]
module.elasticsearch.aws_elasticsearch_domain.es: Creation complete after 13s [id=arn:aws:es:us-east-1:000000000000:domain/devops-dev-es]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

Outputs:

kke_elasticsearch_endpoint = "devops-dev-es.us-east-1.es.localhost.localstack.cloud:4566"
kke_secret_arn = "arn:aws:secretsmanager:us-east-1:000000000000:secret:devops-dev-secret-ykbSbg"
kke_table_name = "devops-dev-table"
```


Step 5 — Deploy PROD

```
cd /home/bob/terraform/env/prod
terraform init
terraform validate
terraform apply -var-file=prod.tfvars -auto-approve
```

Output

```
bob@iac-server terraform/env/prod via 💠 default ➜  terraform apply -var-file=pr
od.tfvars -auto-approve

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # module.dynamodb.aws_dynamodb_table.table will be created
  + resource "aws_dynamodb_table" "table" {
      + arn              = (known after apply)
      + billing_mode     = "PAY_PER_REQUEST"
      + hash_key         = "id"
      + id               = (known after apply)
      + name             = "devops-prod-table"
      + read_capacity    = (known after apply)
      + region           = "us-east-1"
      + stream_arn       = (known after apply)
      + stream_label     = (known after apply)
      + stream_view_type = (known after apply)
      + tags_all         = (known after apply)
      + write_capacity   = (known after apply)

      + attribute {
          + name = "id"
          + type = "S"
        }

      + global_secondary_index (known after apply)

      + global_table_witness (known after apply)

      + point_in_time_recovery (known after apply)

      + server_side_encryption (known after apply)

      + ttl (known after apply)

      + warm_throughput (known after apply)
    }

  # module.elasticsearch.aws_elasticsearch_domain.es will be created
  + resource "aws_elasticsearch_domain" "es" {
      + access_policies       = (known after apply)
      + advanced_options      = (known after apply)
      + arn                   = (known after apply)
      + domain_id             = (known after apply)
      + domain_name           = "devops-prod-es"
      + elasticsearch_version = "7.10"
      + endpoint              = (known after apply)
      + id                    = (known after apply)
      + kibana_endpoint       = (known after apply)
      + region                = "us-east-1"
      + tags_all              = (known after apply)

      + advanced_security_options (known after apply)

      + auto_tune_options (known after apply)

      + cluster_config {
          + dedicated_master_enabled = false
          + instance_count           = 1
          + instance_type            = "t3.small.elasticsearch"

          + cold_storage_options (known after apply)
        }

      + domain_endpoint_options (known after apply)

      + ebs_options {
          + ebs_enabled = true
          + iops        = (known after apply)
          + throughput  = (known after apply)
          + volume_size = 10
          + volume_type = (known after apply)
        }

      + encrypt_at_rest (known after apply)

      + node_to_node_encryption (known after apply)
    }

  # module.secretsmanager.aws_secretsmanager_secret.secret will be created
  + resource "aws_secretsmanager_secret" "secret" {
      + arn                            = (known after apply)
      + force_overwrite_replica_secret = false
      + id                             = (known after apply)
      + name                           = "devops-prod-secret"
      + name_prefix                    = (known after apply)
      + policy                         = (known after apply)
      + recovery_window_in_days        = 30
      + region                         = "us-east-1"
      + tags_all                       = (known after apply)

      + replica (known after apply)
    }

  # module.secretsmanager.aws_secretsmanager_secret_version.secret_value will be created
  + resource "aws_secretsmanager_secret_version" "secret_value" {
      + arn                  = (known after apply)
      + has_secret_string_wo = (known after apply)
      + id                   = (known after apply)
      + region               = "us-east-1"
      + secret_id            = (known after apply)
      + secret_string        = (sensitive value)
      + secret_string_wo     = (write-only attribute)
      + version_id           = (known after apply)
      + version_stages       = (known after apply)
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_elasticsearch_endpoint = (known after apply)
  + kke_secret_arn             = (known after apply)
  + kke_table_name             = "devops-prod-table"
module.secretsmanager.aws_secretsmanager_secret.secret: Creating...
module.elasticsearch.aws_elasticsearch_domain.es: Creating...
module.dynamodb.aws_dynamodb_table.table: Creating...
module.secretsmanager.aws_secretsmanager_secret.secret: Creation complete after 0s [id=arn:aws:secretsmanager:us-east-1:000000000000:secret:devops-prod-secret-nVTfYF]
module.secretsmanager.aws_secretsmanager_secret_version.secret_value: Creating...
module.secretsmanager.aws_secretsmanager_secret_version.secret_value: Creation complete after 0s [id=arn:aws:secretsmanager:us-east-1:000000000000:secret:devops-prod-secret-nVTfYF|terraform-20260219041148596700000002]
module.dynamodb.aws_dynamodb_table.table: Creation complete after 1s [id=devops-prod-table]
module.elasticsearch.aws_elasticsearch_domain.es: Still creating... [10s elapsed]
module.elasticsearch.aws_elasticsearch_domain.es: Creation complete after 12s [id=arn:aws:es:us-east-1:000000000000:domain/devops-prod-es]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

Outputs:

kke_elasticsearch_endpoint = "devops-prod-es.us-east-1.es.localhost.localstack.cloud:4566"
kke_secret_arn = "arn:aws:secretsmanager:us-east-1:000000000000:secret:devops-prod-secret-nVTfYF"
kke_table_name = "devops-prod-table"
```

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
What This Architecture Achieves

You created:

Shared infrastructure logic

Environment-specific configuration

Modular components

Strict separation of environments

Why Use Symbolic Links?

Instead of copying files:

Both dev and prod use the same Terraform logic.

Only tfvars change.

No duplication.

No configuration drift.

How Environment Isolation Works

When you run Terraform inside:

env/dev


It reads:

dev.tfvars


When inside:

env/prod


It reads:

prod.tfvars


Same modules.
Different values.

What Each Module Does

DynamoDB Module
→ Creates environment-specific table

Secrets Manager Module
→ Creates environment-specific secret
→ Stores environment-specific value

Elasticsearch Module
→ Creates environment-specific search domain

Final Environment Separation

DEV:

devops-dev-table

devops-dev-secret

devops-dev-es

PROD:

devops-prod-table

devops-prod-secret

devops-prod-es