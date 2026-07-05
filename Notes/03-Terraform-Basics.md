# Terraform Notes
# Chapter 03 - Terraform Basics

> 📘 **Level:** Beginner
> ⏱️ **Estimated Reading Time:** 60–75 minutes
> 🛠️ **Practice Time:** 3–4 hours

---

# 📚 Table of Contents

1. What is Terraform Configuration?
2. HashiCorp Configuration Language (HCL)
3. Terraform Blocks
4. Providers
5. Resources
6. Arguments and Attributes
7. Terraform Workflow
8. First Terraform Project
9. Useful Commands
10. Best Practices
11. Summary
12. Interview Questions
13. Practice Exercises
14. Mini Project
15. Further Reading

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Terraform configuration files
- Learn HashiCorp Configuration Language (HCL)
- Understand Providers and Resources
- Create your first AWS resource
- Execute the Terraform workflow
- Understand Terraform state creation

---

# 📖 What is Terraform Configuration?

Terraform uses configuration files written in **HashiCorp Configuration Language (HCL)**.

Configuration files describe the desired infrastructure.

Example:

```text
"I need one EC2 instance."

"I need one VPC."

"I need one S3 Bucket."
```

Terraform compares the desired state with the current infrastructure and performs the required actions.

---

# 💡 What is HCL?

HCL (HashiCorp Configuration Language) is a human-readable language used by Terraform.

Example:

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = "terraform-demo-bucket"
}
```

Advantages:

- Easy to read
- Easy to maintain
- Declarative
- Supports variables and expressions

---

# 🏗️ Terraform Configuration Blocks

Terraform configurations are made up of different block types.

Common blocks include:

| Block | Purpose |
|--------|---------|
| terraform | Configure Terraform settings |
| provider | Configure cloud provider |
| resource | Create infrastructure |
| variable | Declare input variables |
| output | Display values |
| locals | Define reusable values |
| data | Read existing resources |

---

# ⚙️ Terraform Block

The `terraform` block configures Terraform itself.

Example:

```hcl
terraform {
  required_version = ">= 1.5.0"
}
```

You can also specify required providers:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}
```

---

# ☁️ Provider Block

A provider tells Terraform which platform to interact with.

Example:

```hcl
provider "aws" {
  region = "us-east-1"
}
```

Terraform downloads the provider plugin during:

```bash
terraform init
```

---

# 📦 Resource Block

A resource defines an infrastructure component.

Example:

```hcl
resource "aws_instance" "web" {

  ami           = "ami-xxxxxxxx"

  instance_type = "t2.micro"
}
```

Syntax:

```hcl
resource "<RESOURCE_TYPE>" "<LOCAL_NAME>" {

}
```

Example:

```hcl
resource "aws_s3_bucket" "backup" {

}
```

Here:

- Resource Type → `aws_s3_bucket`
- Local Name → `backup`

---

# 📝 Arguments and Attributes

Arguments configure a resource.

Example:

```hcl
resource "aws_instance" "web" {

  ami           = "ami-xxxxxxxx"

  instance_type = "t2.micro"

  tags = {
    Name = "Terraform-Server"
  }
}
```

Common arguments:

- ami
- instance_type
- tags
- region
- bucket
- cidr_block

Attributes are values returned by Terraform after resource creation.

Example:

```hcl
aws_instance.web.public_ip
```

---

# 🚀 Terraform Workflow

The Terraform workflow consists of:

```text
Write Configuration

↓

terraform init

↓

terraform fmt

↓

terraform validate

↓

terraform plan

↓

terraform apply

↓

terraform destroy
```

---

# 💻 Creating Your First AWS Resource

Create a file named:

```text
main.tf
```

Example:

```hcl
terraform {

  required_providers {

    aws = {

      source = "hashicorp/aws"

      version = "~> 5.0"

    }

  }

}

provider "aws" {

  region = "us-east-1"

}

resource "aws_s3_bucket" "demo" {

  bucket = "my-first-terraform-demo-bucket"

}
```

Initialize Terraform:

```bash
terraform init
```

Validate configuration:

```bash
terraform validate
```

View execution plan:

```bash
terraform plan
```

Create infrastructure:

```bash
terraform apply
```

Destroy infrastructure:

```bash
terraform destroy
```

---

# 📁 Project Structure

