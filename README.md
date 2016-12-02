# Introduction

This repository gives coding conventions for Terraform's HashiCorp Configuration Language (HCL). Terraform allows infrastructure to be described as code. As such, we should adhere to a style guide to ensure easily readable and high quality code.

# Syntax

Use 2 spaces when defining resources except when defining inline policies or other inline resources.


```
resource "aws_iam_role" "iam_role" {
  name = "${var.resource_name}-role"
  assume_role_policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": "sts:AssumeRole",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Effect": "Allow",
      "Sid": ""
    }
  ]
}
EOF
}
```


When commenting use two "//" and a space in front of the comment.

```
// Create ELK Auto Scaling Group Resource
...
```

# Resource Naming

Only use "\_" (underscore/underbar) when naming Terraform variables, modules, etc
Only use "-" (dash/hyphen/minus sign/etc) when naming the created resources.

```
resource "aws_security_group" "security_group" {
  name = "${var.resource_name}-security-group"
...
```

* A resource's NAME should be the same as the TYPE minus the provider.

```
resource "aws_autoscaling_group" "autoscaling_group" {
...
```

* If there are multiple resources of the same TYPE defined, add a minimalistic identifier to differentiate between the two resources.

```
// Create Data S3 Bucket
resource "aws_s3_bucket" "data_s3_bucket" {
  bucket = "${var.environment_name}-data-${var.aws_region}"
  acl = "private"
  versioning {
    enabled = true
  }
}

// Create Images S3 Bucket
resource "aws_s3_bucket" "images_s3_bucket" {
  bucket = "${var.environment_name}-images-${var.aws_region}"
  acl = "private"
}
```

A blank line should sperate resource definitions contained in the same file.

## Service Modules

Create a separate resource file for each type of AWS resource.

```
ami.tf
autoscaling_group.tf
autoscaling_policy.tf
cloudwatch.tf
iam.tf
launch_configuration.tf
providers.tf
s3.tf
security_groups.tf
sns.tf
sqs.tf
user_data.sh
variables.tf
```

Similar resources should be defined in the same file and named accordingly. See _Resource Naming_ section above.
