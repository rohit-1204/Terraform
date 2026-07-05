# Terraform Notes
# Chapter 20 - Terraform with AWS IAM (Part 1)

> 📘 **Level:** Intermediate
> ⏱️ **Estimated Reading Time:** 80–90 minutes
> 🛠️ **Practice Time:** 4–5 hours

---

# 📚 Table of Contents

1. Introduction
2. What is AWS IAM?
3. Why Use Terraform with IAM?
4. IAM Components
5. IAM Policy Types
6. Terraform IAM Resources
7. Creating IAM Users
8. Creating Multiple IAM Users
9. Creating IAM Groups
10. Adding Users to Groups
11. Creating IAM Policies
12. Using AWS Managed Policies
13. Creating IAM Roles
14. Trust Policies
15. IAM Instance Profiles
16. Real-World Example (Part 2)
17. Best Practices (Part 2)
18. Interview Questions (Part 2)

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand AWS IAM fundamentals
- Create IAM Users
- Create IAM Groups
- Create IAM Policies
- Attach policies to users and groups
- Create IAM Roles
- Configure EC2 Instance Profiles
- Understand Trust Policies

---

# 📖 Introduction

AWS **Identity and Access Management (IAM)** is the AWS service responsible for authentication and authorization.

It answers three important questions:

- **Who** can access AWS?
- **What** actions can they perform?
- **Which** AWS resources can they access?

Terraform allows you to manage IAM as **Infrastructure as Code (IaC)**.

Instead of manually creating users and permissions in the AWS Console, you define them inside Terraform configuration files.

---

# 💡 What is AWS IAM?

IAM controls secure access to AWS resources.

Example:

```text
Developer

      │

      ▼

IAM User

      │

      ▼

IAM Policy

      │

      ▼

Amazon S3
Amazon EC2
Amazon RDS
Amazon DynamoDB
```

IAM does **NOT** create resources.

It only controls **permissions**.

---

# 🎯 Why Use Terraform with IAM?

Benefits include:

- Infrastructure as Code
- Version Control
- Automated Deployments
- Easy Rollback
- Repeatable Infrastructure
- Security Compliance
- Team Collaboration

Without Terraform:

```text
AWS Console

↓

Manual Configuration

↓

Human Errors
```

With Terraform:

```text
Terraform Code

↓

Version Control

↓

Repeatable Deployment
```

---

# 🏗️ IAM Components

AWS IAM consists of several components.

| Component | Description |
|------------|-------------|
| IAM User | Individual AWS identity |
| IAM Group | Collection of users |
| IAM Policy | Permission document |
| IAM Role | Temporary AWS identity |
| Instance Profile | Attach IAM Role to EC2 |
| Access Key | Programmatic authentication |

---

# 👤 IAM Users

An IAM User represents:

- Developer
- Administrator
- DevOps Engineer
- CI/CD Pipeline
- Automation Script

Example:

```text
John

↓

IAM User

↓

AWS Permissions
```

---

# 👥 IAM Groups

Groups simplify permission management.

Example:

```text
Developers Group

├── Alice

├── Bob

└── Charlie
```

Instead of assigning permissions individually, assign them to the group.

---

# 📜 IAM Policies

Policies define permissions.

Policies are written in **JSON**.

Example:

```json
{
  "Version":"2012-10-17",

  "Statement":[

    {

      "Effect":"Allow",

      "Action":"s3:*",

      "Resource":"*"

    }

  ]

}
```

---

# 📚 Types of IAM Policies

There are two main policy types.

## 1. AWS Managed Policies

Created and maintained by AWS.

Examples:

- AmazonS3ReadOnlyAccess
- AmazonEC2ReadOnlyAccess
- AmazonEC2FullAccess
- AmazonRDSFullAccess
- AmazonSSMManagedInstanceCore

Advantages:

- Managed by AWS
- Automatically updated
- Easy to use

---

## 2. Customer Managed Policies

Created by your organization.

Advantages:

- Custom permissions
- Fine-grained control
- Reusable across multiple users and roles

---

# ⚙️ Terraform IAM Resources

Terraform provides several IAM resources.

| Resource | Purpose |
|-----------|----------|
| aws_iam_user | Create users |
| aws_iam_group | Create groups |
| aws_iam_policy | Create custom policies |
| aws_iam_role | Create IAM roles |
| aws_iam_instance_profile | Create instance profiles |
| aws_iam_user_policy_attachment | Attach policy to user |
| aws_iam_group_policy_attachment | Attach policy to group |
| aws_iam_role_policy_attachment | Attach policy to role |

---

# 👤 Creating an IAM User

Example:

```hcl
resource "aws_iam_user" "developer" {

  name = "developer"

}
```

Deploy:

```bash
terraform apply
```

Terraform creates:

```text
IAM User

↓

developer
```

---

