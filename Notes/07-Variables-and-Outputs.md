# Terraform Notes
# Chapter 07 - Variables and Outputs

> 📘 **Level:** Beginner to Intermediate
> ⏱️ **Estimated Reading Time:** 60–75 minutes
> 🛠️ **Practice Time:** 3–4 hours

---

# 📚 Table of Contents

1. Introduction
2. What are Variables?
3. Why Use Variables?
4. Variable Types
5. Declaring Variables
6. Using Variables
7. Variable Values
8. Variable Precedence
9. Sensitive Variables
10. Output Values
11. Output Commands
12. Best Practices
13. Summary
14. Interview Questions
15. Practice Exercises
16. Mini Project
17. Further Reading

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Terraform Variables
- Declare and use variables
- Pass values to Terraform
- Understand variable precedence
- Work with sensitive variables
- Create and use outputs

---

# 📖 Introduction

Hardcoding values in Terraform configurations makes infrastructure difficult to reuse.

Example:

```hcl
resource "aws_instance" "web" {

  instance_type = "t2.micro"

}
```

What if you want:

- Development → t2.micro
- Testing → t3.small
- Production → t3.medium

Instead of changing the code every time, use **Variables**.

---

# 💡 What are Variables?

Variables allow you to make Terraform configurations reusable and configurable.

Instead of hardcoding values, you define variables and assign values separately.

Example:

```hcl
variable "instance_type" {

  default = "t2.micro"

}
```

Use it:

```hcl
instance_type = var.instance_type
```

---

# 🎯 Why Use Variables?

Variables provide:

- Reusable code
- Cleaner configuration
- Easy environment management
- Less duplication
- Better maintainability

---

# 📝 Declaring Variables

Variables are usually defined in **variables.tf**.

Example:

```hcl
variable "region" {

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

---

# 📦 Variable Types

Terraform supports several variable types.

| Type | Example |
|------|---------|
| string | `"Hello"` |
| number | `100` |
| bool | `true` |
| list | `["a","b","c"]` |
| map | `{ env = "dev" }` |
| object | `{ name="web", cpu=2 }` |
| tuple | `["dev", 2]` |
| set | `["a","b"]` |

---

# 💻 String Variable

```hcl
variable "environment" {

  type = string

  default = "development"

}
```

---

# 🔢 Number Variable

```hcl
variable "disk_size" {

  type = number

  default = 20

}
```

---

# ✅ Boolean Variable

```hcl
variable "enable_monitoring" {

  type = bool

  default = true

}
```

---

# 📋 List Variable

```hcl
variable "availability_zones" {

  type = list(string)

  default = [

    "ap-south-1a",

    "ap-south-1b"

  ]

}
```

Use:

```hcl
availability_zone = var.availability_zones[0]
```

---

# 🗺️ Map Variable

```hcl
variable "tags" {

  type = map(string)

  default = {

    Environment = "Dev"

    Owner = "Admin"

  }

}
```

Use:

```hcl
tags = var.tags
```

---

# ⚙️ Using Variables

Example:

```hcl
resource "aws_instance" "web" {

  ami = "ami-xxxxxxxx"

  instance_type = var.instance_type

}
```

Terraform replaces:

```text
var.instance_type
```

with its actual value.

---

# 📄 terraform.tfvars

Store variable values in:

```text
terraform.tfvars
```

Example:

```hcl
region = "ap-south-1"

instance_type = "t3.micro"

environment = "production"
```

Terraform automatically loads this file.

---

# 💻 Passing Variables from CLI

Example:

```bash
terraform apply \
-var="instance_type=t3.micro"
```

Multiple variables:

```bash
terraform apply \
-var="region=ap-south-1" \
-var="environment=dev"
```

---

# 📂 Using Variable Files

Example:

```text
dev.tfvars

test.tfvars

prod.tfvars
```

Apply:

```bash
terraform apply \
-var-file="prod.tfvars"
```

---

# 🏆 Variable Precedence

Terraform resolves variables in this order:

```text
Command Line (-var)

↓

Variable File (-var-file)

↓

terraform.tfvars

↓

Environment Variables

↓

