## Terraform Installation on Windows — Step by Step

### 1. Check whether Terraform is already installed

Open **Command Prompt** or **PowerShell**:

```powershell
terraform -version
```

If installed, you'll see something like:

```text
Terraform v1.x.x
```

If you get:

```text
'terraform' is not recognized...
```

then Terraform is either not installed or its location is not in `PATH`.

---

## 2. Download Terraform

Download Terraform for **Windows AMD64** from HashiCorp's official Terraform download page:

[Terraform Downloads — HashiCorp](https://developer.hashicorp.com/terraform/install?utm_source=chatgpt.com)

For most Intel/AMD Windows laptops:

```text
Windows
   ↓
AMD64
```

> **Important:** AMD64 does not mean you need an AMD processor. It is the standard 64-bit x86 build and works on most Intel and AMD Windows PCs.

---

## 3. Extract the ZIP

Suppose you downloaded:

```text
terraform_...._windows_amd64.zip
```

Create a folder:

```text
C:\Terraform
```

Extract the ZIP.

You should have:

```text
C:\Terraform\terraform.exe
```

So your structure looks like:

```text
C:
└── Terraform
    └── terraform.exe
```

---

# 4. Add Terraform to Windows PATH

This is the important step.

Press:

```text
Windows Key
```

Search:

```text
Environment Variables
```

Select:

**Edit the system environment variables**

Then:

**Advanced → Environment Variables**

Under **System variables**, find:

```text
Path
```

Select:

**Edit → New**

Add:

```text
C:\Terraform
```

Then click:

**OK → OK → OK**

---

# 5. Close and reopen CMD

This is important.

If CMD was already open, close it and open a **new Command Prompt**.

Run:

```powershell
terraform -version
```

You should get:

```text
Terraform v1.x.x
on windows_amd64
```

Now Terraform is installed. ✅

---

# 6. Verify Terraform location

Run:

```powershell
where terraform
```

You should see:

```text
C:\Terraform\terraform.exe
```

This is a good command to teach students because it tells them:

> "Windows is finding Terraform from this location."

---

# 7. Test Terraform

Create a practice folder:

```powershell
mkdir terraform-demo
cd terraform-demo
```

Create:

```text
main.tf
```

Put:

```hcl
terraform {
  required_providers {
    local = {
      source = "hashicorp/local"
    }
  }
}

provider "local" {}

resource "local_file" "demo" {
  filename = "hello.txt"
  content  = "Hello Terraform"
}
```

Then run:

```powershell
terraform init
```

Then:

```powershell
terraform validate
```

Then:

```powershell
terraform plan
```

Finally:

```powershell
terraform apply
```

Enter:

```text
yes
```

Terraform should create:

```text
hello.txt
```

---

# 🎓 How I would explain this to freshers

Use this simple flow:

```text
Download Terraform
       ↓
Extract terraform.exe
       ↓
Put it in C:\Terraform
       ↓
Add C:\Terraform to PATH
       ↓
Open NEW CMD
       ↓
terraform -version
       ↓
Terraform ready
```

### One important concept

Tell students:

> **PATH is like a list of locations where Windows looks for commands.**

Without PATH:

```text
terraform
   ↓
Windows: "I don't know where terraform.exe is."
```

With PATH:

```text
terraform
   ↓
Windows checks PATH
   ↓
C:\Terraform
   ↓
terraform.exe found ✅
```
