
# Terraform Notes
# Chapter 10 - Terraform Modules

> 📘 **Level:** Intermediate
> ⏱️ **Estimated Reading Time:** 75–90 minutes
> 🛠️ **Practice Time:** 4–5 hours

---

# 📚 Table of Contents

1. Introduction
2. What are Modules?
3. Why Use Modules?
4. Module Types
5. Root Module
6. Child Module
7. Module Structure
8. Creating Your First Module
9. Module Inputs
10. Module Outputs
11. Calling a Module
12. Using Multiple Modules
13. Terraform Registry Modules
14. Module Versioning
15. Best Practices
16. Summary
17. Interview Questions
18. Practice Exercises
19. Mini Project
20. Further Reading

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Terraform Modules
- Create reusable modules
- Pass variables into modules
- Return values using outputs
- Use modules from the Terraform Registry
- Follow production-ready module design

---

# 📖 Introduction

As Terraform projects grow, keeping all resources inside a single `main.tf` file becomes difficult.

Instead of duplicating code, Terraform allows you to group resources into **Modules**.

Think of a module as a reusable building block.

Example:

Instead of writing the EC2 configuration multiple times:

```text
Development EC2

↓

Testing EC2

↓

Production EC2
```

You create one module and reuse it.

---

# 💡 What are Modules?

A **Module** is a container for multiple Terraform resources that are used together.

A module can include:

- Resources
- Variables
- Outputs
- Locals
- Data Sources

Every Terraform configuration has at least one module called the **Root Module**.

---

# 🎯 Why Use Modules?

Benefits:

- Reusable code
- Easier maintenance
- Better organization
- Reduced duplication
- Consistent infrastructure
- Faster deployments

---

# 📦 Module Types

Terraform supports three types of modules.

| Module Type | Description |
|--------------|-------------|
| Root Module | Main Terraform configuration |
| Child Module | Module created inside your project |
| Registry Module | Module downloaded from Terraform Registry |

---

# 🏠 Root Module

The directory where Terraform commands are executed is called the **Root Module**.

Example:

```text
terraform-project/

├── main.tf
├── variables.tf
├── outputs.tf
└── terraform.tfvars
```

Running:

```bash
terraform apply
```

executes the Root Module.

---

# 👶 Child Module

A child module is stored inside another directory.

Example:

```text
terraform-project/

├── modules/
│
├── ec2/
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
│
└── main.tf
```

The Root Module calls the Child Module.

---

# 📁 Recommended Module Structure

```text
terraform-project/

├── modules/
│
├── ec2/
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
│
├── s3/
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
│
├── vpc/
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
│
├── main.tf
├── variables.tf
├── outputs.tf
└── providers.tf
```

---

# 🏗️ Creating Your First Module

Project structure:

```text
terraform-demo/

├── modules/
│
├── s3/
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
│
└── main.tf
```

---

## modules/s3/main.tf

```hcl
resource "aws_s3_bucket" "bucket" {

  bucket = var.bucket_name

}
```

---

## modules/s3/variables.tf

```hcl
variable "bucket_name" {

  type = string

}
```

---

## modules/s3/outputs.tf

```hcl
output "bucket_id" {

  value = aws_s3_bucket.bucket.id

}
```

---

# 🔗 Calling a Module

Root module:

```hcl
module "demo_bucket" {

  source = "./modules/s3"

  bucket_name = "terraform-demo-bucket"

}
```

Run:

```bash
terraform init

terraform apply
```

Terraform creates the S3 bucket using the child module.

---

# 📝 Module Inputs

Variables passed into a module are called **Inputs**.

Example:

```hcl
module "web_server" {

  source = "./modules/ec2"

  instance_type = "t3.micro"

  instance_name = "web-server"

}
```

Module variables:

```hcl
variable "instance_type" {}

variable "instance_name" {}
```

---

# 📤 Module Outputs

Outputs expose values from a module.

Child module:

```hcl
output "instance_id" {

  value = aws_instance.web.id

}
```

Root module:

```hcl
output "web_instance_id" {

  value = module.web_server.instance_id

}
```

---

# 📦 Using Multiple Modules

Example:

```hcl
module "network" {

  source = "./modules/vpc"

}

module "storage" {

  source = "./modules/s3"

  bucket_name = "project-storage"

}

module "compute" {

  source = "./modules/ec2"

  instance_type = "t3.micro"

}
```

Execution flow:

```text
Root Module

     │

     ▼

VPC Module

S3 Module

EC2 Module

     │

     ▼

AWS Infrastructure
```

