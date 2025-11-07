# Step 1: Create Main Terraform Configuration

```
# main.tf

# Provision EC2 instance
resource "aws_instance" "ec2" {
  ami           = "ami-0c101f26f147fa7fd"
  instance_type = "t2.micro"
  subnet_id     = "subnet-57a1e28bab475c2c4"
  vpc_security_group_ids = [
    "sg-fe12c680b499a31a0"
  ]

  tags = {
    Name = "devops-ec2"
  }
}

# Provision Elastic IP
resource "aws_eip" "ec2_eip" {
  tags = {
    Name = "devops-ec2-eip"
  }
}

# Associate Elastic IP with EC2 instance
resource "aws_eip_association" "devops_eip_assoc" {
  instance_id   = aws_instance.ec2.id
  allocation_id = aws_eip.ec2_eip.id
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
aws_eip.ec2_eip: Refreshing state... [id=eipalloc-fa968145f94055c9a]
aws_instance.ec2: Refreshing state... [id=i-a933f86cb54e53e6a]

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_eip_association.devops_eip_assoc will be created
  + resource "aws_eip_association" "devops_eip_assoc" {
      + allocation_id        = "eipalloc-fa968145f94055c9a"
      + id                   = (known after apply)
      + instance_id          = "i-a933f86cb54e53e6a"
      + network_interface_id = (known after apply)
      + private_ip_address   = (known after apply)
      + public_ip            = (known after apply)
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
aws_eip.ec2_eip: Refreshing state... [id=eipalloc-fa968145f94055c9a]
aws_instance.ec2: Refreshing state... [id=i-a933f86cb54e53e6a]

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_eip_association.devops_eip_assoc will be created
  + resource "aws_eip_association" "devops_eip_assoc" {
      + allocation_id        = "eipalloc-fa968145f94055c9a"
      + id                   = (known after apply)
      + instance_id          = "i-a933f86cb54e53e6a"
      + network_interface_id = (known after apply)
      + private_ip_address   = (known after apply)
      + public_ip            = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_eip_association.devops_eip_assoc: Creating...
aws_eip_association.devops_eip_assoc: Creation complete after 0s [id=eipassoc-edfdfa62e89268d0b]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

***
