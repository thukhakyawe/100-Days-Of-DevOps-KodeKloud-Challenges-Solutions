🧭 Part 1: Step-by-Step Guidelines

Step 1 — Configure the Module Structure



Inside this directory, create:

main.tf
variables.tf
outputs.tf

Step 2 — Define Variables

📄 /home/bob/terraform/modules/s3-static-site/variables.tf

```
variable "bucket_name" {
  description = "Name of the S3 bucket"
  type        = string
}

variable "index_document" {
  description = "Index document for static website"
  type        = string
}
```

These variables must be referenced in main.tf — do not hardcode values.

Step 3 — Create S3 Bucket & Configuration

📄 /home/bob/terraform/modules/s3-static-site/main.tf

```
resource "aws_s3_bucket" "static_site" {
  bucket = var.bucket_name

  tags = {
    Project = "StaticWeb"
  }
}

# Enable static website hosting
resource "aws_s3_bucket_website_configuration" "website" {
  bucket = aws_s3_bucket.static_site.id

  index_document {
    suffix = var.index_document
  }
}

# Disable public access block (required for public website)
resource "aws_s3_bucket_public_access_block" "public_access" {
  bucket = aws_s3_bucket.static_site.id

  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}

# Bucket policy to allow public read access
resource "aws_s3_bucket_policy" "public_policy" {
  bucket = aws_s3_bucket.static_site.id

  depends_on = [
    aws_s3_bucket_public_access_block.public_access
  ]

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid       = "PublicReadGetObject"
        Effect    = "Allow"
        Principal = "*"
        Action    = "s3:GetObject"
        Resource  = "${aws_s3_bucket.static_site.arn}/*"
      }
    ]
  })
}

# Upload index.html using Terraform
resource "aws_s3_object" "index" {
  bucket       = aws_s3_bucket.static_site.id
  key          = var.index_document
  source       = "${path.root}/index.html"
  content_type = "text/html"

  depends_on = [
    aws_s3_bucket_website_configuration.website
  ]
}
```

Step 4 — Define Outputs
📄 /home/bob/terraform/modules/s3-static-site/outputs.tf

```
output "website_url" {
  description = "S3 static website URL"
  value       = "http://aws:4566/${var.bucket_name}/${var.index_document}"
}
```

This matches the required mock AWS / LocalStack endpoint format.

Step 5 — Call Module in Root Configuration
📄 /home/bob/terraform/main.tf

```
module "s3_static_site" {
  source         = "./modules/s3-static-site"
  bucket_name    = "nautilus-web-28947"
  index_document = "index.html"
}

output "website_url" {
  value = module.s3_static_site.website_url
}
```
Step 6 — Ensure index.html Exists

Make sure this file exists:

/home/bob/terraform/index.html


Example content:

Welcome to KKE labs!


Step 7 — Deploy

From:

/home/bob/terraform


Run:

terraform init
terraform validate
terraform plan
terraform apply -auto-approve

Step 8 — Verify