# 🏷️ Adding Tags

```hcl
resource "aws_iam_user" "developer" {

  name = "developer"

  tags = {

    Team = "DevOps"

    Environment = "Development"

  }

}
```

---

# 👥 Creating Multiple Users

Terraform supports loops.

```hcl
resource "aws_iam_user" "users" {

  for_each = toset([

    "alice",

    "bob",

    "charlie"

  ])

  name = each.value

}
```

Terraform creates:

```text
alice

bob

charlie
```

Much easier than creating three separate resources.

---

# 👨‍👩‍👧 Creating an IAM Group

Example:

```hcl
resource "aws_iam_group" "developers" {

  name = "Developers"

}
```

Terraform creates:

```text
IAM Group

↓

Developers
```

---

# ➕ Adding Users to a Group

```hcl
resource "aws_iam_user_group_membership" "developers" {

  user = aws_iam_user.developer.name

  groups = [

    aws_iam_group.developers.name

  ]

}
```

Result:

```text
Developer

↓

Developers Group
```

---

# 📜 Creating a Custom IAM Policy

Terraform provides:

```hcl
resource "aws_iam_policy"
```

Example:

```hcl
resource "aws_iam_policy" "s3_readonly" {

  name = "S3ReadOnly"

  policy = jsonencode({

    Version = "2012-10-17"

    Statement = [

      {

        Effect = "Allow"

        Action = [

          "s3:GetObject",

          "s3:ListBucket"

        ]

        Resource = "*"

      }

    ]

  })

}
```

Terraform automatically converts the HCL into valid JSON.

---

# 🔗 Attaching a Policy to a User

```hcl
resource "aws_iam_user_policy_attachment" "attach" {

  user = aws_iam_user.developer.name

  policy_arn = aws_iam_policy.s3_readonly.arn

}
```

Workflow:

```text
IAM User

↓

Custom Policy

↓

S3 Read Access
```

---

# 👥 Attaching a Policy to a Group

Instead of attaching policies to every user:

```hcl
resource "aws_iam_group_policy_attachment" "developers" {

  group = aws_iam_group.developers.name

  policy_arn = aws_iam_policy.s3_readonly.arn

}
```

Now every member of the group automatically receives the permission.

---

# ☁️ Using AWS Managed Policies

AWS already provides hundreds of managed policies.

Example:

```hcl
resource "aws_iam_role_policy_attachment" "ssm" {

  role = aws_iam_role.ec2_role.name

  policy_arn = "arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore"

}
```

Some commonly used managed policies:

| Policy | Purpose |
|----------|----------|
| AmazonS3ReadOnlyAccess | Read-only S3 |
| AmazonEC2ReadOnlyAccess | Read-only EC2 |
| AmazonEC2FullAccess | Full EC2 Access |
| AmazonRDSReadOnlyAccess | Read-only RDS |
| AmazonSSMManagedInstanceCore | AWS Systems Manager |
| CloudWatchAgentServerPolicy | CloudWatch Agent |
| AmazonEKSClusterPolicy | EKS Control Plane |
| AmazonEKSWorkerNodePolicy | EKS Worker Nodes |

---

# 🎭 IAM Roles

Unlike IAM Users, IAM Roles do **not** have permanent credentials.

Roles are assumed temporarily by:

- EC2
- Lambda
- ECS
- EKS
- CloudFormation
- Other AWS services

Example:

```text
EC2 Instance

↓

Assume IAM Role

↓

Temporary Credentials

↓

Access Amazon S3
```

---

# 🏗️ Creating an IAM Role

```hcl
resource "aws_iam_role" "ec2_role" {

  name = "EC2Role"

  assume_role_policy = jsonencode({

    Version = "2012-10-17"

    Statement = [

      {

        Effect = "Allow"

        Principal = {

          Service = "ec2.amazonaws.com"

        }

        Action = "sts:AssumeRole"

      }

    ]

  })

}
```

The `assume_role_policy` defines **who is allowed to use the role**.

---

# 🤝 Understanding Trust Policies

Every IAM Role requires a **Trust Policy**.

Example:

```text
EC2 Service

↓

Allowed?

↓

Yes

↓

Assume Role
```

Without a trust policy:

```text
Access Denied
```

---

# 🖥️ IAM Instance Profiles

EC2 instances cannot directly attach an IAM Role.

Instead, AWS uses an **Instance Profile**.

Example:

```hcl
resource "aws_iam_instance_profile" "profile" {

  name = "EC2Profile"

  role = aws_iam_role.ec2_role.name

}
```

Attach it to EC2:

```hcl
resource "aws_instance" "web" {

  ami = "ami-xxxxxxxx"

  instance_type = "t3.micro"

  iam_instance_profile = aws_iam_instance_profile.profile.name

}
```

Workflow:

