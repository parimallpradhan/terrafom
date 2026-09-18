# Terraform Components




<img width="744" height="469" alt="image" src="https://github.com/user-attachments/assets/2a03f6be-c957-43ab-8e0b-a96627992b2f" />


Use Case
 **"We want Terraform to create an EC2 server in AWS. What things do we need?"**

Then build the project step by step.

# 🏗️ Terraform Components — Fresher Hands-On

## 1. Real Use Case



> "Imagine I am a DevOps engineer. My developer team asks me to create an EC2 server in AWS. Instead of creating it manually from the AWS Console, I want Terraform to create it."

Our flow is:

```text
👨‍💻 DevOps Engineer
        │
        ↓
 Terraform CLI
        │
        ↓
  main.tf
        │
        ├── Provider
        │
        └── Resource
              │
              ↓
             AWS
              │
              ↓
             EC2
```

Tell students:

> "Today we will understand what each box means by actually creating an EC2."

---

# 2. Terraform CLI

### Explain first

**Terraform CLI** means the Terraform command-line tool.

For example:

```bash
terraform init
terraform plan
terraform apply
terraform destroy
```

### Hands-on

Ask students:

```bash
terraform version
```

Expected:

```text
Terraform v1.x.x
```

Then:

```bash
terraform -help
```

They will see available Terraform commands.

### Simple explanation

> Terraform CLI is the **tool we use to talk to Terraform**.

Analogy:

```text
👨‍💻 User
   ↓
⌨️ Terraform CLI
   ↓
Terraform
```

---

# 3. Configuration `.tf`

Now ask:

> "Where do we tell Terraform what we want?"

Answer:

> In a `.tf` file.

Create:

```text
terraform-ec2/
└── main.tf
```

At this stage, tell students:

> "Terraform reads `.tf` files and understands what infrastructure we want."

---

# 4. Provider

Now ask students:

> "Terraform knows we want an EC2. But where should it create the EC2?"

Answer:

> AWS.

So we need an **AWS provider**.

Put this into `main.tf`:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}
```

Then run:

```bash
terraform init
```

### Explain what happened

```text
main.tf
   │
   ↓
AWS Provider
   │
   ↓
Connect Terraform with AWS
```

### Very simple definition

> **Provider = translator/connector between Terraform and an external platform.**

Examples:

```text
Terraform
   │
   ├── AWS Provider → AWS
   ├── Azure Provider → Azure
   └── Google Provider → GCP
```

---

# 5. Resource

Now ask:

> "We connected Terraform to AWS. But what do we actually want to create?"

Answer:

> EC2.

Add this to the **same `main.tf`**:

```hcl
resource "aws_instance" "web" {
  ami           = "YOUR-AMI-ID"
  instance_type = "t2.micro"

  tags = {
    Name = "Terraform-Demo"
  }
}
```

Now explain this line:

```hcl
resource "aws_instance" "web"
```

Break it down:

```text
resource
   ↓
Terraform keyword

aws_instance
   ↓
AWS EC2 resource type

web
   ↓
Our Terraform name for this resource
```

So:

```text
resource "aws_instance" "web"
```

means:

> "Terraform, I want you to manage an AWS EC2 instance, and inside Terraform I'll call it `web`."

---

# 6. Now Run `terraform plan`

Run:

```bash
terraform plan
```

Terraform should show:

```text
Plan: 1 to add, 0 to change, 0 to destroy.
```

Now ask students:

**Question:** What are we creating?

Answer:

> EC2.

**Question:** Which component tells Terraform to create EC2?

Answer:

> Resource.

---

# 7. Apply — See the Resource in AWS

Run:

```bash
terraform apply
```

Type:

```text
yes
```

Now EC2 is created.

Show students:

```text
Terraform
   │
   ↓
Provider
   │
   ↓
AWS
   │
   ↓
EC2 Resource
```

This is the first major "aha" moment for beginners.

---

# 8. State File

Now say:

> "Terraform created the EC2. But how will Terraform remember that it created this EC2?"

This introduces the **state file**.

After `terraform apply`, students will see:

```text
terraform.tfstate
```

Their directory is now approximately:

```text
terraform-ec2/
│
├── main.tf
├── terraform.tfstate
├── terraform.tfstate.backup
├── .terraform/
└── .terraform.lock.hcl
```

Don't explain every file yet.

Focus only on:

```text
main.tf
   ↓
Terraform creates EC2
   ↓
terraform.tfstate
   ↓
Terraform remembers the resource
```

### Simple definition

> **State file = Terraform's record of the infrastructure it manages.**

---

# 9. Hands-On With State

Run:

```bash
terraform state list
```

Output:

```text
aws_instance.web
```

Tell students:

> "This tells us Terraform currently manages an `aws_instance.web`."

Now run:

```bash
terraform show
```

Terraform displays details about the EC2.

So:

```text
terraform state list
        ↓
"What resources do I manage?"

terraform show
        ↓
"Show me their details"
```

---

# 10. Outputs

Now students already have an EC2.

Ask:

> "The EC2 has a public IP. How can we ask Terraform to display it?"

Add this to the **same `main.tf`**:

```hcl
output "public_ip" {
  value = aws_instance.web.public_ip
}
```

Run:

```bash
terraform apply
```

Then:

```bash
terraform output
```

Example:

```text
public_ip = "54.xx.xx.xx"
```

### Explain

> **Output = information we want Terraform to display after deployment.**

You can add:

```hcl
output "instance_id" {
  value = aws_instance.web.id
}
```

Then:

```bash
terraform output
```

Example:

```text
instance_id = "i-0123456789"
public_ip   = "54.xx.xx.xx"
```

---

# 11. Variables

Now introduce variables.

Tell students:

> "Our EC2 currently has a fixed instance type."

We have:

```hcl
instance_type = "t2.micro"
```

Ask:

> "What if tomorrow I want `t3.micro`?"

We would have to edit the resource.

Instead, let's make the value configurable.

Add to **`main.tf`**:

```hcl
variable "instance_type" {
  default = "t2.micro"
}
```

Then change:

```hcl
instance_type = "t2.micro"
```

to:

```hcl
instance_type = var.instance_type
```

Now the concept is:

```text
Variable
   ↓
