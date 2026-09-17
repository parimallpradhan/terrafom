## 4. RDS — `aws_db_instance`

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
resource "aws_db_instance" "mydb" {
  engine         = "mysql"
  instance_class = "db.t3.micro"
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
