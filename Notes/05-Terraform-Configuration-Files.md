# Terraform Notes
# Chapter 05 - Terraform Configuration Files

> 📘 **Level:** Beginner
> ⏱️ **Estimated Reading Time:** 60–75 minutes
> 🛠️ **Practice Time:** 3–4 hours

---

# 📚 Table of Contents

1. Introduction
2. Terraform Project Structure
3. main.tf
4. variables.tf
5. terraform.tfvars
6. outputs.tf
7. versions.tf
8. providers.tf
9. File Loading Behavior
10. Organizing Large Projects
11. Best Practices
12. Summary
13. Interview Questions
14. Practice Exercises
15. Mini Project
16. Further Reading

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Terraform configuration files
- Organize Terraform projects
- Separate variables and outputs
- Manage provider configurations
- Follow production-ready project structures

---

# 📖 Introduction

Terraform projects consist of one or more **`.tf` (Terraform)** files.

Terraform automatically loads **all `.tf` files** in the current working directory.

For example:

```text
terraform-demo/

├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
├── providers.tf
└── versions.tf
```

You can split your configuration across multiple files to improve readability and maintainability.

---

# 📂 Terraform Project Structure

A recommended structure for small projects:

```text
terraform-demo/

├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
├── providers.tf
└── versions.tf
```

For larger projects:

```text
terraform-project/

├── modules/
│   ├── ec2/
│   ├── vpc/
│   └── s3/
│
├── environments/
│   ├── dev/
│   ├── test/
│   └── prod/
│
├── main.tf
├── variables.tf
├── outputs.tf
├── providers.tf
├── versions.tf
└── terraform.tfvars
```

---

# 📄 main.tf

`main.tf` contains the primary infrastructure resources.

Example:

```hcl
resource "aws_instance" "web" {

  ami           = "ami-xxxxxxxx"

  instance_type = "t2.micro"

  tags = {

    Name = "Terraform-EC2"

  }

}
```

Typically, this file contains:

- Resources
- Modules
- Data sources

---

# 📝 variables.tf

This file defines input variables.

Example:

```hcl
variable "region" {

  description = "AWS Region"

  type = string

  default = "us-east-1"

}
```

Another example:

```hcl
variable "instance_type" {

  type = string

  default = "t2.micro"

}
```

Benefits:

- Reusable code
- Cleaner configuration
- Environment-specific values

---

# ⚙️ terraform.tfvars

`terraform.tfvars` stores values for variables.

Example:

```hcl
region = "ap-south-1"

instance_type = "t3.micro"
```

Terraform automatically loads this file.

Run:

```bash
terraform apply
```

No need to specify variables manually.

---

# 📤 outputs.tf

Outputs display information after resources are created.

Example:

```hcl
output "instance_id" {

  value = aws_instance.web.id

}
```

Another example:

```hcl
output "public_ip" {

  value = aws_instance.web.public_ip

}
```

Display outputs:

```bash
terraform output
```

---

# 📄 versions.tf

This file specifies Terraform and provider versions.

Example:

```hcl
terraform {

  required_version = ">= 1.5.0"

  required_providers {

    aws = {

      source  = "hashicorp/aws"

      version = "~> 5.0"

    }

  }

}
```

Benefits:

- Consistent deployments
- Provider compatibility
- Prevents unexpected upgrades

---

# ☁️ providers.tf

This file contains provider configuration.

Example:

```hcl
provider "aws" {

  region = var.region

}
```

Using variables:

```hcl
variable "region" {

  default = "us-east-1"

}
```

This allows easy region changes without modifying provider code.

---

# 📋 File Loading Behavior

Terraform automatically loads:

```text
✔ main.tf

✔ variables.tf

✔ outputs.tf

✔ providers.tf

✔ versions.tf

✔ *.tf

✔ terraform.tfvars
```

Terraform ignores unrelated files.

Example:

```text
README.md

notes.txt

image.png
```

---

# 🏗️ Example Project

Project structure:

```text
terraform-demo/

├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
├── providers.tf
└── versions.tf
```

**versions.tf**

```hcl
terraform {

  required_version = ">=1.5.0"

  required_providers {

    aws = {

      source = "hashicorp/aws"

      version = "~>5.0"

    }

  }

}
```

**providers.tf**

```hcl
provider "aws" {

  region = var.region

}
```

**variables.tf**

```hcl
variable "region" {

  default = "us-east-1"

}

variable "instance_type" {

  default = "t2.micro"

}
```

