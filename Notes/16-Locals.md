# Terraform Notes
# Chapter 16 - Local Values (Locals)

> 📘 **Level:** Beginner to Intermediate
> ⏱️ **Estimated Reading Time:** 60–75 minutes
> 🛠️ **Practice Time:** 3–4 hours

---

# 📚 Table of Contents

1. Introduction
2. What are Local Values?
3. Why Use Locals?
4. Local Syntax
5. Using Locals
6. Multiple Local Values
7. Locals with Functions
8. Locals with Expressions
9. Locals vs Variables
10. Best Practices
11. Summary
12. Interview Questions
13. Practice Exercises
14. Mini Project
15. Further Reading

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Local Values
- Create reusable expressions
- Simplify Terraform configurations
- Combine locals with variables and functions
- Follow Terraform best practices

---

# 📖 Introduction

As Terraform projects grow, the same values and expressions are often repeated throughout the configuration.

Example:

```hcl
bucket = "${var.project_name}-dev-bucket"

tags = {

  Project = var.project_name

}
```

Repeating expressions makes the code harder to maintain.

Terraform provides **Local Values (Locals)** to define reusable values once and reference them throughout the project.

---

# 💡 What are Local Values?

A **Local Value** is a named expression that is evaluated once and reused within the same Terraform module.

Locals help:

- Reduce duplicate code
- Improve readability
- Simplify complex expressions
- Centralize reusable values

---

# 🎯 Why Use Locals?

Benefits:

- Reusable values
- Cleaner configuration
- Easier maintenance
- Reduced duplication
- Better readability

---

# 📝 Local Syntax

General syntax:

```hcl
locals {

  name = value

}
```

Example:

```hcl
locals {

  environment = "dev"

}
```

Reference a local value:

```hcl
local.environment
```

---

# 🏗️ Basic Example

```hcl
locals {

  instance_type = "t3.micro"

}

resource "aws_instance" "web" {

  ami = "ami-xxxxxxxx"

  instance_type = local.instance_type

}
```

Terraform replaces:

```text
local.instance_type

↓

t3.micro
```

---

# 📦 Multiple Local Values

```hcl
locals {

  project_name = "terraform-demo"

  environment = "dev"

  instance_type = "t3.micro"

}
```

Usage:

```hcl
tags = {

  Project = local.project_name

  Environment = local.environment

}
```

---

# 🔧 Locals with Functions

Locals can use Terraform functions.

Example:

```hcl
locals {

  bucket_name = lower(

    "${var.project_name}-bucket"

  )

}
```

Output:

```text
terraform-demo-bucket
```

---

# 🔄 Locals with Expressions

Example:

```hcl
locals {

  instance_type = var.environment == "prod"

    ? "t3.large"

    : "t2.micro"

}
```

Terraform selects:

```text
Production

↓

t3.large

Development

↓

t2.micro
```

---

# 🏷️ Locals for Tags

Instead of repeating tags:

```hcl
tags = {

  Project = "Demo"

  Owner = "DevOps"

  Environment = "Development"

}
```

Use locals:

```hcl
locals {

  common_tags = {

    Project = "Demo"

    Owner = "DevOps"

    Environment = "Development"

  }

}
```

Apply:

```hcl
resource "aws_s3_bucket" "bucket" {

  bucket = "demo-bucket"

  tags = local.common_tags

}
```

---

# 🌍 Real-World Example

```hcl
variable "project_name" {

  default = "demo"

}

variable "environment" {

  default = "dev"

}

locals {

  bucket_name = lower(

    "${var.project_name}-${var.environment}-bucket"

  )

  instance_type = var.environment == "prod"

    ? "t3.large"

    : "t2.micro"

}

resource "aws_s3_bucket" "bucket" {

  bucket = local.bucket_name

}

resource "aws_instance" "web" {

  ami = "ami-xxxxxxxx"

  instance_type = local.instance_type

}
```

---

# ⚖️ Locals vs Variables

| Local Values | Variables |
|--------------|-----------|
| Defined inside the module | Input provided by users |
| Cannot be overridden | Can be overridden |
| Used for internal logic | Used for external configuration |
| Referenced as `local.name` | Referenced as `var.name` |

