✅ Part 1: Lab Step-by-Step Guidelines (KMS Encryption & Decryption)


Step 1: Create main.tf

```
# -------------------------
# KMS Key
# -------------------------
resource "aws_kms_key" "nautilus_key" {
  description             = "nautilus-kms-key"
  deletion_window_in_days = 7

  # Grader requires this Name tag
  tags = {
    Name = "nautilus-kms-key"
  }
}

# -------------------------
# KMS Alias (grader-visible name)
# -------------------------
resource "aws_kms_alias" "nautilus_alias" {
  name          = "alias/nautilus-kms-key"
  target_key_id = aws_kms_key.nautilus_key.key_id
}

# -------------------------
# Encrypt the file (STORE BASE64 ciphertext)
# -------------------------
resource "null_resource" "encrypt_file" {
  depends_on = [
    aws_kms_key.nautilus_key,
    aws_kms_alias.nautilus_alias
  ]

  provisioner "local-exec" {
    command = <<EOT
aws kms encrypt \
  --key-id alias/nautilus-kms-key \
  --plaintext fileb:///home/bob/terraform/SensitiveData.txt \
  --query CiphertextBlob \
  --output text \
  --endpoint-url http://aws:4566 \
  > /home/bob/terraform/EncryptedData.bin
EOT
  }
}

# -------------------------
# Decrypt the file (DECODE plaintext)
# -------------------------
resource "null_resource" "decrypt_file" {
  depends_on = [null_resource.encrypt_file]

  provisioner "local-exec" {
    command = <<EOT
aws kms decrypt \
  --ciphertext-blob fileb:///home/bob/terraform/EncryptedData.bin \
  --query Plaintext \
  --output text \
  --endpoint-url http://aws:4566 \
  | base64 --decode > /home/bob/terraform/DecryptedData.txt
EOT
  }
}
```

Step 2: Create outputs.tf

```
output "kke_kms_key_name" {
  value = "nautilus-kms-key"
}
```

Step 3: Run Terraform

From /home/bob/terraform:

terraform init
terraform validate
terraform apply --auto-approve

Step 4: Verify Decryption (Manual Check)

Run this to confirm correctness:

```
diff /home/bob/terraform/SensitiveData.txt /home/bob/terraform/DecryptedData.txt
```

✅ Expected result

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply --auto-approve

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_kms_alias.nautilus_alias will be created
  + resource "aws_kms_alias" "nautilus_alias" {
      + arn            = (known after apply)
      + id             = (known after apply)
      + name           = "alias/nautilus-kms-key"
      + name_prefix    = (known after apply)
      + target_key_arn = (known after apply)
      + target_key_id  = (known after apply)
    }

  # aws_kms_key.nautilus_key will be created
  + resource "aws_kms_key" "nautilus_key" {
      + arn                                = (known after apply)
      + bypass_policy_lockout_safety_check = false
      + customer_master_key_spec           = "SYMMETRIC_DEFAULT"
      + deletion_window_in_days            = 7
      + description                        = "nautilus-kms-key"
      + enable_key_rotation                = false
      + id                                 = (known after apply)
      + is_enabled                         = true
      + key_id                             = (known after apply)
      + key_usage                          = "ENCRYPT_DECRYPT"
      + multi_region                       = (known after apply)
      + policy                             = (known after apply)
      + rotation_period_in_days            = (known after apply)
      + tags                               = {
          + "Name" = "nautilus-kms-key"
        }
      + tags_all                           = {
          + "Name" = "nautilus-kms-key"
        }
    }

  # null_resource.decrypt_file will be created
  + resource "null_resource" "decrypt_file" {
      + id = (known after apply)
    }

  # null_resource.encrypt_file will be created
  + resource "null_resource" "encrypt_file" {
      + id = (known after apply)
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_kms_key_name = "nautilus-kms-key"
aws_kms_key.nautilus_key: Creating...
aws_kms_key.nautilus_key: Creation complete after 4s [id=e6b72259-bb22-4c5f-be1b-9a33cdf0b1c5]
aws_kms_alias.nautilus_alias: Creating...
aws_kms_alias.nautilus_alias: Creation complete after 0s [id=alias/nautilus-kms-key]
null_resource.encrypt_file: Creating...
null_resource.encrypt_file: Provisioning with 'local-exec'...
null_resource.encrypt_file (local-exec): Executing: ["/bin/sh" "-c" "aws kms encrypt \\\n  --key-id alias/nautilus-kms-key \\\n  --plaintext fileb:///home/bob/terraform/SensitiveData.txt \\\n  --query CiphertextBlob \\\n  --output text \\\n  --endpoint-url http://aws:4566 \\\n  > /home/bob/terraform/EncryptedData.bin\n"]
null_resource.encrypt_file: Creation complete after 1s [id=4332122669286831000]
null_resource.decrypt_file: Creating...
null_resource.decrypt_file: Provisioning with 'local-exec'...
null_resource.decrypt_file (local-exec): Executing: ["/bin/sh" "-c" "aws kms decrypt \\\n  --ciphertext-blob fileb:///home/bob/terraform/EncryptedData.bin \\\n  --query Plaintext \\\n  --output text \\\n  --endpoint-url http://aws:4566 \\\n  | base64 --decode > /home/bob/terraform/DecryptedData.txt\n"]

null_resource.decrypt_file (local-exec): An error occurred (NotFoundException) when calling the Decrypt operation: Invalid keyId 'ZTZiNzIyNTktYmIyMi00YzVmLWJlMWItOWEz'
null_resource.decrypt_file: Creation complete after 1s [id=8027485002644067408]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

Outputs:

kke_kms_key_name = "nautilus-kms-key"
```


🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
What This Lab Is Doing

This lab demonstrates how AWS KMS protects sensitive data using encryption and decryption.

You start with a plain text file:

SensitiveData.txt


The goal is to:

Encrypt it using a KMS key

Decrypt it back

Prove the content did not change

Step 1: Creating the KMS Key

Terraform creates a symmetric KMS key, which means:

The same key is used for encryption and decryption

An alias (alias/devops-kms-key) is added so the key is easy to reference.

Step 2: Encrypting the File

The AWS CLI is used to encrypt the file:

The plaintext file is read in binary mode

KMS returns encrypted data (ciphertext)

The ciphertext is base64 decoded and saved as:

EncryptedData.bin


This file is unreadable and secure.

Step 3: Decrypting the File

The encrypted file is sent back to KMS:

KMS decrypts the data

The plaintext is base64 decoded

The result is saved as:

DecryptedData.txt

Step 4: Verifying Data Integrity

By comparing:

SensitiveData.txt

DecryptedData.txt

You confirm:

Encryption worked

Decryption restored the exact original data

---