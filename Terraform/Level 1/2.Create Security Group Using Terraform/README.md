```
# main.tf

terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

resource "aws_security_group" "xfusion_sg" {
  name        = "xfusion-sg"
  description = "Security group for Nautilus App Servers"
  vpc_id      = data.aws_vpc.default.id

  ingress {
    description = "HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    description = "SSH"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "xfusion-sg"
  }
}

data "aws_vpc" "default" {
  default = true
}
```

Here's what this configuration does:

Provider Configuration: Sets up the AWS provider for the us-east-1 region as required.

Data Source: Uses aws_vpc.default to automatically find and use the default VPC in the region.

Security Group Resource: Creates the xfusion-sg security group with:

Name: xfusion-sg

Description: Security group for Nautilus App Servers

Two inbound rules:

HTTP on port 80 from anywhere (0.0.0.0/0)

SSH on port 22 from anywhere (0.0.0.0/0)

To deploy this configuration:

Navigate to the Terraform directory:

1. Initialize Terraform:

```
terraform init
```

2. Plan the deployment to verify the configuration:
```
terraform plan
```

3. Apply the configuration:
```
terraform apply
```

Then type yes when prompted to confirm the creation of resources.

The security group will be created in the default VPC in the us-east-1 region with the exact specifications required by the Nautilus DevOps team.