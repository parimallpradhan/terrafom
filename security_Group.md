## 2. Security Group — `aws_security_group`

```hcl
resource "aws_security_group" "web_sg" {
  name = "web-sg"

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```



```text
resource
   ↓
aws_security_group
   ↓
web_sg
   ↓
name
   ↓
ingress block
   ↓
port / protocol / source
```

The important HCL concept here is the **nested block**:

```hcl
ingress {
   ...
}
```

---

## 3. S3 — `aws_s3_bucket`

```hcl
resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-demo-bucket-12345"
}
```

Explain:

```text
resource      → block
aws_s3_bucket → S3 resource
my_bucket     → local name
bucket        → actual S3 bucket name
```

---

## 4. RDS — `aws_db_instance`

For teaching syntax, keep the first example simple:

```hcl
resource "aws_db_instance" "mydb" {
  allocated_storage = 20
  engine            = "mysql"
  instance_class    = "db.t3.micro"
  username          = "admin"
  password          = "password123"
}
```

Explain:

```text
aws_db_instance → RDS
allocated_storage → storage
engine            → database type
instance_class    → database size
username          → DB user
password          → DB password
```

⚠️ For a real project, **don't hardcode the RDS password** in a `.tf` file. Later teach students Terraform variables/secrets or AWS Secrets Manager.

---



```hcl
resource "aws_security_group" "web_sg" {
  name = "web-sg"
}
```

> **"The syntax is almost the same. Only the AWS resource type and its arguments change."**

### The formula to remember

```text
resource "AWS_RESOURCE_TYPE" "LOCAL_NAME" {
    
    argument = value

}
```

For example:

```text
resource "aws_instance" "web"
          ↑               ↑
       what to create   our name
```

This is the **most important HCL syntax pattern** to make students comfortable with before you move into variables, outputs, and resource dependencies.