**terraform.tfvars**

```hcl
region = "ap-south-1"

instance_type = "t3.micro"
```

**main.tf**

```hcl
resource "aws_instance" "web" {

  ami = "ami-xxxxxxxx"

  instance_type = var.instance_type

}
```

**outputs.tf**

```hcl
output "instance_id" {

  value = aws_instance.web.id

}
```

---

# 💻 Useful Commands

Initialize:

```bash
terraform init
```

Validate:

```bash
terraform validate
```

Format:

```bash
terraform fmt
```

Plan:

```bash
terraform plan
```

Apply:

```bash
terraform apply
```

View outputs:

```bash
terraform output
```

Destroy:

```bash
terraform destroy
```

---

# 🏗️ Configuration Flow

```text
variables.tf

        │

        ▼

terraform.tfvars

        │

        ▼

providers.tf

        │

        ▼

main.tf

        │

        ▼

AWS Infrastructure

        │

        ▼

outputs.tf
```

---

# 🏆 Best Practices

- ✅ Separate variables into `variables.tf`
- ✅ Store outputs in `outputs.tf`
- ✅ Keep providers in `providers.tf`
- ✅ Lock provider versions
- ✅ Never hardcode sensitive values
- ✅ Use meaningful variable names
- ✅ Format code with `terraform fmt`
- ✅ Keep modules independent

---

# ⚠️ Common Mistakes

| Mistake | Solution |
|----------|----------|
| Hardcoding values | Use variables |
| Mixing provider and resources | Separate into dedicated files |
| Missing outputs | Create `outputs.tf` |
| Not locking versions | Use `versions.tf` |
| Duplicate variable definitions | Define variables once |

---

# 📝 Key Takeaways

- Terraform automatically loads all `.tf` files.
- Organize configurations into separate files.
- Use variables for reusable infrastructure.
- Outputs provide useful deployment information.
- Provider versions should be locked.

---

# 📋 Summary

In this chapter, you learned:

- Terraform file structure
- `main.tf`
- `variables.tf`
- `terraform.tfvars`
- `outputs.tf`
- `providers.tf`
- `versions.tf`
- Project organization

---

# ❓ Interview Questions

## Beginner

1. What is `main.tf`?
2. What is `variables.tf`?
3. What is `terraform.tfvars`?
4. What is `outputs.tf`?
5. Does Terraform load all `.tf` files automatically?

---

## Intermediate

6. Why separate Terraform configurations?
7. What is the purpose of `versions.tf`?
8. Difference between `variables.tf` and `terraform.tfvars`?
9. Where should provider configuration be stored?
10. How are outputs used?

---

## Advanced

11. How would you organize a production Terraform project?
12. How do variables improve maintainability?
13. Why should provider versions be locked?
14. How does Terraform resolve variables?
15. How would you structure multiple environments?

---

# 🎯 Practice Exercises

## Exercise 1

Create the following files:

```text
main.tf

variables.tf

outputs.tf

providers.tf

versions.tf

terraform.tfvars
```

---

## Exercise 2

Initialize Terraform.

```bash
terraform init
```

---

## Exercise 3

Validate configuration.

```bash
terraform validate
```

---

## Exercise 4

Format Terraform files.

```bash
terraform fmt
```

---

## Exercise 5

Display outputs.

```bash
terraform output
```

---

# 🧩 Mini Project

Create a Terraform project that provisions:

- One EC2 Instance
- One S3 Bucket

Organize the project using:

```text
terraform-aws/

├── main.tf
├── variables.tf
├── outputs.tf
├── providers.tf
├── versions.tf
└── terraform.tfvars
```

Run:

```bash
terraform init

terraform fmt

terraform validate

terraform plan

terraform apply

terraform output
```

Finally, remove all resources:

```bash
terraform destroy
```

---

# 📚 Further Reading

- Terraform Language Documentation
- Terraform Best Practices
- AWS Provider Documentation
- Terraform Registry
- HashiCorp Learn

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [04 - Providers and Resources](04-Providers-and-Resources.md) | [Terraform Roadmap](README.md) | [06 - Terraform State](06-Terraform-State.md) |

---

# 🚀 What's Next?

In **Chapter 06 – Terraform State**, you'll learn:

- 📄 What is `terraform.tfstate`?
- 💾 State File Management
- 🔄 State Refresh
- 📝 State Commands
- ☁️ Local vs Remote State
- 🎯 Why Terraform State is Critical
