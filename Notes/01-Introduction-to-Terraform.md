# Terraform Notes
# Chapter 01 - Introduction to Terraform

> 📘 **Level:** Beginner
> ⏱️ **Estimated Reading Time:** 45–60 minutes
> 🛠️ **Practice Time:** 2–3 hours

---

# 📚 Table of Contents

1. What is Terraform?
2. What is Infrastructure as Code (IaC)?
3. Why Terraform?
4. Terraform Architecture
5. Terraform Workflow
6. Terraform Features
7. Terraform vs CloudFormation
8. Terraform vs Ansible
9. Terraform Use Cases
10. Terraform Lifecycle
11. Best Practices
12. Summary
13. Interview Questions
14. Practice Exercises
15. Mini Project
16. Further Reading

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Infrastructure as Code (IaC)
- Learn what Terraform is
- Understand Terraform architecture
- Learn the Terraform workflow
- Understand where Terraform fits in DevOps
- Compare Terraform with other IaC tools

---

# 📖 What is Terraform?

**Terraform** is an **Infrastructure as Code (IaC)** tool developed by **HashiCorp**.

It allows you to define, provision, update, and manage infrastructure using simple configuration files written in **HCL (HashiCorp Configuration Language)**.

Instead of manually creating cloud resources through the AWS Console, Terraform automates the process using code.

---

# 💡 What is Infrastructure as Code (IaC)?

Infrastructure as Code (IaC) is the practice of managing infrastructure using configuration files instead of manual processes.

Without IaC:

```text
Login to AWS Console

↓

Create VPC

↓

Create EC2

↓

Create Security Group

↓

Configure Networking

↓

Time Consuming ❌
```

With Terraform:

```text
Write Code

↓

terraform init

↓

terraform plan

↓

terraform apply

↓

Infrastructure Created ✅
```

---

# ❓ Why Use Terraform?

Terraform offers several advantages:

- ✅ Automates infrastructure provisioning
- ✅ Supports multiple cloud providers
- ✅ Version-controlled infrastructure
- ✅ Reusable modules
- ✅ Consistent deployments
- ✅ Easy rollback and updates
- ✅ Infrastructure documentation through code

---

# 🏗️ Terraform Architecture

```text
Terraform Configuration (.tf Files)

               │

               ▼

       Terraform CLI

               │

               ▼

        Terraform Core

               │

               ▼

      Provider Plugins

               │

               ▼

        AWS API Calls

               │

               ▼

      AWS Infrastructure
```

---

# ⚙️ Components of Terraform

## 1. Terraform Core

Responsible for:

- Reading configuration files
- Creating execution plans
- Managing state
- Calling providers

---

## 2. Providers

Providers allow Terraform to interact with external platforms.

Examples:

- AWS
- Azure
- Google Cloud
- Kubernetes
- Docker
- GitHub

Example:

```hcl
provider "aws" {
  region = "us-east-1"
}
```

---

## 3. Resources

Resources represent infrastructure components.

Examples:

- EC2 Instance
- VPC
- S3 Bucket
- IAM User
- Security Group

Example:

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = "my-demo-bucket"
}
```

---

# 🔄 Terraform Workflow

The standard workflow is:

```text
Write Configuration

↓

terraform init

↓

terraform validate

↓

terraform plan

↓

terraform apply

↓

Infrastructure Created

↓

