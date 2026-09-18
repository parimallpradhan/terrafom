### 🎯 Goal
Create **one EC2 instance** using only:

```text
terraform-ec2/
└── main.tf
```

Then practice all commands.

---

## 1. Create the project

```bash
mkdir terraform-ec2
cd terraform-ec2
```

Create:

```text
main.tf
```

That's it.

---

# 2. First understand `main.tf`

Put this simple code in `main.tf`:

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

resource "aws_instance" "web" {
  ami           = "YOUR-AMI-ID"
  instance_type = "t2.micro"

  tags = {
    Name = "Terraform-Demo"
  }
}
```

Don't introduce outputs yet.

Tell students:

> "Today we are going to understand Terraform commands using only this `main.tf`."

---

# 3. `terraform version`

Run:

```bash
terraform version
```

Explain:

> Before using Terraform, let's check whether Terraform is installed.

```text
Student
   ↓
terraform version
   ↓
Terraform installed?
   ↓
Which version?
```

---

# 4. `terraform init`

Run:

```bash
terraform init
```

Explain very simply:

> Terraform sees that our `main.tf` needs the AWS provider. So Terraform downloads the required provider.

After running it, students will see new files/folders:

```text
terraform-ec2/
│
├── main.tf
├── .terraform/
└── .terraform.lock.hcl
```

### Important teaching point

Tell them:

> **We created only `main.tf`. Terraform automatically created the other files/folders.**

Don't go deep into `.terraform` yet.

---

# 5. `terraform fmt`

Now purposely change formatting.

For example:

```hcl
resource "aws_instance" "web" {
ami = "YOUR-AMI-ID"
instance_type="t2.micro"
tags={
Name="Terraform-Demo"
}
}
```

Run:

```bash
terraform fmt
```

Open `main.tf`.

Terraform will format it:

```hcl
resource "aws_instance" "web" {
  ami           = "YOUR-AMI-ID"
  instance_type = "t2.micro"

  tags = {
    Name = "Terraform-Demo"
  }
}
```

### Tell students:

> `terraform fmt` only formats our `main.tf`. It does not create anything in AWS.

---

# 6. `terraform validate`

Run:

```bash
terraform validate
```

You should get:

```text
Success! The configuration is valid.
```

Now make a mistake:

```hcl
instance_typ = "t2.micro"
```

Run:

```bash
terraform validate
```

Terraform will show an error.

Fix:

```hcl
instance_type = "t2.micro"
```

Run again:

```bash
terraform validate
```

### Simple explanation

```text
main.tf
   ↓
terraform validate
   ↓
Is my Terraform configuration valid?
```

---

# 7. `terraform plan`

Now run:

```bash
terraform plan
```

Explain:

> Terraform reads our `main.tf` and tells us what it is planning to do.

Students will see something similar to:

```text
Plan: 1 to add, 0 to change, 0 to destroy.
```

And something like:

```text
+ aws_instance.web
```

### Important

Ask students:

**"Has EC2 been created?"**

Answer:

> ❌ No.

`plan` is only a preview.

```text
terraform plan
      ↓
Preview
      ↓
No EC2 created
```

---

# 8. `terraform apply`

Now:

```bash
terraform apply
```

Terraform again shows the plan.

Ask students to type:

```text
yes
```

Terraform creates the EC2.

They will see:

```text
Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

Now go to:

**AWS Console → EC2 → Instances**

Show students:

```text
Terraform-Demo
      ↓
EC2 Instance
      ↓
Running
```

Now they understand the most important concept:

```text
main.tf
   ↓
terraform apply
   ↓
AWS
   ↓
EC2 created
```

---

# 9. `terraform show`

Now introduce:

```bash
terraform show
```

Tell students:

> "Terraform created the EC2. Now let's ask Terraform to show us information about what it created."

They will see lots of information:

```text
resource "aws_instance" "web" {
    ami           = "ami-..."
    instance_type = "t2.micro"
    id            = "i-xxxxxxxx"
    ...
}
```

Don't explain every field.

Just tell them:

> `terraform show` = Show details of infrastructure managed by Terraform.

---

# 10. `terraform state list`

Now:

```bash
terraform state list
```

Output:

```text
aws_instance.web
```

This is a good point to introduce **state** for the first time.

Tell students:

> "Terraform needs to remember what it created. Terraform maintains this information in its state."

Students may see:

```text
terraform.tfstate
```

Their directory now looks approximately like:

```text
terraform-ec2/
│
├── main.tf
├── terraform.tfstate
├── terraform.tfstate.backup
├── .terraform/
└── .terraform.lock.hcl
```

### Simple explanation