---

# 🌐 Terraform Registry Modules

Terraform provides thousands of reusable modules through the Terraform Registry.

Example:

```hcl
module "vpc" {

  source = "terraform-aws-modules/vpc/aws"

  version = "5.0.0"

  name = "demo-vpc"

  cidr = "10.0.0.0/16"

  azs = [

    "ap-south-1a",

    "ap-south-1b"

  ]

}
```

Download modules:

```bash
terraform init
```

---

# 🔖 Module Versioning

Always specify module versions when using Registry modules.

Example:

```hcl
module "vpc" {

  source = "terraform-aws-modules/vpc/aws"

  version = "5.0.0"

}
```

Benefits:

- Stable deployments
- Predictable behavior
- Easier upgrades

---

# 🏗️ Example Project Structure

```text
terraform-project/

├── modules/
│
├── ec2/
│
├── s3/
│
├── vpc/
│
├── main.tf
├── variables.tf
├── outputs.tf
├── providers.tf
├── versions.tf
└── terraform.tfvars
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

# 🏆 Best Practices

- ✅ Create small, focused modules.
- ✅ Keep modules reusable.
- ✅ Avoid hardcoded values.
- ✅ Pass configuration through variables.
- ✅ Return useful outputs.
- ✅ Pin module versions.
- ✅ Store modules in a dedicated `modules/` directory.
- ✅ Document module inputs and outputs.

---

# ⚠️ Common Mistakes

| Mistake | Solution |
|----------|----------|
| Large monolithic modules | Split into smaller modules |
| Hardcoded values | Use input variables |
| Missing outputs | Define outputs for reusable values |
| No version pinning | Specify module versions |
| Repeating code | Create reusable modules |

---

# 📝 Key Takeaways

- Modules make Terraform reusable.
- Every project has a Root Module.
- Child Modules organize infrastructure.
- Inputs configure modules.
- Outputs expose module values.
- Registry Modules accelerate development.

---

# 📋 Summary

In this chapter, you learned:

- Terraform Modules
- Root Modules
- Child Modules
- Module Inputs
- Module Outputs
- Module Registry
- Module Versioning

---

# ❓ Interview Questions

## Beginner

1. What is a Terraform Module?
2. What is the Root Module?
3. What is a Child Module?
4. Why are modules used?
5. How do you call a module?

---

## Intermediate

6. Explain module inputs and outputs.
7. What is the Terraform Registry?
8. Why should module versions be pinned?
9. How do modules improve maintainability?
10. How are modules organized in production?

---

## Advanced

11. How would you design reusable Terraform modules?
12. What are the advantages of small modules?
13. How do modules communicate with each other?
14. Explain Registry Modules vs Local Modules.
15. How would you structure a large AWS Terraform project?

---

# 🎯 Practice Exercises

## Exercise 1

Create a module named `s3`.

```text
modules/

└── s3/
```

---

## Exercise 2

Add a resource.

```hcl
resource "aws_s3_bucket" "demo" {

  bucket = var.bucket_name

}
```

---

## Exercise 3

Pass a variable.

```hcl
bucket_name = "terraform-demo-bucket"
```

---

## Exercise 4

Output the bucket ID.

```hcl
output "bucket_id" {

  value = aws_s3_bucket.demo.id

}
```

---

## Exercise 5

Call the module.

```bash
terraform init

terraform apply
```

---

# 🧩 Mini Project

Create a modular Terraform project for AWS that provisions:

- One VPC module
- One EC2 module
- One S3 module

Project structure:

```text
terraform-modules/

├── modules/
│   ├── ec2/
│   ├── s3/
│   └── vpc/
│
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

terraform validate

terraform plan

terraform apply

terraform output

terraform destroy
```

---

# 📚 Further Reading

- Terraform Modules Documentation
- Terraform Registry
- HashiCorp Learn
- AWS Provider Documentation
- Terraform Module Best Practices

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [09 - Expressions and Loops](09-Expressions-and-Loops.md) | [Terraform Roadmap](README.md) | [11 - Data Sources.md](11-Data-Sources.md) |

---

# 🚀 What's Next?

In **Chapter 11 – Data Sources**, you'll learn:

- 📖 What are Data Sources?
- 🔍 Reading Existing AWS Resources
- 📦 Using Existing VPCs, AMIs, and Security Groups
- 🔗 Combining Data Sources with Resources
- 🎯 Real-World AWS Examples
