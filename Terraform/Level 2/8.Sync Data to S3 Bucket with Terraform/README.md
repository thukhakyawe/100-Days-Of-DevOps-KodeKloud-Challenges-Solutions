1️⃣ variables.tf

```
variable "KKE_BUCKET" {
  type = string
}
```

2️⃣ terraform.tfvars

```
KKE_BUCKET = "devops-sync-31997"
```

The grader explicitly checks that the bucket name comes from variables.tf.

3️⃣ Update main.tf (append below existing code)


```
# Already existed
resource "aws_s3_bucket" "wordpress_bucket" {
  bucket = "devops-s3-17362"
}

resource "aws_s3_bucket_acl" "wordpress_bucket_acl" {
  bucket = aws_s3_bucket.wordpress_bucket.id
  acl    = "private"
}

# Create the NEW private bucket (from variable)
resource "aws_s3_bucket" "sync_bucket" {
  bucket = var.KKE_BUCKET
}

resource "aws_s3_bucket_acl" "sync_bucket_acl" {
  bucket = aws_s3_bucket.sync_bucket.id
  acl    = "private"
}

# Perform data migration (Terraform-triggered)
resource "null_resource" "s3_sync" {

  provisioner "local-exec" {
    command = "aws s3 sync s3://devops-s3-17362 s3://${var.KKE_BUCKET}"
  }

  depends_on = [
    aws_s3_bucket.sync_bucket
  ]
}
```



4️⃣ outputs.tf

```
output "new_kke_bucket_name" {
  value = aws_s3_bucket.sync_bucket.bucket
}

output "new_kke_bucket_acl" {
  value = aws_s3_bucket_acl.sync_bucket_acl.acl
}
```


Because the lab already had partial state, do this cleanly:

terraform init
terraform validate
terraform apply


Type:

yes

![alt text](image.png)

---

🧠 Simple Explanation 

Let’s connect this to the lab requirements line by line:

✅ “Create a New Private S3 Bucket using variables.tf”

✔ Bucket name comes from var.KKE_BUCKET
✔ Value is set in terraform.tfvars
✔ ACL is private

✅ “Migrate all data from existing bucket”

✔ aws s3 sync copies all objects
✔ Folder structure preserved
✔ No missing files

✅ “Ensure data consistency”

✔ sync ensures source and destination match
✔ Can be re-run safely
✔ No duplication

✅ “Use Terraform”

✔ Terraform creates infrastructure
✔ Terraform triggers migration
✔ Terraform controls execution order

✅ “Do not create separate .tf file for resources”

✔ Everything added to main.tf
✔ Only variables.tf and outputs.tf are separate (as required)

---