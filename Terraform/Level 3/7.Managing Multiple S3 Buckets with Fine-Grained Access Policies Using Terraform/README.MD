✅ Part 1: Lab Step-by-Step Guidelines


📄 Step 1: Create variables.tf


```
variable "KKE_ENV_TAGS" {
  description = "Environment specific bucket metadata"
  type = map(object({
    bucket_name = string
    owner       = string
    backup      = bool
  }))
}
```

📄 Step 2: Create terraform.tfvars

```
KKE_ENV_TAGS = {
  Dev = {
    bucket_name = "datacenter-dev-bucket-16801"
    owner       = "Alice"
    backup      = false
  }

  Staging = {
    bucket_name = "datacenter-staging-bucket-16801"
    owner       = "Bob"
    backup      = true
  }

  Prod = {
    bucket_name = "datacenter-prod-bucket-16801"
    owner       = "Carol"
    backup      = true
  }
}
```
📄 Step 3: Create main.tf


```
# Create S3 Buckets using for_each
resource "aws_s3_bucket" "env_buckets" {
  for_each = var.KKE_ENV_TAGS

  bucket = each.value.bucket_name

  tags = merge(
    {
      Environment = each.key
      Owner       = each.value.owner
    },
    each.key == "Prod" ? {} : {
      Name = each.value.bucket_name
    },
    each.value.backup ? { Backup = "true" } : {}
  )

  # Protect tags from accidental drift
  lifecycle {
    ignore_changes = [tags]
  }
}

# Lifecycle Rule for Staging and Prod Only
resource "aws_s3_bucket_lifecycle_configuration" "backup_rule" {
  for_each = {
    for k, v in var.KKE_ENV_TAGS : k => v
    if v.backup == true
  }

  bucket = aws_s3_bucket.env_buckets[each.key].id

  rule {
    id     = "MoveToGlacier"
    status = "Enabled"

    filter {
      prefix = ""   # ✅ Required for provider v5
    }

    transition {
      days          = 30
      storage_class = "GLACIER"
    }
  }
}



# Public Read Bucket Policy
resource "aws_s3_bucket_policy" "public_read" {
  for_each = var.KKE_ENV_TAGS

  bucket = aws_s3_bucket.env_buckets[each.key].id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid       = "PublicReadGetObject"
        Effect    = "Allow"
        Principal = "*"
        Action    = "s3:GetObject"
        Resource  = "${aws_s3_bucket.env_buckets[each.key].arn}/*"
      }
    ]
  })

  depends_on = [aws_s3_bucket.env_buckets]
}
```

📄 Step 4: Create outputs.tf

```
output "kke_bucket_names" {
  value = [
    for bucket in aws_s3_bucket.env_buckets :
    bucket.bucket
  ]
}
```

▶️ Step 5: Apply Terraform
terraform init
terraform validate
terraform apply 

type - yes