Default Values
```

Highest priority:

```text
-var
```

Lowest priority:

```text
default
```

---

# 🔒 Sensitive Variables

Sensitive variables hide values in Terraform output.

Example:

```hcl
variable "db_password" {

  type = string

  sensitive = true

}
```

Usage:

```hcl
password = var.db_password
```

Output:

```text
(sensitive value)
```

---

# 📤 What are Outputs?

Outputs display useful information after infrastructure is created.

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

---

# 💻 Display Outputs

Show all outputs:

```bash
terraform output
```

Specific output:

```bash
terraform output public_ip
```

Example:

```text
54.210.xx.xx
```

---

# 📦 Sensitive Outputs

Example:

```hcl
output "database_password" {

  value = var.db_password

  sensitive = true

}
```

Terraform hides the value.

---

# 🏗️ Example Project

```text
terraform-demo/

├── variables.tf
├── terraform.tfvars
├── outputs.tf
└── main.tf
```

**variables.tf**

```hcl
variable "instance_type" {

  default = "t2.micro"

}
```

**terraform.tfvars**

```hcl
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
output "public_ip" {

  value = aws_instance.web.public_ip

}
```

---

# 💻 Useful Commands

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

Display outputs:

```bash
terraform output
```

Destroy:

```bash
terraform destroy
```

---

# 🏗️ Variable Flow

```text
variables.tf

        │

        ▼

terraform.tfvars

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

- ✅ Keep variables in `variables.tf`
- ✅ Store values in `terraform.tfvars`
- ✅ Use meaningful variable names
- ✅ Avoid hardcoding values
- ✅ Mark secrets as sensitive
- ✅ Use outputs for useful resource information
- ✅ Use different `.tfvars` files for each environment
- ✅ Never commit secrets to Git

---

# ⚠️ Common Mistakes

| Mistake | Solution |
|----------|----------|
| Hardcoding values | Use variables |
| Missing default values | Provide defaults or use tfvars |
| Exposing passwords | Mark variables as sensitive |
| Mixing variables with resources | Keep variables in `variables.tf` |
| Committing secrets | Ignore `.tfvars` files containing secrets |

---

# 📝 Key Takeaways

- Variables make Terraform reusable.
- `terraform.tfvars` stores variable values.
- Variables have different data types.
- Sensitive variables hide confidential values.
- Outputs display resource information after deployment.

---

# 📋 Summary

In this chapter, you learned:

- Variables
- Variable Types
- Variable Files
- Variable Precedence
- Sensitive Variables
- Outputs
- Output Commands

---

# ❓ Interview Questions

## Beginner

1. What is a Terraform Variable?
2. Why are variables used?
3. What is `terraform.tfvars`?
4. What is an Output?
5. How do you display outputs?

---

## Intermediate

6. Explain variable precedence.
7. Difference between variables and outputs?
8. What are sensitive variables?
9. What are variable types?
10. How do you use multiple `.tfvars` files?

---

## Advanced

11. How do variables improve Infrastructure as Code?
12. Explain the Terraform variable loading order.
13. When should outputs be marked sensitive?
14. How would you manage variables across multiple environments?
15. How do modules use variables and outputs?

---

# 🎯 Practice Exercises

## Exercise 1

Create variables.

```hcl
variable "instance_type" {

  default = "t2.micro"

}
```

---

## Exercise 2

Assign values.

```hcl
instance_type = "t3.micro"
```

Save in:

```text
terraform.tfvars
```

---

## Exercise 3

Use the variable.

```hcl
instance_type = var.instance_type
```

---

## Exercise 4

Create outputs.

```hcl
output "instance_id" {

  value = aws_instance.web.id

}
```

---

## Exercise 5

Display outputs.

```bash
terraform output
```

---

# 🧩 Mini Project

Create a reusable Terraform project that provisions:

- One EC2 Instance
- One S3 Bucket

Use:

- `variables.tf`
- `terraform.tfvars`
- `outputs.tf`

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

- Terraform Variables Documentation
- Terraform Outputs Documentation
- Terraform Language Reference
- AWS Provider Documentation
- HashiCorp Learn

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [06 - Terraform State](06-Terraform-State.md) | [Terraform Roadmap](README.md) | [08 - Terraform Functions](08-Terraform-Functions.md) |

---

# 🚀 What's Next?

In **Chapter 08 – Terraform Functions**, you'll learn:

- 🔢 Built-in Functions
- 📝 String Functions
- 📋 Collection Functions
- 🔍 Lookup Functions
- 📅 Date & Time Functions
- 🧮 Mathematical Functions
- 🎯 Real-World Function Examples