---

# 📁 Suggested Project Structure

```text
terraform-project/

├── locals.tf
├── main.tf
├── variables.tf
├── outputs.tf
├── providers.tf
├── versions.tf
└── terraform.tfvars
```

Keeping locals in a dedicated `locals.tf` file improves readability.

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

Destroy:

```bash
terraform destroy
```

---

# 🏗️ Local Value Workflow

```text
Input Variables

        │

        ▼

Local Values

        │

        ▼

Resources

        │

        ▼

AWS Infrastructure
```

---

# 🏆 Best Practices

- ✅ Use locals for repeated expressions.
- ✅ Keep local names descriptive.
- ✅ Store common tags in locals.
- ✅ Keep complex logic inside locals.
- ✅ Place locals in `locals.tf`.
- ✅ Use variables for external inputs.
- ✅ Keep locals module-specific.

---

# ⚠️ Common Mistakes

| Mistake | Solution |
|----------|----------|
| Repeating the same expression | Use a local value |
| Using locals for user input | Use variables |
| Long inline expressions | Move logic to locals |
| Hardcoded tags | Store tags in a local map |
| Creating unnecessary locals | Only define reusable values |

---

# 📝 Key Takeaways

- Local values simplify Terraform code.
- Locals reduce duplication.
- Locals can use variables, functions, and expressions.
- Locals improve readability and maintainability.
- Local values are available only within the current module.

---

# 📋 Summary

In this chapter, you learned:

- Local Values
- Local Syntax
- Functions with Locals
- Expressions with Locals
- Common Tags
- Locals vs Variables

---

# ❓ Interview Questions

## Beginner

1. What are Local Values?
2. Why use locals?
3. How do you reference a local value?
4. What is the difference between locals and variables?
5. Where should locals be stored?

---

## Intermediate

6. How do locals improve readability?
7. Can locals use Terraform functions?
8. Can locals reference variables?
9. Why use locals for common tags?
10. When should you avoid creating locals?

---

## Advanced

11. How would you organize locals in a large Terraform project?
12. How do locals improve reusable modules?
13. Explain the evaluation order of variables and locals.
14. How do locals help reduce configuration drift?
15. What are best practices for naming local values?

---

# 🎯 Practice Exercises

## Exercise 1

Create a local value.

```hcl
locals {

  environment = "dev"

}
```

---

## Exercise 2

Reference a local value.

```hcl
local.environment
```

---

## Exercise 3

Create a local tag map.

```hcl
locals {

  common_tags = {

    Owner = "DevOps"

  }

}
```

---

## Exercise 4

Use a conditional expression.

```hcl
locals {

  instance_type = var.environment == "prod"

    ? "t3.large"

    : "t2.micro"

}
```

---

## Exercise 5

Generate an S3 bucket name using a local value.

```hcl
bucket = local.bucket_name
```

---

# 🧩 Mini Project

Create a Terraform project that:

- Uses locals for common tags
- Generates S3 bucket names dynamically
- Selects EC2 instance types based on environment
- Creates reusable values for multiple resources

Project structure:

```text
terraform-locals/

├── locals.tf
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

Verify:

- Tags are applied consistently.
- Bucket names are generated from local values.
- Instance type changes based on the selected environment.

---

# 📚 Further Reading

- Terraform Local Values Documentation
- Terraform Language Documentation
- HashiCorp Learn
- AWS Provider Documentation
- Terraform Best Practices

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [15 - Data Sources](15-Data-Sources.md) | [Terraform Roadmap](README.md) | [17 - Lifecycle Meta-Arguments.md](17-Lifecycle-Meta-Arguments.md) |

---

# 🚀 What's Next?

In **Chapter 17 – Lifecycle Meta-Arguments**, you'll learn:

- 🔄 What is the Lifecycle Block?
- 🛡️ `create_before_destroy`
- 🚫 `prevent_destroy`
- 🔍 `ignore_changes`
- 🔁 `replace_triggered_by`
- 🎯 Production Best Practices
