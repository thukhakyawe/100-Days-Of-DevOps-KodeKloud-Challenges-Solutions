# Step 1: Create Main Terraform Configuration

```
# main.tf

# Provision EC2 instance
resource "aws_instance" "ec2" {
  ami           = "ami-0c101f26f147fa7fd"
  instance_type = "t2.nano"  # Changed from t2.micro to t2.nano
  subnet_id     = ""
  vpc_security_group_ids = [
    "sg-1a0391390d2b1259b"
  ]

  tags = {
    Name = "xfusion-ec2"
  }
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
aws_instance.ec2: Refreshing state... [id=i-afe76a935e3162c2f]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  ~ update in-place

Terraform will perform the following actions:

  # aws_instance.ec2 will be updated in-place
  ~ resource "aws_instance" "ec2" {
        id                                   = "i-afe76a935e3162c2f"
      ~ instance_type                        = "t2.micro" -> "t2.nano"
      ~ public_dns                           = "ec2-54-214-116-174.compute-1.amazonaws.com" -> (known after apply)
      ~ public_ip                            = "54.214.116.174" -> (known after apply)
        tags                                 = {
            "Name" = "xfusion-ec2"
        }
        # (34 unchanged attributes hidden)

        # (2 unchanged blocks hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.

────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run
"terraform apply" now.
```


# Apply the configuration:

```
terraform apply
```

Then type `yes` when prompted to confirm the creation of the snapshot.


Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply
aws_instance.ec2: Refreshing state... [id=i-afe76a935e3162c2f]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  ~ update in-place

Terraform will perform the following actions:

  # aws_instance.ec2 will be updated in-place
  ~ resource "aws_instance" "ec2" {
        id                                   = "i-afe76a935e3162c2f"
      ~ instance_type                        = "t2.micro" -> "t2.nano"
      ~ public_dns                           = "ec2-54-214-116-174.compute-1.amazonaws.com" -> (known after apply)
      ~ public_ip                            = "54.214.116.174" -> (known after apply)
        tags                                 = {
            "Name" = "xfusion-ec2"
        }
        # (34 unchanged attributes hidden)

        # (2 unchanged blocks hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.ec2: Modifying... [id=i-afe76a935e3162c2f]
aws_instance.ec2: Still modifying... [id=i-afe76a935e3162c2f, 10s elapsed]
aws_instance.ec2: Still modifying... [id=i-afe76a935e3162c2f, 20s elapsed]
aws_instance.ec2: Modifications complete after 20s [id=i-afe76a935e3162c2f]

Apply complete! Resources: 0 added, 1 changed, 0 destroyed.
```

***