```text
IAM Role

↓

Instance Profile

↓

EC2 Instance

↓

AWS Services
```

---

# 📚 End of Part 1

In **Part 2**, you'll learn:

- 🌍 Complete Real-World IAM Project
- 🏗️ IAM Architecture Diagram
- 🔐 Least Privilege Principle
- 🚀 IAM Best Practices
- ⚠️ Common Mistakes
- 📝 Key Takeaways
- ❓ 15 Interview Questions
- 🎯 Practice Exercises
- 🧩 Mini Project
- 📚 Further Reading
- 📖 Navigation to Chapter 21 (AWS VPC)

# Terraform Notes
# Chapter 20 - Terraform with AWS IAM (Part 2)

> 📘 Continuation of Chapter 20

---

# 🌍 Real-World AWS Example

Let's create a production-ready IAM setup for an EC2 instance.

Architecture:

```text
                    Terraform

                        │

                        ▼

                IAM Role (EC2Role)

                        │

                        ▼

         AmazonSSMManagedInstanceCore Policy

                        │

                        ▼

              IAM Instance Profile

                        │

                        ▼

                 Amazon EC2 Instance

                        │

                        ▼

        Secure Access to AWS Services
```

Terraform configuration:

```hcl
resource "aws_iam_role" "ec2_role" {

  name = "EC2Role"

  assume_role_policy = jsonencode({

    Version = "2012-10-17"

    Statement = [

      {

        Effect = "Allow"

        Principal = {

          Service = "ec2.amazonaws.com"

        }

        Action = "sts:AssumeRole"

      }

    ]

  })

}

resource "aws_iam_role_policy_attachment" "ssm" {

  role = aws_iam_role.ec2_role.name

  policy_arn = "arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore"

}

resource "aws_iam_instance_profile" "profile" {

  name = "EC2Profile"

  role = aws_iam_role.ec2_role.name

}

resource "aws_instance" "web" {

  ami                  = "ami-xxxxxxxx"

  instance_type        = "t3.micro"

  iam_instance_profile = aws_iam_instance_profile.profile.name

}
```

This configuration allows the EC2 instance to securely communicate with AWS Systems Manager (SSM) **without storing access keys**.

---

# 🔐 IAM Best Practices

## Principle of Least Privilege

Always grant only the permissions that are required.

❌ Bad:

```text
AdministratorAccess
```

✅ Good:

```text
Allow:

s3:GetObject

s3:ListBucket
```

Only grant permissions needed for the application.

---

## Never Use the Root User

Root user should only be used for:

- Initial AWS account setup
- Billing
- Account recovery

Daily work should use:

- IAM Users
- IAM Roles
- AWS IAM Identity Center (AWS SSO)

---

## Use IAM Roles Instead of Access Keys

❌ Avoid:

```text
AWS_ACCESS_KEY_ID

AWS_SECRET_ACCESS_KEY
```

✅ Prefer:

```text
EC2

↓

IAM Role

↓

Temporary Credentials
```

This is more secure because credentials are automatically rotated.

---

## Enable MFA

Enable Multi-Factor Authentication for:

- Administrators
- Production users
- Billing users

Benefits:

- Additional security
- Protection against stolen passwords

---

## Rotate Credentials Regularly

Rotate:

- Access Keys
- Passwords
- Secrets

Avoid long-lived credentials.

---

## Use AWS Managed Policies When Appropriate

AWS Managed Policies are:

- Maintained by AWS
- Automatically updated
- Easy to attach

Use Customer Managed Policies only when custom permissions are required.

---

## Use Groups for Permission Management

Instead of:

```text
User A

↓

Policy

User B

↓

Policy

User C

↓

Policy
```

Use:

```text
Developers Group

↓

Policy

↓

Users
```

Much easier to manage.

---

# 🏗️ Production IAM Architecture

```text
                  AWS Account

                       │

        ┌──────────────┼──────────────┐

        ▼              ▼              ▼

   IAM Users      IAM Roles      IAM Groups

        │              │              │

        ▼              ▼              ▼

  Developers       EC2 Role      Developers

                        │

                        ▼

                Instance Profile

                        │

                        ▼

                   EC2 Instance

                        │

                        ▼

                AWS Resources
```

---

# ⚠️ Common Mistakes

## Hardcoding AWS Access Keys

❌

```hcl
access_key = "AKIAxxxxxxxx"

secret_key = "xxxxxxxx"
```

✅

Use:

- AWS CLI credentials
- IAM Roles
- Environment Variables

---

## Giving AdministratorAccess to Everyone

Never do this.

Instead:

```text
Least Privilege
```

---

## Sharing IAM Users

Each person should have:

```text
One User

↓

One Identity
```

Never share credentials.

---

## Using Inline Policies Everywhere

Prefer:

```text
Managed Policies
```

Advantages:

