# Terraform Style Guide

**Table of Contents**

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
- [Introduction](#introduction)

- [Introduction](#introduction)
- [Syntax](#syntax)
  - [Spacing](#spacing)
  - [Resource Block Alignment](#resource-block-alignment)
  - [Comments](#comments)
- [Naming Conventions](#naming-conventions)
  - [File Names](#file-names)
  - [Parameter, Meta-parameter and Variable Naming](#parameter-meta-parameter-and-variable-naming)
  - [Resource Naming](#resource-naming)

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
  access_key = "${var.aws_access_key}"
  secret_key = "${var.aws_secret_key}"
  region     = "us-east-1"
}
```


### Comments

When commenting use two "//" and a space in front of the comment.

```
// CREATE ELK IAM ROLE 
...
```

## Naming Conventions

### File Names

Create a separate resource file for each type of AWS resource. Similar resources should be defined in the same file and named accordingly.

```
ami.tf
autoscaling_group.tf
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

__A resource's NAME should be the same as the TYPE minus the provider, with a minimalistic identifier as prefix.__

```
# Resource for Core app
resource "google_service_account" "core_service_account" {
...
}

# Resource for InTo app
resource "google_service_account" "into_service_account" {
...
}
```
