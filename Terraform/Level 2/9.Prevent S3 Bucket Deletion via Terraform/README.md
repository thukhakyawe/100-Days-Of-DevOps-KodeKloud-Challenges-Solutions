1️⃣ variables.tf


```
variable "KKE_BUCKET_NAME" {
  type = string
}
```

2️⃣ terraform.tfvars

```
KKE_BUCKET_NAME = "devops-s3-7734"
```

3️⃣ main.tf

```
resource "aws_s3_bucket" "protected_bucket" {
  bucket = var.KKE_BUCKET_NAME

  lifecycle {
    prevent_destroy = true
  }
}
```



4️⃣ outputs.tf

```
output "s3_bucket_name" {
  value = aws_s3_bucket.protected_bucket.bucket
}
```

5️⃣ Terraform Commands (Run in Order)

terraform init
terraform validate
terraform apply


Type:

yes

✅ Expected Output After Apply
Outputs:

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_s3_bucket.protected_bucket will be created
  + resource "aws_s3_bucket" "protected_bucket" {
      + acceleration_status         = (known after apply)
      + acl                         = (known after apply)
      + arn                         = (known after apply)
      + bucket                      = "devops-s3-7734"
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
      + tags_all                    = (known after apply)
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

Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + s3_bucket_name = "devops-s3-7734"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_s3_bucket.protected_bucket: Creating...
aws_s3_bucket.protected_bucket: Creation complete after 0s [id=devops-s3-7734]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

s3_bucket_name = "devops-s3-7734"
```

---


🧠 Step-by-Step Explanation (Why & What Happens)

Let’s understand this simply, without buzzwords.

🔹 What problem is this lab solving?

In real life:

S3 buckets often store critical data

Someone runs terraform destroy by mistake

💥 Data is gone

This lab teaches you how to block that mistake.

🔹 What does prevent_destroy = true mean?
lifecycle {
  prevent_destroy = true
}


Terraform is being told:

“Even if someone runs terraform destroy,
DO NOT delete this resource.”

It’s like a safety lock 🔒 on the bucket.

🔹 What happens during terraform apply?

1️⃣ Terraform reads terraform.tfvars
2️⃣ Gets bucket name = devops-s3-7734
3️⃣ Creates the S3 bucket
4️⃣ Registers a lifecycle rule in state
5️⃣ Outputs the bucket name

🔹 What happens if someone runs terraform destroy later?

Terraform will refuse:

Error: Instance cannot be destroyed
Resource aws_s3_bucket.protected_bucket has lifecycle.prevent_destroy set


👉 Bucket stays safe
👉 Terraform stops execution

This is exactly what the DevOps team wants.

🔹 Why use variables here?

Because:

Hardcoding is dangerous

Reusability matters

Grader checks variable usage

The lab explicitly wants:

KKE_BUCKET_NAME → terraform.tfvars → main.tf

🧠 Easy Mental Model

S3 bucket = 📦 valuable data

Terraform = 🤖 automation

prevent_destroy = 🔒 safety lock

State file = 📒 rulebook Terraform must obey

🚨 Common Mistakes (You avoided all)

❌ Hardcoding bucket name
❌ Forgetting lifecycle block
❌ Putting lifecycle in wrong resource
❌ Creating extra .tf files
❌ Output name mismatch

---