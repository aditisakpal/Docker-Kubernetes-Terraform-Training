#  Terraform AWS Infrastructure: EC2 + VPC

This project provisions a minimal yet complete AWS infrastructure using Terraform.  
You’ll create:

-  A custom VPC
-  A public subnet
-  An internet gateway
-  A route table with route to the internet
-  A security group allowing SSH
-  A public EC2 instance (Amazon Linux 2)

---

##  Prerequisites

Before running the Terraform code, ensure you have the following installed and configured:

###  1. Terraform

Install Terraform (v1.2+ recommended):  
[https://developer.hashicorp.com/terraform/downloads](https://developer.hashicorp.com/terraform/downloads)

To check if it's installed:

```bash
terraform -version
```

---

###  2. AWS CLI

Install the AWS CLI:  
[https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)

Then configure it:

```bash
aws configure
```

You'll be prompted to enter:

```text
AWS Access Key ID [None]: <your-access-key>
AWS Secret Access Key [None]: <your-secret-key>
Default region name [None]: us-east-1
Default output format [None]: json
```

To verify credentials:

```bash
aws sts get-caller-identity
```

This returns your AWS account and user info.

---

###  3. IAM Permissions

Ensure your AWS user/role has these minimum IAM permissions:

- `ec2:*`
- `vpc:*`
- `iam:PassRole` *(optional for advanced EC2 configs)*

---

## 🧾 Terraform Code (main.tf)

Create a file named `main.tf` and paste the following code:

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "CustomVPC"
  }
}

resource "aws_subnet" "public" {
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.1.0/24"
  map_public_ip_on_launch = true
  availability_zone       = "us-east-1a"

  tags = {
    Name = "PublicSubnet"
  }
}

resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "MainIGW"
  }
}

resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }

  tags = {
    Name = "PublicRouteTable"
  }
}

resource "aws_route_table_association" "public_assoc" {
  subnet_id      = aws_subnet.public.id
  route_table_id = aws_route_table.public.id
}

resource "aws_security_group" "ssh_sg" {
  name        = "allow_ssh"
  description = "Allow SSH inbound traffic"
  vpc_id      = aws_vpc.main.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "SSHAccess"
  }
}

resource "aws_instance" "ec2" {
  ami                    = "ami-0c101f26f147fa7fd" # Amazon Linux 2 - us-east-1
  instance_type          = "t2.micro"
  subnet_id              = aws_subnet.public.id
  vpc_security_group_ids = [aws_security_group.ssh_sg.id]
  associate_public_ip_address = true

  tags = {
    Name = "MyEC2Instance"
  }
}
```

---

##  How to Deploy

### 1. Initialize Terraform

```bash
terraform init
```

### 2. Preview the Plan

```bash
terraform plan
```

### 3. Apply the Configuration

```bash
terraform apply
```

When prompted, type `yes` to confirm resource creation.

---

##  To Destroy All Resources

```bash
terraform destroy
```

---

##  Notes

- The EC2 instance is deployed in `us-east-1`.
- Public IP assignment is enabled via subnet and EC2 config.
- SSH access is open to the world for demo purposes — tighten in production.

---
