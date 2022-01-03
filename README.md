# Terraform Style Guide


<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Introduction](#introduction)
- [Syntax](#syntax)
  - [Spacing](#spacing)
  - [Resource Block Alignment](#resource-block-alignment)
  - [Comments](#comments)
  - [Organizing Variables](#organizing-variables)
- [Naming Conventions](#naming-conventions)
  - [File Names](#file-names)
  - [Parameter, Meta-parameter and Variable Naming](#parameter-meta-parameter-and-variable-naming)
  - [Resource Naming](#resource-naming)
- [Policies as Data Sources](#policies-as-data-sources)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Introduction

This repository gives coding conventions for Terraform's HashiCorp Configuration Language (HCL). Terraform allows infrastructure to be described as code. As such, we should adhere to a style guide to ensure readable and high quality code.

## Syntax

- Strings are in double-quotes.

### Spacing

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

### Resource Block Alignment

Parameter definitions in a resource block should be aligned. The `terraform fmt` command can do this for you.

```
provider "aws" {
  access_key = var.aws_access_key
  secret_key = var.aws_secret_key
  region     = "us-east-1"
}
```


### Comments

When commenting use a hash "#" and a space in front of the comment.

```
# Create ELK IAM Role
...
```

### Organizing Variables

The `variables.tf` file should be broken down into three sections with each section arranged alphabetically. Starting at the top of the file:

1. Variables that have no defaults defined
2. Variables that contain defaults
3. All locals blocks 

For example:

```
variable "image_tag" {}

variable "desired_count" {
  default = "2"
}

locals {
  domain_name = ${data.terraform_remote_state.account.domain_name}
}
```

## Naming Conventions

### File Names

Create a separate resource file for each type of AWS resource. Similar resources should be defined in the same file and named accordingly.

```
ami.tf
autoscaling_group.tf
cloudwatch.tf
data.tf
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

### Parameter, Meta-parameter and Variable Naming

 __Only use an underscore (`_`) when naming Terraform resources like TYPE/NAME parameters and variables.__
 
 ```
resource "aws_security_group" "security_group" {
...
```

### Resource Naming

__Only use a hyphen (`-`) when naming the component being created.__

 ```
resource "aws_security_group" "security_group" {
  name = "${var.resource_name}-security-group"
...
```

__A resource's NAME should be the same as the TYPE minus the provider.__

```
resource "aws_autoscaling_group" "autoscaling_group" {
...
```

If there are multiple resources of the same TYPE defined, add a minimalistic identifier to differentiate between the two resources. A blank line should sperate resource definitions contained in the same file.

```
# Create Data S3 Bucket
resource "aws_s3_bucket" "data_s3_bucket" {
  bucket = "${var.environment_name}-data-${var.aws_region}"
  acl    = "private"
  versioning {
    enabled = true
  }
}

# Create Images S3 Bucket
resource "aws_s3_bucket" "images_s3_bucket" {
  bucket = "${var.environment_name}-images-${var.aws_region}"
  acl    = "private"
}
```

## Policies as Data Sources

All policies (IAM, S3, KMS, SNS, etc.) should be located in `data.tf`.  The following examples create IAM resources in `iam.tf` and policies as data sources in `data.tf`

Snippet from `iam.tf`:
```
# Create Cloudtrail log IAM role for logging
resource "aws_iam_role" "cloudtrail_iam_role" {
  name  = "cloudtrail-role"
  assume_role_policy = data.aws_iam_policy_document.cloudtrail_assume_role_iam_policy_document.json
}

# Attach Cloudtrail log policy to Cloudtrail log IAM role
resource "aws_iam_role_policy_attachment" "cloudtrail_policy_attachement" {
  role       = aws_iam_role.cloudtrail_iam_role.name
  policy_arn = aws_iam_policy.cloudtrail_iam_policy.id
}

# Create Cloudtrail log IAM policy
resource "aws_iam_policy" "cloudtrail_iam_policy" {
  name   = "cloudtrail-iam-iam-policy"
  policy = data.aws_iam_policy_document.cloudtrail_iam_policy_document.json
}
```

Snippet from `data.tf`:
```
# Create Cloudtrail assume role policy
data "aws_iam_policy_document" "cloudtrail_assume_role_iam_policy_document" {
  statement {
    actions = ["sts:AssumeRole"]

    principals {
      type        = "AWS"
      identifiers = ["cloudtrail.amazonaws.com"]
    }
  }
}

# Create Cloudtrail log IAM policy document
data "aws_iam_policy_document" "cloudtrail_log_iam_policy_document" {
  statement {
    sid    = "AllowLogs"
    effect = "Allow"

    actions = [
      "logs:CreateLogStream",
      "logs:PutLogEvents",
    ]

    resources = [
      "${aws_cloudwatch_log_group.cloudtrail_cloudwatch_log_group.arn}*",
    ]
  }
}

```