- Reusable
- Easier updates
- Better maintenance

---

## Not Tagging IAM Resources

Good example:

```hcl
tags = {

  Environment = "Production"

  Team = "DevOps"

}
```

---

# 💻 Useful Commands

Initialize Terraform:

```bash
terraform init
```

Validate:

```bash
terraform validate
```

Preview changes:

```bash
terraform plan
```

Deploy:

```bash
terraform apply
```

Destroy:

```bash
terraform destroy
```

List IAM Users:

```bash
aws iam list-users
```

List IAM Roles:

```bash
aws iam list-roles
```

List IAM Policies:

```bash
aws iam list-policies
```

Get current AWS identity:

```bash
aws sts get-caller-identity
```

---

# 📝 Key Takeaways

- IAM controls access to AWS resources.
- Terraform can manage all IAM resources.
- IAM Users are for people.
- IAM Groups simplify permission management.
- IAM Policies define permissions.
- IAM Roles provide temporary credentials.
- Instance Profiles attach roles to EC2.
- Follow the Principle of Least Privilege.
- Avoid storing AWS Access Keys.
- Prefer IAM Roles over long-lived credentials.

---

# 📋 Summary

In this chapter, you learned:

- AWS IAM fundamentals
- IAM Users
- IAM Groups
- IAM Policies
- AWS Managed Policies
- Customer Managed Policies
- IAM Roles
- Trust Policies
- Instance Profiles
- IAM Security Best Practices

---

# ❓ Interview Questions

## Beginner

1. What is AWS IAM?
2. What is an IAM User?
3. What is an IAM Group?
4. What is an IAM Policy?
5. What is an IAM Role?

---

## Intermediate

6. What is the difference between an IAM User and an IAM Role?
7. What is an Instance Profile?
8. What is a Trust Policy?
9. Why should EC2 use IAM Roles instead of Access Keys?
10. What is the Principle of Least Privilege?

---

## Advanced

11. Explain the difference between AWS Managed Policies and Customer Managed Policies.
12. How do IAM Roles provide temporary credentials?
13. Why should you avoid using the root account?
14. How would you securely grant an EC2 instance access to Amazon S3?
15. How would you design IAM permissions for a production environment?

---

# 🎯 Practice Exercises

## Exercise 1

Create an IAM User.

```hcl
resource "aws_iam_user" "developer" {

  name = "developer"

}
```

---

## Exercise 2

Create an IAM Group.

```hcl
resource "aws_iam_group" "developers" {

  name = "Developers"

}
```

---

## Exercise 3

Attach an AWS Managed Policy.

```hcl
resource "aws_iam_role_policy_attachment" "ssm" {

  role = aws_iam_role.ec2_role.name

  policy_arn = "arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore"

}
```

---

## Exercise 4

Create an IAM Instance Profile.

```hcl
resource "aws_iam_instance_profile" "profile" {

  name = "EC2Profile"

  role = aws_iam_role.ec2_role.name

}
```

---

## Exercise 5

Launch an EC2 instance with an IAM Role attached.

Verify that it can access AWS Systems Manager without using access keys.

---

# 🧩 Mini Project

Create a complete IAM solution using Terraform.

Resources:

- 3 IAM Users
- 1 IAM Group
- 1 Custom S3 ReadOnly Policy
- 1 IAM Role
- 1 Instance Profile
- 1 EC2 Instance
- Attach AmazonSSMManagedInstanceCore policy

Project structure:

```text
terraform-aws-iam/

├── main.tf
├── iam.tf
├── ec2.tf
├── variables.tf
├── outputs.tf
├── providers.tf
├── versions.tf
├── terraform.tfvars
└── README.md
```

Run:

```bash
terraform init

terraform fmt

terraform validate

terraform plan

terraform apply

terraform destroy
```

Expected outcome:

- IAM users are created.
- IAM group is created.
- Custom policy is attached.
- IAM role is created.
- EC2 launches with an Instance Profile.
- EC2 can access AWS Systems Manager securely.

---

# 📚 Further Reading

- Terraform AWS Provider Documentation
- Terraform IAM Resources Documentation
- AWS IAM User Guide
- AWS IAM Best Practices
- AWS Security Best Practices
- HashiCorp Learn

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [19 - Terraform CLI](19-Terraform-CLI.md) | [Terraform Roadmap](README.md) | [21 - Terraform with AWS VPC](21-Terraform-with-AWS-VPC.md) |

---

# 🚀 What's Next?

In **Chapter 21 – Terraform with AWS VPC**, you'll learn:

- 🌐 Create a VPC using Terraform
- 🛣️ Public and Private Subnets
- 🌍 Internet Gateway
- 🚪 NAT Gateway
- 📋 Route Tables
- 🔒 Security Groups
- 📡 Network ACLs
- 🏗️ Production-Ready VPC Architecture