✅ Expected Result

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_s3_bucket.env_buckets["Dev"] will be created
  + resource "aws_s3_bucket" "env_buckets" {
      + acceleration_status         = (known after apply)
      + acl                         = (known after apply)
      + arn                         = (known after apply)
      + bucket                      = "datacenter-dev-bucket-16801"
      + bucket_domain_name          = (known after apply)
      + bucket_prefix               = (known after apply)
      + bucket_regional_domain_name = (known after apply)
      + force_destroy               = false
      + hosted_zone_id              = (known after apply)
      + id                          = (known after apply)
      + object_lock_enabled         = (known after apply)
      + policy                      = (known after apply)
      + region                      = (known after apply)
      + request_payer               = (known after apply)
      + tags                        = {
          + "Environment" = "Dev"
          + "Name"        = "datacenter-dev-bucket-16801"
          + "Owner"       = "Alice"
        }
      + tags_all                    = {
          + "Environment" = "Dev"
          + "Name"        = "datacenter-dev-bucket-16801"
          + "Owner"       = "Alice"
        }
      + website_domain              = (known after apply)
      + website_endpoint            = (known after apply)

      + cors_rule (known after apply)

      + grant (known after apply)

      + lifecycle_rule (known after apply)

      + logging (known after apply)

      + object_lock_configuration (known after apply)

      + replication_configuration (known after apply)

      + server_side_encryption_configuration (known after apply)

      + versioning (known after apply)

      + website (known after apply)
    }

  # aws_s3_bucket.env_buckets["Prod"] will be created
  + resource "aws_s3_bucket" "env_buckets" {
      + acceleration_status         = (known after apply)
      + acl                         = (known after apply)
      + arn                         = (known after apply)
      + bucket                      = "datacenter-prod-bucket-16801"
      + bucket_domain_name          = (known after apply)
      + bucket_prefix               = (known after apply)
      + bucket_regional_domain_name = (known after apply)
      + force_destroy               = false
      + hosted_zone_id              = (known after apply)
      + id                          = (known after apply)
      + object_lock_enabled         = (known after apply)
      + policy                      = (known after apply)
      + region                      = (known after apply)
      + request_payer               = (known after apply)
      + tags                        = {
          + "Backup"      = "true"
          + "Environment" = "Prod"
          + "Owner"       = "Carol"
        }
      + tags_all                    = {
          + "Backup"      = "true"
          + "Environment" = "Prod"
          + "Owner"       = "Carol"
        }
      + website_domain              = (known after apply)
      + website_endpoint            = (known after apply)

      + cors_rule (known after apply)

      + grant (known after apply)

      + lifecycle_rule (known after apply)

      + logging (known after apply)

      + object_lock_configuration (known after apply)

      + replication_configuration (known after apply)

      + server_side_encryption_configuration (known after apply)

      + versioning (known after apply)

      + website (known after apply)
    }

  # aws_s3_bucket.env_buckets["Staging"] will be created
  + resource "aws_s3_bucket" "env_buckets" {
      + acceleration_status         = (known after apply)
      + acl                         = (known after apply)
      + arn                         = (known after apply)
      + bucket                      = "datacenter-staging-bucket-16801"
      + bucket_domain_name          = (known after apply)
      + bucket_prefix               = (known after apply)
      + bucket_regional_domain_name = (known after apply)
      + force_destroy               = false
      + hosted_zone_id              = (known after apply)
      + id                          = (known after apply)
      + object_lock_enabled         = (known after apply)
      + policy                      = (known after apply)
      + region                      = (known after apply)
      + request_payer               = (known after apply)
      + tags                        = {
          + "Backup"      = "true"
          + "Environment" = "Staging"
          + "Name"        = "datacenter-staging-bucket-16801"
          + "Owner"       = "Bob"
        }
      + tags_all                    = {
          + "Backup"      = "true"
          + "Environment" = "Staging"
          + "Name"        = "datacenter-staging-bucket-16801"
          + "Owner"       = "Bob"
        }
      + website_domain              = (known after apply)
      + website_endpoint            = (known after apply)

      + cors_rule (known after apply)

      + grant (known after apply)

      + lifecycle_rule (known after apply)

      + logging (known after apply)

      + object_lock_configuration (known after apply)

      + replication_configuration (known after apply)

      + server_side_encryption_configuration (known after apply)

      + versioning (known after apply)

      + website (known after apply)
    }

  # aws_s3_bucket_lifecycle_configuration.backup_rule["Prod"] will be created
  + resource "aws_s3_bucket_lifecycle_configuration" "backup_rule" {
      + bucket                                 = (known after apply)
      + expected_bucket_owner                  = (known after apply)
      + id                                     = (known after apply)
      + transition_default_minimum_object_size = "all_storage_classes_128K"

      + rule {
          + id     = "MoveToGlacier"
          + prefix = (known after apply)
          + status = "Enabled"

          + filter {
              + object_size_greater_than = (known after apply)
              + object_size_less_than    = (known after apply)
                # (1 unchanged attribute hidden)
            }

          + transition {
              + days          = 30
              + storage_class = "GLACIER"
            }
        }
    }

  # aws_s3_bucket_lifecycle_configuration.backup_rule["Staging"] will be created
  + resource "aws_s3_bucket_lifecycle_configuration" "backup_rule" {
      + bucket                                 = (known after apply)
      + expected_bucket_owner                  = (known after apply)
      + id                                     = (known after apply)
      + transition_default_minimum_object_size = "all_storage_classes_128K"

      + rule {
          + id     = "MoveToGlacier"
          + prefix = (known after apply)
          + status = "Enabled"

          + filter {
              + object_size_greater_than = (known after apply)
              + object_size_less_than    = (known after apply)
                # (1 unchanged attribute hidden)
            }

          + transition {
              + days          = 30
              + storage_class = "GLACIER"
            }
        }
    }

  # aws_s3_bucket_policy.public_read["Dev"] will be created
  + resource "aws_s3_bucket_policy" "public_read" {
      + bucket = (known after apply)
      + id     = (known after apply)
      + policy = (known after apply)
    }

  # aws_s3_bucket_policy.public_read["Prod"] will be created
  + resource "aws_s3_bucket_policy" "public_read" {
      + bucket = (known after apply)
      + id     = (known after apply)
      + policy = (known after apply)
    }

  # aws_s3_bucket_policy.public_read["Staging"] will be created
  + resource "aws_s3_bucket_policy" "public_read" {
      + bucket = (known after apply)
      + id     = (known after apply)
      + policy = (known after apply)
    }