```text
terraform-demo/

├── main.tf
├── .terraform/
├── terraform.tfstate
├── terraform.tfstate.backup
└── .terraform.lock.hcl
```

---

# 📄 Generated Files

| File | Purpose |
|------|---------|
| .terraform | Provider plugins |
| terraform.tfstate | Infrastructure state |
| terraform.tfstate.backup | Backup of state |
| .terraform.lock.hcl | Provider version lock |

---

# 💻 Frequently Used Commands

Initialize:

```bash
terraform init
```

Format code:

```bash
terraform fmt
```

Validate:

```bash
terraform validate
```

Preview changes:

```bash
terraform plan
```

Apply changes:

```bash
terraform apply
```

Destroy resources:

```bash
terraform destroy
```

Show state:

```bash
terraform show
```

List state resources:

```bash
terraform state list
```

---

# 🏗️ Execution Flow

```text
main.tf

↓

Terraform CLI

↓

Provider

↓

AWS API

↓

AWS Resources

↓

State File Updated
```

---

# 🏆 Best Practices

- ✅ Use descriptive resource names.
- ✅ Format code using `terraform fmt`.
- ✅ Validate before applying.
- ✅ Review execution plans carefully.
- ✅ Keep configurations modular.
- ✅ Avoid hardcoding values.
- ✅ Use version control.
- ✅ Never edit the state file manually.

---

# ⚠️ Common Mistakes

| Mistake | Solution |
|----------|----------|
| Forgetting `terraform init` | Initialize first |
| Invalid HCL syntax | Run `terraform validate` |
| Incorrect AWS region | Verify provider configuration |
| Duplicate bucket name | Use globally unique bucket names |
| Editing state manually | Use Terraform commands instead |

---

# 📝 Key Takeaways

- HCL is Terraform's configuration language.
- Providers connect Terraform to cloud platforms.
- Resources define infrastructure.
- Terraform follows a declarative approach.
- The state file tracks infrastructure.

---

# 📋 Summary

In this chapter, you learned:

- Terraform configuration
- HCL syntax
- Terraform blocks
- Providers
- Resources
- Arguments and attributes
- Terraform workflow
- First Terraform project

---

# ❓ Interview Questions

## Beginner

1. What is HCL?
2. What is a Provider?
3. What is a Resource?
4. What is the purpose of `terraform init`?
5. What does `terraform plan` do?

---

## Intermediate

6. Explain Terraform configuration blocks.
7. Difference between arguments and attributes?
8. What files are generated after `terraform init`?
9. What is the Terraform workflow?
10. What is the purpose of `.terraform.lock.hcl`?

---

## Advanced

11. Explain how Terraform interacts with AWS APIs.
12. Why should infrastructure be declarative?
13. How does Terraform know what resources exist?
14. What happens if the state file is deleted?
15. How would you organize Terraform code for large projects?

---

# 🎯 Practice Exercises

## Exercise 1

Create a project folder.

```bash
mkdir terraform-demo

cd terraform-demo
```

---

## Exercise 2

Create a Terraform configuration.

```bash
touch main.tf
```

---

## Exercise 3

Initialize Terraform.

```bash
terraform init
```

---

## Exercise 4

Validate configuration.

```bash
terraform validate
```

---

## Exercise 5

Preview infrastructure changes.

```bash
terraform plan
```

---

# 🧩 Mini Project

Create a Terraform project that provisions:

- One AWS S3 Bucket

Project structure:

```text
terraform-demo/

├── main.tf
├── terraform.tfstate
└── .terraform.lock.hcl
```

Run:

```bash
terraform init

terraform fmt

terraform validate

terraform plan

terraform apply
```

Finally, remove the infrastructure:

```bash
terraform destroy
```

---

# 📚 Further Reading

- Terraform Language Documentation
- Terraform AWS Provider Documentation
- Terraform Registry
- HashiCorp Learn
- AWS IAM Documentation

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [02 - Installing Terraform](02-Installing-Terraform.md) | [Terraform Roadmap](README.md) | [04 - Providers and Resources](04-Providers-and-Resources.md) |

---

# 🚀 What's Next?

In **Chapter 04 – Providers and Resources**, you'll learn:

- ☁️ What are Providers?
- 📦 Resource Types
- 🔌 Provider Configuration
- 📝 Resource Arguments
- 🏗️ Creating Multiple AWS Resources
- 🎯 Provider Versioning and Best Practices