terraform destroy (optional)
```

---

# 💻 Basic Terraform Commands

Initialize Terraform:

```bash
terraform init
```

Validate configuration:

```bash
terraform validate
```

Preview changes:

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

# 🌍 Terraform Use Cases

Terraform is commonly used to provision:

- Amazon EC2
- Amazon S3
- Amazon VPC
- IAM Users & Roles
- RDS Databases
- Load Balancers
- Auto Scaling Groups
- Amazon EKS Clusters
- Route 53
- CloudFront
- Security Groups

---

# ⚖️ Terraform vs AWS CloudFormation

| Terraform | AWS CloudFormation |
|------------|--------------------|
| Multi-cloud support | AWS only |
| Uses HCL | Uses JSON/YAML |
| Open source | AWS managed |
| Large provider ecosystem | Limited to AWS |
| Easy reusable modules | Nested stacks |

---

# ⚖️ Terraform vs Ansible

| Terraform | Ansible |
|------------|----------|
| Infrastructure Provisioning | Configuration Management |
| Declarative | Mostly Procedural |
| Creates infrastructure | Configures servers |
| Uses State File | No State File |
| Cloud provisioning | Software installation & automation |

---

# 🔄 Terraform Lifecycle

```text
Configuration Files

↓

Initialize

↓

Validate

↓

Plan

↓

Apply

↓

State Updated

↓

Modify

↓

Plan

↓

Apply

↓

Destroy (Optional)
```

---

# 🏆 Best Practices

- ✅ Keep infrastructure modular.
- ✅ Store state remotely.
- ✅ Enable state locking.
- ✅ Never hardcode secrets.
- ✅ Use version control.
- ✅ Review `terraform plan` before applying.
- ✅ Use meaningful resource names.
- ✅ Keep Terraform and providers updated.

---

# 🌍 Real-World Example

Suppose you need to create:

- VPC
- Public Subnet
- Internet Gateway
- EC2 Instance
- Security Group

Without Terraform:

- Login to AWS Console
- Configure each resource manually
- Repeat for every environment

With Terraform:

```bash
terraform apply
```

Everything is created automatically and consistently.

---

# 📝 Key Takeaways

- Terraform is an Infrastructure as Code tool.
- Infrastructure is defined using HCL.
- Terraform manages infrastructure through providers.
- Terraform uses state files to track resources.
- Terraform automates cloud infrastructure provisioning.

---

# 📋 Summary

In this chapter, you learned:

- Terraform Overview
- Infrastructure as Code
- Terraform Architecture
- Terraform Workflow
- Providers
- Resources
- Basic Commands
- Best Practices

---

# ❓ Interview Questions

## Beginner

1. What is Terraform?
2. What is Infrastructure as Code?
3. What is a Provider?
4. What is a Resource?
5. What is Terraform State?

---

## Intermediate

6. Explain Terraform architecture.
7. Why is Terraform declarative?
8. Explain the Terraform workflow.
9. Difference between Terraform and CloudFormation?
10. Difference between Terraform and Ansible?

---

## Advanced

11. How does Terraform communicate with AWS?
12. Why is Terraform state important?
13. What happens during `terraform plan`?
14. What happens during `terraform apply`?
15. How would you organize Terraform code for production?

---

# 🎯 Practice Exercises

## Exercise 1

Install Terraform on your system.

---

## Exercise 2

Verify the installed version.

```bash
terraform version
```

---

## Exercise 3

Display Terraform help.

```bash
terraform --help
```

---

## Exercise 4

Create a folder named:

```text
terraform-demo
```

---

## Exercise 5

Create your first Terraform file:

```text
main.tf
```

---

# 🧩 Mini Project

Create a project structure:

```text
terraform-demo/

├── main.tf
├── variables.tf
├── outputs.tf
└── terraform.tfvars
```

Initialize the project:

```bash
terraform init
```

Verify the setup:

```bash
terraform validate
```

---

# 📚 Further Reading

- Terraform Documentation
- HashiCorp Learn
- AWS Provider Documentation
- Terraform Registry

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| README | [Terraform Roadmap](README.md) | [02 - Installing Terraform](02-Installing-Terraform.md) |

---

# 🚀 What's Next?

In **Chapter 02 – Installing Terraform**, you'll learn:

- 💻 Installing Terraform on Windows
- 🐧 Installing Terraform on Linux
- 🍎 Installing Terraform on macOS
- ⚙️ Configuring AWS CLI
- 🔑 Setting Up AWS Credentials
- ✅ Verifying Terraform Installation
