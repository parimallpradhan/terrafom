## 🎯 Requirement

Imagine a Jira ticket:

> **Create an EC2 web server and allow HTTP traffic on port 80.**

Architecture:

```text
                Internet
                    |
                    | HTTP : 80
                    ↓
          ┌──────────────────┐
          │  Security Group  │
          │                  │
          │  Allow TCP 80    │
          └────────┬─────────┘
                   |
                   ↓
          ┌──────────────────┐
          │      EC2         │
          │   Web Server     │
          │   t2.micro       │
          └──────────────────┘
```

---

# Step 1: Create a Terraform directory

```bash
mkdir ec2-sg
cd ec2-sg
```

Create a file:

```bash
vi main.tf
```

---

# Step 2: Create Security Group

Add this first:

```hcl
resource "aws_security_group" "web_sg" {

  name = "web-sg"

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

### Explain to students

This creates:

```text
aws_security_group
        ↓
      web_sg
```

And:

```text
ingress
   ↓
Incoming traffic
   ↓
Port 80
```

So:

```text
Internet → Port 80 → Security Group → EC2
```

---

# Step 3: Create EC2

Below the Security Group, add:

```hcl
resource "aws_instance" "web" {

  ami           = "ami-xxxxxxxx"
  instance_type = "t2.micro"

  security_groups = [aws_security_group.web_sg.name]

  tags = {
    Name = "Terraform-Web-Server"
  }
}
```

Replace:

```text
ami-xxxxxxxx
```

with a valid AMI ID for your AWS region.

---

# Step 4: Understand the important part

This line is the key:

```hcl
security_groups = [aws_security_group.web_sg.name]
```

Students should understand that we are **connecting two resources**.

```text
aws_security_group.web_sg
        ↓
      name
        ↓
     web-sg
        ↓
attached to EC2
```

So Terraform understands:

```text
EC2
 |
 └── Security Group
       |
       └── web-sg
```

---

# Step 5: Complete `main.tf`

For teaching, you can initially show students the complete file:

```hcl
provider "aws" {
  region = "us-east-1"
}


resource "aws_security_group" "web_sg" {

  name = "web-sg"

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}


resource "aws_instance" "web" {

  ami           = "ami-xxxxxxxx"
  instance_type = "t2.micro"

  security_groups = [aws_security_group.web_sg.name]

  tags = {
    Name = "Terraform-Web-Server"
  }
}
```

---

# Step 6: Initialize Terraform

```bash
terraform init
```

You should see Terraform downloading the AWS provider.

---

# Step 7: Validate

```bash
terraform validate
```

Expected:

```text
Success! The configuration is valid.
```

---

# Step 8: See what Terraform will create

```bash
terraform plan
```

Students should see approximately:

```text
+ aws_security_group.web_sg
+ aws_instance.web
```

Explain:

> `terraform plan` doesn't create anything. It only shows what Terraform plans to create.

---

# Step 9: Create the resources

```bash
terraform apply
```

Terraform asks:

```text
Do you want to perform these actions?
```

Enter:

```text
yes
```

Terraform creates:

```text
Security Group
      ↓
EC2
```

---

# Step 10: Verify in AWS Console

Go to:

**AWS Console → EC2 → Instances**

You should see:

```text
Terraform-Web-Server
```

Then check:

**EC2 → Security Groups**

You should see:

```text
web-sg
```

Inbound rule:

```text
Type       Port
HTTP       80
```

---

## ⭐ Very important teaching point

Ask students:

**"How did Terraform know that this Security Group belongs to this EC2?"**

Answer:

```hcl
security_groups = [aws_security_group.web_sg.name]
```

This is a **resource reference**.

Break it down:

```text
aws_security_group
        ↓
     web_sg
        ↓
      .name
```

Terraform sees the reference and understands the relationship.

---

## One correction for modern AWS/Terraform

For a real project, I recommend teaching students the more explicit `vpc_security_group_ids` argument rather than the older `security_groups` argument:

```hcl
resource "aws_instance" "web" {

  ami           = "ami-xxxxxxxx"
  instance_type = "t2.micro"

  vpc_security_group_ids = [
    aws_security_group.web_sg.id
  ]

  tags = {
    Name = "Terraform-Web-Server"
  }
}
```

So your **preferred modern example** should be:

```text
Security Group
      |
      | ID
      ↓
    EC2
```

with:

```hcl
vpc_security_group_ids = [aws_security_group.web_sg.id]
```

That also gives you a perfect opportunity to teach students the next HCL concept: **resource attribute references (`resource_type.resource_name.attribute`)**.