Terraform output should display:

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply -auto-approve

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # module.s3_static_site.aws_s3_bucket.static_site will be created
  + resource "aws_s3_bucket" "static_site" {
      + acceleration_status         = (known after apply)
      + acl                         = (known after apply)
      + arn                         = (known after apply)
      + bucket                      = "nautilus-web-28947"
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
          + "Project" = "StaticWeb"
        }
      + tags_all                    = {
          + "Project" = "StaticWeb"
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

  # module.s3_static_site.aws_s3_bucket_policy.public_policy will be created
  + resource "aws_s3_bucket_policy" "public_policy" {
      + bucket = (known after apply)
      + id     = (known after apply)
      + policy = (known after apply)
    }

  # module.s3_static_site.aws_s3_bucket_public_access_block.public_access will be created
  + resource "aws_s3_bucket_public_access_block" "public_access" {
      + block_public_acls       = false
      + block_public_policy     = false
      + bucket                  = (known after apply)
      + id                      = (known after apply)
      + ignore_public_acls      = false
      + restrict_public_buckets = false
    }

  # module.s3_static_site.aws_s3_bucket_website_configuration.website will be created
  + resource "aws_s3_bucket_website_configuration" "website" {
      + bucket           = (known after apply)
      + id               = (known after apply)
      + routing_rules    = (known after apply)
      + website_domain   = (known after apply)
      + website_endpoint = (known after apply)

      + index_document {
          + suffix = "index.html"
        }

      + routing_rule (known after apply)
    }

  # module.s3_static_site.aws_s3_object.index will be created
  + resource "aws_s3_object" "index" {
      + acl                    = (known after apply)
      + arn                    = (known after apply)
      + bucket                 = (known after apply)
      + bucket_key_enabled     = (known after apply)
      + checksum_crc32         = (known after apply)
      + checksum_crc32c        = (known after apply)
      + checksum_crc64nvme     = (known after apply)
      + checksum_sha1          = (known after apply)
      + checksum_sha256        = (known after apply)
      + content_type           = "text/html"
      + etag                   = (known after apply)
      + force_destroy          = false
      + id                     = (known after apply)
      + key                    = "index.html"
      + kms_key_id             = (known after apply)
      + server_side_encryption = (known after apply)
      + source                 = "./index.html"
      + storage_class          = (known after apply)
      + tags_all               = (known after apply)
      + version_id             = (known after apply)
    }

Plan: 5 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + website_url = "http://aws:4566/nautilus-web-28947/index.html"
module.s3_static_site.aws_s3_bucket.static_site: Creating...
module.s3_static_site.aws_s3_bucket.static_site: Creation complete after 1s [id=nautilus-web-28947]
module.s3_static_site.aws_s3_bucket_public_access_block.public_access: Creating...
module.s3_static_site.aws_s3_bucket_website_configuration.website: Creating...
module.s3_static_site.aws_s3_bucket_public_access_block.public_access: Creation complete after 0s [id=nautilus-web-28947]
module.s3_static_site.aws_s3_bucket_policy.public_policy: Creating...
module.s3_static_site.aws_s3_bucket_website_configuration.website: Creation complete after 0s [id=nautilus-web-28947]
module.s3_static_site.aws_s3_object.index: Creating...
module.s3_static_site.aws_s3_bucket_policy.public_policy: Creation complete after 0s [id=nautilus-web-28947]
module.s3_static_site.aws_s3_object.index: Creation complete after 0s [id=index.html]

Apply complete! Resources: 5 added, 0 changed, 0 destroyed.

Outputs:

website_url = "http://aws:4566/nautilus-web-28947/index.html"
```


You can test:

curl http://aws:4566/nautilus-web-28947/index.html


Expected: HTML content returned.

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What We Just Built

You created:

A storage bucket

Turned it into a mini web server

Allowed the world to read files inside it

Uploaded a web page

Think of S3 Like This

Imagine:

The bucket = a public folder on the internet

index.html = the homepage file

Bucket policy = permission settings on that folder

By default, AWS locks everything down.

We explicitly:

Turned off public access blocking

Attached a rule saying:

“Anyone on the internet can read files in this bucket”

That is what this policy does:

Action: s3:GetObject
Principal: *


Which means:

Anyone (*)

Can read objects (GetObject)

Why We Used Variables

Instead of writing:

bucket = "nautilus-web-28947"


We used:

bucket = var.bucket_name


This makes the module reusable.

Next time, someone can deploy:

bucket_name = "my-company-portal"


Without editing module code.

That is real DevOps practice.

Why We Disabled Public Access Block

AWS protects you by default.

But a public website must:

Allow public read

Not block public policies

So we explicitly turned those protections off for this use case.

Why We Used aws_s3_object

Instead of manually uploading the file with AWS CLI, we let Terraform:

Create the bucket

Configure it

Upload the file

This makes the infrastructure fully reproducible.

Final Architecture

Internet User
→ S3 Static Website Endpoint
→ Reads index.html
→ Returns HTML

No EC2.
No Load Balancer.
No Web Server.

Just pure object storage serving static content.

---