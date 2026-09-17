## 1. EC2 — `aws_instance`

```hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t2.micro"
}
```

Explain:

```text
resource       → block type
aws_instance   → AWS resource type (EC2)
web            → local name/reference
ami            → AMI to use
instance_type  → EC2 size
```

---



# ⭐ One common pattern for students



```hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t2.micro"
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