```text
main.tf
   ↓
Terraform creates EC2
   ↓
Terraform remembers it
   ↓
terraform.tfstate
```

Then:

```bash
terraform state list
```

shows:

```text
aws_instance.web
```

### Don't teach state manipulation yet.

Avoid commands like:

```bash
terraform state rm
terraform state mv
terraform state pull
```

Students don't need those now.

---

# 11. `terraform output`

Here I would **not teach `terraform output` yet**.

Why?

Because students know only `main.tf`, and we haven't introduced Terraform `output` blocks.

Instead, show them a simple demonstration by adding this to the **same `main.tf`**:

```hcl
output "instance_id" {
  value = aws_instance.web.id
}
```

Now run:

```bash
terraform apply
```

Then:

```bash
terraform output
```

They will see:

```text
instance_id = "i-xxxxxxxxxxxxxxxx"
```

Now explain:

> An output is a value that we want Terraform to show us.

### Important

Tell students:

> We still have only **one file — `main.tf`**.

```text
main.tf
│
├── terraform block
├── provider block
├── resource block
└── output block
```

This is a good beginner progression.

---

# 12. Add Public IP Output

Now add another output to the same `main.tf`:

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

Output:

```text
instance_id = "i-xxxxxxxxxxxxxxxx"
public_ip   = "54.xx.xx.xx"
```

You can also run:

```bash
terraform output public_ip
```

---

# 13. Modify the Infrastructure

Now give students a real exercise.

Current:

```hcl
tags = {
  Name = "Terraform-Demo"
}
```

Change to:

```hcl
tags = {
  Name = "Terraform-Web-Server"
}
```

Then follow the workflow:

```bash
terraform fmt
```

↓

```bash
terraform validate
```

↓

```bash
terraform plan
```

Ask:

> What is Terraform planning to change?

Then:

```bash
terraform apply
```

Now AWS has the new tag.

---

# 14. `terraform show`

Again:

```bash
terraform show
```

Students can find:

```text
Name = "Terraform-Web-Server"
```

This reinforces:

```text
Change main.tf
      ↓
plan
      ↓
apply
      ↓
show
```

---

# 15. `terraform state list`

Again:

```bash
terraform state list
```

Still:

```text
aws_instance.web
```

Explain:

> We changed the tag, but we didn't create a completely new resource. Terraform is still managing `aws_instance.web`.

---

# 16. `terraform destroy`

Finally:

```bash
terraform destroy
```

Terraform asks:

```text
Do you really want to destroy all resources?
```

Type:

```text
yes
```

Students will see:

```text
Destroy complete! Resources: 1 destroyed.
```

Then check AWS Console.

The EC2 is gone.

---

# 🎓 Complete Beginner Flow

I recommend putting this on your presentation slide:

```text
                 main.tf
                    │
                    ↓
            terraform version
                    │
                    ↓
             terraform init
                    │
                    ↓
              terraform fmt
                    │
                    ↓
           terraform validate
                    │
                    ↓
             terraform plan
                    │
                    ↓
            terraform apply
                    │
                    ↓
             AWS EC2 Created
                    │
          ┌─────────┼─────────┐
          ↓         ↓         ↓
     terraform   terraform  terraform
       show      state list   output
          │         │         │
          └─────────┼─────────┘
                    ↓
            Modify main.tf
                    ↓
             terraform plan
                    ↓
             terraform apply
                    ↓
             terraform destroy
                    ↓
              EC2 Deleted
```

# 🧑‍🎓 What I Would Teach Today

Don't overload the students. Use this sequence:

| Step | Command                | Simple meaning              |
| ---- | ---------------------- | --------------------------- |
| 1    | `terraform version`    | Is Terraform installed?     |
| 2    | `terraform init`       | Prepare the project         |
| 3    | `terraform fmt`        | Format my code              |
| 4    | `terraform validate`   | Is my code valid?           |
| 5    | `terraform plan`       | What will Terraform do?     |
| 6    | `terraform apply`      | Create/update it            |
| 7    | `terraform show`       | Show me the details         |
| 8    | `terraform state list` | What does Terraform manage? |
| 9    | `terraform output`     | Show important values       |
| 10   | `terraform destroy`    | Delete it                   |

### The key teaching trick

For freshers, repeatedly connect **command → question**:

```text
version  → "Is Terraform installed?"
init     → "Can Terraform prepare my project?"
fmt      → "Is my code formatted?"
validate → "Is my code valid?"
plan     → "What will happen?"
apply    → "Do it!"
show     → "What did Terraform create?"
state    → "What does Terraform remember?"
output   → "Give me important values"
destroy  → "Remove it!"
```

This way, students understand **why they are running each command**, instead of just memorizing Terraform commands.
