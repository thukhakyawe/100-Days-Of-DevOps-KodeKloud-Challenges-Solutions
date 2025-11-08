# Step 1: Check Main Terraform Configuration

```
# main.tf

resource "aws_vpc" "this" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name        = "datacenter-vpc"
  }
}
```



# Check the VPC details

```
aws ec2 describe-vpcs --region us-east-1 --filters "Name=tag:Name,Values=datacenter-vpc" --query "Vpcs[].{VpcId:VpcId,CidrBlock:CidrBlock}"
```

Output

```
bob@iac-server ~/terraform via 💠 default ➜  aws ec2 describe-vpcs --region us-east-1 --filters "Name=tag:Name,Values=datacenter-vpc" --query "Vpcs[].{VpcId:VpcId,CidrBlock:CidrBlock}"
[
    {
        "VpcId": "vpc-616b7d41294668897",
        "CidrBlock": "10.0.0.0/16"
    }
]


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



# Destroy the configuration:

```
terraform destroy -target=aws_vpc.this
```

Then type `yes` when prompted to confirm the creation of the snapshot.


Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform destroy -target=aws_vpc.this
aws_vpc.this: Refreshing state... [id=vpc-616b7d41294668897]

Terraform used the selected providers to generate the following execution plan. Resource
actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # aws_vpc.this will be destroyed
  - resource "aws_vpc" "this" {
      - arn                                  = "arn:aws:ec2:us-east-1:000000000000:vpc/vpc-616b7d41294668897" -> null
      - assign_generated_ipv6_cidr_block     = false -> null
      - cidr_block                           = "10.0.0.0/16" -> null
      - default_network_acl_id               = "acl-bc313f56a3d206091" -> null
      - default_route_table_id               = "rtb-73c3f228ed541141c" -> null
      - default_security_group_id            = "sg-cf76107f1051d50a8" -> null
      - dhcp_options_id                      = "default" -> null
      - enable_dns_hostnames                 = false -> null
      - enable_dns_support                   = true -> null
      - enable_network_address_usage_metrics = false -> null
      - id                                   = "vpc-616b7d41294668897" -> null
      - instance_tenancy                     = "default" -> null
      - ipv6_netmask_length                  = 0 -> null
      - main_route_table_id                  = "rtb-73c3f228ed541141c" -> null
      - owner_id                             = "000000000000" -> null
      - tags                                 = {
          - "Name" = "datacenter-vpc"
        } -> null
      - tags_all                             = {
          - "Name" = "datacenter-vpc"
        } -> null
        # (4 unchanged attributes hidden)
    }

Plan: 0 to add, 0 to change, 1 to destroy.
╷
│ Warning: Resource targeting is in effect
│ 
│ You are creating a plan with the -target option, which means that the result of this
│ plan may not represent all of the changes requested by the current configuration.
│ 
│ The -target option is not for routine use, and is provided only for exceptional
│ situations such as recovering from errors or mistakes, or when Terraform specifically
│ suggests to use it as part of an error message.
╵

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

aws_vpc.this: Destroying... [id=vpc-616b7d41294668897]
aws_vpc.this: Destruction complete after 0s
╷
│ Warning: Applied changes may be incomplete
│ 
│ The plan was created with the -target option in effect, so some changes requested in
│ the configuration may have been ignored and the output values may not be fully updated.
│ Run the following command to verify that no other changes are pending:
│     terraform plan
│ 
│ Note that the -target option is not suitable for routine use, and is provided only for
│ exceptional situations such as recovering from errors or mistakes, or when Terraform
│ specifically suggests to use it as part of an error message.
╵

Destroy complete! Resources: 1 destroyed.
```

***
