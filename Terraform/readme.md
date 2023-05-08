# Remote Backend
- Terraform backend stores the terraform.tfstate file in an s3 bucket and uses dynamoDB table for state locking and consistency checking.
- Add the following block to your `provider.tf` file:
```conf
terraform {
  backend "s3" {
    bucket         = "bucket-name"
    key            = "terraform.tfstate"
    region         = "eu-west-1"
    dynamodb_table = "dynamodb-table-name"
  }
}
```
- Create AWS S3 bucket either through the AWS console or by applying the following code(make sure to run either as separate project or to comment the backend block above):
```conf
resource "aws_s3_bucket" "bucket" {
    bucket = "bucket-name"
    versioning {
        enabled = true
    }
    server_side_encryption_configuration {
        rule {
            apply_server_side_encryption_by_default {
                sse_algorithm = "AES256"
            }
        }
    }
    object_lock_configuration {
        object_lock_enabled = "Enabled"
    }
    tags = {
        Name = "S3 Remote Terraform State Store"
    }
}
```
- Create DynamoDB table either through the AWS Console with primary key named `LockID` or with this terraform code:
```conf
resource "aws_dynamodb_table" "terraform-lock" {
    name           = "dynamodb-table-name"
    read_capacity  = 5
    write_capacity = 5
    hash_key       = "LockID"
    attribute {
        name = "LockID"
        type = "S"
    }
    tags = {
        "Name" = "DynamoDB Terraform State Lock Table"
    }
}
```
- When both S3 and DynamoDB table are create we can run terraform init and start working on our infrastructure