instance_type
   ↓
t2.micro
```

### Simple definition

> **Variable = a value that we can change without modifying the main resource code.**

---

# 12. Demonstrate Variable

Change:

```hcl
variable "instance_type" {
  default = "t2.micro"
}
```

to:

```hcl
variable "instance_type" {
  default = "t3.micro"
}
```

Run:

```bash
terraform plan
```

Terraform will show that the infrastructure needs to change.

This makes the purpose of variables much clearer than explaining the definition alone.

---

# 13. Backend

Now tell students:

> "Currently our state file is stored on this computer."

```text
My Laptop
   │
   └── terraform.tfstate
```

That's the default/local state approach.

Now imagine:

```text
Developer 1
     │
Developer 2
     │
DevOps Engineer
     │
     ↓
Same Terraform Project
```

If everyone's state is on their own laptop, we have a problem.

So we can store Terraform state in a **remote backend**.

For AWS, a common setup uses **S3** for remote state storage.

Conceptually:

```text
Developer
    │
    ↓
Terraform
    │
    ↓
Remote Backend
    │
    ↓
S3
    │
    ↓
terraform.tfstate
```

### Simple definition

> **Backend = where Terraform stores its state.**

### Important beginner distinction

Students often confuse these:

```text
State File
    ↓
What Terraform remembers

Backend
    ↓
Where that state is stored
```

For now, keep the lab using local state. Teach **remote S3 backend as a separate hands-on exercise** after students understand state.

---

# 14. Module

Now tell students:

> "Imagine we create the same EC2 configuration again and again."

For example:

```text
Project 1 → EC2
Project 2 → EC2
Project 3 → EC2
Project 4 → EC2
```

Instead of copying the same Terraform code everywhere, we can create reusable Terraform code called a **module**.

Conceptually:

```text
             Module
               │
       ┌───────┼───────┐
       ↓       ↓       ↓
      DEV      QA     PROD
       │       │       │
      EC2     EC2     EC2
```

### Simple definition

> **Module = reusable Terraform code.**

Don't start with modules in the first EC2 lab. Students should first understand resources, variables, outputs and state.

---

# 15. Terraform Registry

Now ask:

> "Where can we find providers and reusable modules?"

Answer:

> **Terraform Registry.**

Explain:

```text
Terraform Registry
       │
       ├── Providers
       │      ├── AWS
       │      ├── Azure
       │      └── Google
       │
       └── Modules
              ├── VPC
              ├── EC2
              └── EKS
```

Students can search the Registry for providers and modules.

For example, the AWS provider documentation is available through the [Terraform Registry](https://registry.terraform.io/?utm_source=chatgpt.com).

---

# 🎯 Put Everything Together

After teaching all the concepts, show students this:

```text
                    TERRAFORM
                        │
                ┌───────┴───────┐
                │               │
          Terraform CLI     Configuration
                │              main.tf
                │               │
                │        ┌──────┴──────┐
                │        │             │
                │    Provider       Resource
                │        │             │
                │        ↓             ↓
                │       AWS            EC2
                │
                │
         ┌──────┴───────────┐
         │                  │
      Variables          Outputs
         │                  │
     Input values       Useful values
         │                  │
         └────────┬─────────┘
                  ↓
             State File
                  │
                  ↓
               Backend
```

Then introduce the reusable ecosystem:

```text
Terraform Registry
       │
       ├── Providers
       │
       └── Modules
```

---

# 🧑‍🏫 Best Teaching Order for Your Freshers

Since they currently know only `main.tf`, I recommend **not teaching all components in one session**.

### Session 1 — Core

Start with only:

```text
Terraform CLI
      ↓
main.tf
      ↓
Provider
      ↓
Resource
      ↓
terraform plan
      ↓
terraform apply
      ↓
EC2
```

### Session 2 — Terraform remembers

```text
State File
     ↓
terraform state list
     ↓
terraform show
```

### Session 3 — Making code flexible

```text
Variables
    ↓
var.instance_type
```

### Session 4 — Showing information

```text
Outputs
   ↓
public_ip
   ↓
terraform output
```

### Session 5 — Team environment

```text
Backend
   ↓
Remote State
   ↓
S3
```

### Session 6 — Reusable code

```text
Module
   ↓
Reusable Terraform Code
```

### Session 7 — Finding existing providers/modules

```text
Terraform Registry
       ↓
Providers + Modules
```

---

## ⭐ example

Use this story throughout your classes:

> **"I want to create an EC2."**

```text
CLI
 ↓
"Run Terraform"

main.tf
 ↓
"Here is my Terraform code"

Provider
 ↓
"Connect me to AWS"

Resource
 ↓
"Create an EC2"

Variable
 ↓
"Let me change the instance type"

Output
 ↓
"Show me the EC2 public IP"

State
 ↓
"Remember my EC2"

Backend
 ↓
"Store that state here"

Module
 ↓
"Let me reuse this EC2 code"

Registry
 ↓
"Where can I find providers/modules?"
```

This approach will be much easier for a fresher than giving them ten definitions to memorize.