Plan: 8 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_bucket_names = [
      + "datacenter-dev-bucket-16801",
      + "datacenter-prod-bucket-16801",
      + "datacenter-staging-bucket-16801",
    ]

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_s3_bucket.env_buckets["Staging"]: Creating...
aws_s3_bucket.env_buckets["Prod"]: Creating...
aws_s3_bucket.env_buckets["Dev"]: Creating...
aws_s3_bucket.env_buckets["Staging"]: Creation complete after 1s [id=datacenter-staging-bucket-16801]
aws_s3_bucket.env_buckets["Dev"]: Creation complete after 1s [id=datacenter-dev-bucket-16801]
aws_s3_bucket.env_buckets["Prod"]: Creation complete after 1s [id=datacenter-prod-bucket-16801]
aws_s3_bucket_policy.public_read["Staging"]: Creating...
aws_s3_bucket_policy.public_read["Prod"]: Creating...
aws_s3_bucket_policy.public_read["Dev"]: Creating...
aws_s3_bucket_policy.public_read["Staging"]: Creation complete after 0s [id=datacenter-staging-bucket-16801]
aws_s3_bucket_policy.public_read["Dev"]: Creation complete after 0s [id=datacenter-dev-bucket-16801]
aws_s3_bucket_policy.public_read["Prod"]: Creation complete after 0s [id=datacenter-prod-bucket-16801]
aws_s3_bucket_lifecycle_configuration.backup_rule["Prod"]: Creating...
aws_s3_bucket_lifecycle_configuration.backup_rule["Staging"]: Creating...
aws_s3_bucket_lifecycle_configuration.backup_rule["Prod"]: Still creating... [10s elapsed]
aws_s3_bucket_lifecycle_configuration.backup_rule["Staging"]: Still creating... [10s elapsed]
aws_s3_bucket_lifecycle_configuration.backup_rule["Prod"]: Still creating... [20s elapsed]
aws_s3_bucket_lifecycle_configuration.backup_rule["Staging"]: Still creating... [20s elapsed]
aws_s3_bucket_lifecycle_configuration.backup_rule["Prod"]: Creation complete after 20s [id=datacenter-prod-bucket-16801]
aws_s3_bucket_lifecycle_configuration.backup_rule["Staging"]: Creation complete after 20s [id=datacenter-staging-bucket-16801]

Apply complete! Resources: 8 added, 0 changed, 0 destroyed.

Outputs:

kke_bucket_names = [
  "datacenter-dev-bucket-16801",
  "datacenter-prod-bucket-16801",
  "datacenter-staging-bucket-16801",
]
```

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🎯 What This Lab Is About

This lab teaches you how to:

Create multiple S3 buckets using one block of code

Configure environment-specific settings

Apply lifecycle rules for backups

Protect tags from drift

Attach public bucket policies

Use modular Terraform design

Instead of repeating code three times, we build everything dynamically.

🪣 Step 1: Creating Three Buckets Using for_each

Instead of writing:

resource "aws_s3_bucket" "dev" { ... }
resource "aws_s3_bucket" "staging" { ... }
resource "aws_s3_bucket" "prod" { ... }


We use:

for_each = var.KKE_ENV_TAGS


Terraform loops through:

Dev

Staging

Prod

And creates:

datacenter-dev-bucket-16801

datacenter-staging-bucket-16801

datacenter-prod-bucket-16801

This makes the infrastructure modular and scalable.

🏷 Step 2: Adding Environment-Specific Tags

Each environment has different owners:

Environment	Owner
Dev	Alice
Staging	Bob
Prod	Carol

We dynamically assign tags using:

tags = {
  Environment = each.key
  Owner       = each.value.owner
}


For Dev and Staging, we also include the Name tag as required.

For Staging and Prod, we also add:

Backup = true


This shows how to use conditional logic inside Terraform.

♻️ Step 3: Adding Lifecycle Rule for Backup

Only Staging and Prod need backups.

We filter environments where:

backup = true


Then we apply a lifecycle rule:

ID: MoveToGlacier

Transition after 30 days

Move to GLACIER storage class

This means:

Objects older than 30 days are automatically archived.

This simulates real production cost optimization.

🔐 Step 4: Protecting Tags with Lifecycle Block

We use:

lifecycle {
  ignore_changes = [tags]
}


Why?

Because:

AWS or LocalStack may modify metadata internally

We don’t want Terraform to keep trying to “fix” tags

It prevents unnecessary drift

This keeps terraform plan clean.

🌍 Step 5: Adding Public Read Policy

We attach a bucket policy allowing:

s3:GetObject


For:

Principal = "*"


This makes all objects publicly readable.

We use depends_on to ensure:

Policy is applied only after bucket creation

This prevents race conditions.

🧩 Step 6: Why This Design Is Important

This lab demonstrates key DevOps principles:

✅ Infrastructure as Code

Everything is declarative and repeatable.

✅ Modular Design

Using a map variable makes environments configurable.

✅ Conditional Logic

Backup rules apply only where needed.

✅ Scalability

To add another environment, just update the variable map.

🔎 What Terraform Actually Does

When you run:

terraform apply


Terraform:

Reads the environment map

Creates 3 S3 buckets

Applies environment-specific tags

Applies lifecycle rules only where needed

Attaches public bucket policies

Outputs the bucket names

🧠 Key Concepts You Practiced

for_each

Map variables

Conditional filtering

Lifecycle rules

Policy JSON generation

Resource dependency management

Tag protection

This is real-world Terraform structure.

---