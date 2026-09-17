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



```hcl
resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-demo-bucket-12345"
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
