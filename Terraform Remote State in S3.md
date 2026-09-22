**Local State → Create S3 Bucket → Configure S3 Backend → `terraform init` → State moves to S3**

HashiCorp's current S3 backend supports storing Terraform state in an S3 object and can also use an S3 lock file with `use_lockfile = true`. HashiCorp also recommends enabling S3 bucket versioning for state recovery. ([HashiCorp Developer][1])

# Terraform Remote State in S3 — Step by Step

## Step 1 — Understand the Current Situation

Normally, when students run:

```bash
terraform apply
```

Terraform creates:

```text
terraform.tfstate
```

locally.

Example:

```text
terraform-project/
│
├── main.tf
├── variables.tf
├── outputs.tf
└── terraform.tfstate    ← Local state
```

We want to change this to:

```text
Terraform
    |
    ↓
AWS S3
    |
    └── terraform.tfstate
```

This is called a **remote backend**. Remote state is useful when multiple people or automation systems need to work with the same Terraform state. ([HashiCorp Developer][2])

---

# Step 2 — Create an S3 Bucket

For a beginner lab, first create the S3 bucket from the **AWS Console**.

Go to:

**AWS Console → S3 → Create bucket**

Example bucket name:

```text
my-company-terraform-state-2026
```

Bucket names must be globally unique, so students should use their own unique name.

Choose your AWS Region, for example:

```text
us-east-1
```

### Important

Enable:

**Bucket Versioning → Enable**

Versioning is recommended because it helps recover the state if the object is accidentally deleted or overwritten. ([HashiCorp Developer][1])

Keep the bucket private.

---

# Step 3 — Create Your Terraform Project

Create a directory:

```bash
mkdir terraform-s3-backend
cd terraform-s3-backend
```

Create:

```text
main.tf
```

For example:

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t2.micro"

  tags = {
    Name = "Web-Server"
  }
}
```

Use an AMI ID that exists in your selected region.

---

# Step 4 — Initialize Terraform

Run:

```bash
terraform init
```

Then:

```bash
terraform plan
```

Then:

```bash
terraform apply
```

After `apply`, you should initially see:

```text
terraform.tfstate
```

locally.

---

# Step 5 — Configure the S3 Backend

Now create a backend configuration inside `main.tf`:

```hcl
terraform {
  backend "s3" {
    bucket       = "my-company-terraform-state-2026"
    key          = "dev/terraform.tfstate"
    region       = "us-east-1"
    use_lockfile = true
  }
}
```

### Explain these lines to students

```hcl
backend "s3"
```

Means:

> Store Terraform state in Amazon S3.

```hcl
bucket = "my-company-terraform-state-2026"
```

Which S3 bucket should contain the state?

```hcl
key = "dev/terraform.tfstate"
```

Where should the state object be stored inside the bucket?

So the logical structure becomes:

```text
S3 Bucket
│
└── dev/
     └── terraform.tfstate
```

```hcl
region = "us-east-1"
```

Which AWS Region contains the bucket?

```hcl
use_lockfile = true
```

Enables S3-based state locking using a `.tflock` object. Current Terraform documentation recommends this approach; the older DynamoDB locking mechanism is now deprecated. ([HashiCorp Developer][1])

---

# Step 6 — Run `terraform init` Again

This is the important step:

```bash
terraform init
```

Terraform will detect that you have configured a new backend.

You may see something similar to:

```text
Initializing the backend...

Successfully configured the backend "s3"!
```

If Terraform asks whether you want to migrate existing local state:

```text
Do you want to copy existing state to the new backend?
```

Answer:

```text
yes
```

Terraform will migrate the existing local state to S3.

---

# Step 7 — Check the S3 Bucket

Go to:

**AWS Console → S3 → Your Bucket**

You should see:

```text
dev/
   terraform.tfstate
```

And when locking is being used, Terraform may create a lock object such as:

```text
dev/
   terraform.tfstate
   terraform.tfstate.tflock
```

The lock object is temporary and is used to prevent conflicting state operations. ([HashiCorp Developer][1])

---

# Step 8 — Check the Local Directory

After configuring a remote backend, students should understand that the state is **no longer normally maintained as the project's local `terraform.tfstate` file**.

They may see:

```text
terraform-project/
│
├── main.tf
├── .terraform/
└── .terraform.lock.hcl
```

The important state is now in:

```text
AWS S3
   ↓
dev/terraform.tfstate
```

Remote backends are specifically designed so Terraform can work with state remotely rather than relying on a shared local state file. ([HashiCorp Developer][3])

---

# Step 9 — Test the Remote State

Run:

```bash
terraform plan
```

Then:

```bash
terraform apply
```

Terraform will read/write the state from the S3 backend.

You can also check the state with:

```bash
terraform state list
```

Example:

```text
aws_instance.web
```

---

# Step 10 — Explain the Complete Flow to Students

I recommend showing this diagram:

```text
                  Developer
                     |
                     ↓
              Terraform Code
                  main.tf
                     |
                     ↓
              terraform init
                     |
                     ↓
             S3 Remote Backend
                     |
          ┌──────────┴──────────┐
          ↓                     ↓
 terraform.tfstate       terraform.tfstate.tflock
          |
          ↓
     AWS Infrastructure
          |
          ↓
       EC2 / S3 / VPC
```

### Simple explanation for students

> **Earlier, Terraform stored the state locally in `terraform.tfstate`. With an S3 backend, Terraform stores the state remotely in an S3 bucket so the state can be shared and managed centrally.**

---

## Important Real-World Point

Don't put AWS access keys directly inside:

```hcl
backend "s3" {
   access_key = "..."
   secret_key = "..."
}
```

For authentication, HashiCorp recommends using environment variables or appropriate AWS identity mechanisms rather than hardcoding credentials in Terraform configuration. ([HashiCorp Developer][1])

For your **student lab**, you can use an AWS CLI profile or environment-based AWS credentials.

---

## One Important Teaching Sequence

Since your students are learning Terraform progressively, I would teach it in this order:

```text
1. Terraform Basics
       ↓
2. Provider
       ↓
3. Resource
       ↓
4. terraform.tfstate
       ↓
5. Remote State
       ↓
6. S3 Backend
       ↓
7. State Locking
       ↓
8. Terraform with Jenkins
```

Then you can give them a practical assignment:

> **Problem Statement:** ABC Technologies has multiple DevOps engineers working on the same AWS infrastructure. The team does not want Terraform state to remain on individual laptops. Configure an S3 remote backend with versioning and state locking, migrate the existing local state to S3, and verify that Terraform can successfully manage the infrastructure using the remote state.

[1]: https://developer.hashicorp.com/terraform/language/backend/s3?utm_source=chatgpt.com "Backend Type: s3 | Terraform | HashiCorp Developer"
[2]: https://developer.hashicorp.com/terraform/language/state/remote?utm_source=chatgpt.com "State: Remote Storage | Terraform | HashiCorp Developer"
[3]: https://developer.hashicorp.com/terraform/language/state/backends?utm_source=chatgpt.com "Backends: State Storage and Locking | Terraform | HashiCorp Developer"
