# Terraform Notes
# Chapter 13 - Workspaces

> 📘 **Level:** Intermediate
> ⏱️ **Estimated Reading Time:** 60–75 minutes
> 🛠️ **Practice Time:** 3–4 hours

---

# 📚 Table of Contents

1. Introduction
2. What are Workspaces?
3. Why Use Workspaces?
4. Default Workspace
5. Creating Workspaces
6. Switching Workspaces
7. Listing Workspaces
8. Deleting Workspaces
9. Workspace-Specific Configuration
10. Workspaces and State Files
11. Best Practices
12. Summary
13. Interview Questions
14. Practice Exercises
15. Mini Project
16. Further Reading

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Terraform Workspaces
- Create and manage multiple workspaces
- Isolate Terraform state for different environments
- Use workspace-specific configurations
- Follow workspace best practices

---

# 📖 Introduction

Organizations usually manage multiple environments such as:

- Development
- Testing
- Staging
- Production

Without workspaces, each environment requires a separate state file.

Terraform **Workspaces** allow you to manage multiple state files from the same configuration.

---

# 💡 What are Workspaces?

A **Workspace** is an isolated instance of Terraform state.

Each workspace:

- Uses the same Terraform configuration
- Maintains its own state file
- Can provision separate infrastructure

Example:

```text
Terraform Configuration

        │

        ▼

Workspace: dev

Workspace: test

Workspace: prod

        │

        ▼

Separate State Files
```

---

# 🎯 Why Use Workspaces?

Benefits:

- Separate infrastructure by environment
- Isolated state files
- Reuse the same Terraform code
- Simplified environment management
- Faster deployments

---

# 🏠 Default Workspace

Terraform automatically creates one workspace.

Name:

```text
default
```

Check the current workspace:

```bash
terraform workspace show
```

Output:

```text
default
```

---

# ➕ Creating a Workspace

Create a development workspace.

```bash
terraform workspace new dev
```

Output:

```text
Created and switched to workspace "dev"
```

Create additional workspaces.

```bash
terraform workspace new test

terraform workspace new prod
```

---

# 📋 Listing Workspaces

List all workspaces.

```bash
terraform workspace list
```

Example:

```text
default

* dev

test

prod
```

The `*` indicates the active workspace.

---

# 🔄 Switching Workspaces

Switch to another workspace.

```bash
terraform workspace select prod
```

Output:

```text
Switched to workspace "prod"
```

Verify:

```bash
terraform workspace show
```

Output:

```text
prod
```

---

# ❌ Deleting a Workspace

Delete an unused workspace.

```bash
terraform workspace delete test
```

> **Note:** You cannot delete the currently selected workspace.

Switch first:

```bash
terraform workspace select default
```

Then delete:

```bash
terraform workspace delete test
```

---

# 🏗️ Workspace-Specific Configuration

The active workspace is available using:

```hcl
terraform.workspace
```

Example:

```hcl
locals {

  instance_type = terraform.workspace == "prod"

    ? "t3.large"

    : "t2.micro"

}
```

Use it:

```hcl
resource "aws_instance" "web" {

  ami = "ami-xxxxxxxx"

  instance_type = local.instance_type

}
```

---

# 🏷️ Workspace-Based Naming

Generate unique names.

```hcl
resource "aws_s3_bucket" "demo" {

  bucket = "myapp-${terraform.workspace}-bucket"

}
```

Results:

```text
default → myapp-default-bucket

dev → myapp-dev-bucket

test → myapp-test-bucket

prod → myapp-prod-bucket
```

---

# 📂 Workspaces and State Files

Each workspace has its own state.

Example:

```text
default

↓

terraform.tfstate

----------------------

dev

↓

terraform.tfstate.d/dev/

----------------------

test

↓

terraform.tfstate.d/test/

----------------------

prod

↓

terraform.tfstate.d/prod/
```

When using a remote backend, Terraform stores separate state files for each workspace automatically.

---

# 🌍 Using Workspaces with Remote State

Backend example:

```hcl
terraform {

  backend "s3" {

    bucket = "terraform-state-demo"

    key = "terraform.tfstate"

    region = "ap-south-1"

    dynamodb_table = "terraform-locks"

  }

}
```

Terraform stores:

```text
default

↓

terraform.tfstate

---------------------

dev

↓

env:/dev/terraform.tfstate

---------------------

prod

↓

env:/prod/terraform.tfstate
```

Each workspace has an independent state file.

---

# 💻 Useful Workspace Commands

Show current workspace:

```bash
terraform workspace show
```

List workspaces:

```bash
terraform workspace list
```

Create workspace:

```bash
terraform workspace new dev
```

Switch workspace:

```bash
terraform workspace select prod
```

Delete workspace:

```bash
terraform workspace delete test
```

---

# 🏗️ Workspace Workflow

```text
Terraform Configuration

        │

        ▼

Create Workspace

        │

        ▼

Switch Workspace

        │

        ▼

terraform plan

        │

        ▼

terraform apply

        │

        ▼

Separate State File
```

---

# 🌍 Real-World Example

```hcl
locals {

  instance_types = {

    dev  = "t2.micro"

    test = "t3.small"

    prod = "t3.large"

  }

}

resource "aws_instance" "web" {

  ami = "ami-xxxxxxxx"

  instance_type = local.instance_types[terraform.workspace]

}
```

Results:

| Workspace | Instance Type |
|-----------|---------------|
| dev | t2.micro |
| test | t3.small |
| prod | t3.large |

---

# 🏆 Best Practices

- ✅ Use workspaces for similar environments.
- ✅ Use workspace-based naming.
- ✅ Combine workspaces with remote state.
- ✅ Keep one backend for all workspaces.
- ✅ Avoid storing secrets in variables.
- ✅ Use conditional logic carefully.
- ✅ Use separate IAM permissions for production.

---

# ⚠️ Common Mistakes

| Mistake | Solution |
|----------|----------|
| Deploying in the wrong workspace | Verify using `terraform workspace show` |
| Using workspaces for unrelated projects | Create separate Terraform projects |
| Hardcoding environment names | Use `terraform.workspace` |
| Forgetting state isolation | Remember each workspace has its own state |
| Deleting the active workspace | Switch workspaces first |

---

# 📝 Key Takeaways

- Workspaces isolate Terraform state.
- The default workspace is named `default`.
- Each workspace maintains a separate state file.
- `terraform.workspace` returns the active workspace.
- Workspaces simplify multi-environment deployments.

---

# 📋 Summary

In this chapter, you learned:

- Terraform Workspaces
- Creating Workspaces
- Switching Workspaces
- Workspace Commands
- State Isolation
- Workspace-Based Configuration

---

# ❓ Interview Questions

## Beginner

1. What is a Terraform Workspace?
2. What is the default workspace?
3. How do you create a workspace?
4. How do you switch workspaces?
5. Why use workspaces?

---

## Intermediate

6. Explain workspace state isolation.
7. What is `terraform.workspace`?
8. How are workspaces used with remote state?
9. How do workspaces simplify environment management?
10. When should workspaces be used?

---

## Advanced

11. What are the limitations of Terraform Workspaces?
12. How would you manage production and development environments?
13. Why are workspaces not suitable for unrelated projects?
14. Explain workspace behavior with an S3 backend.
15. How would you design a production-ready workspace strategy?

---

# 🎯 Practice Exercises

## Exercise 1

Show the current workspace.

```bash
terraform workspace show
```

---

## Exercise 2

Create workspaces.

```bash
terraform workspace new dev

terraform workspace new test

terraform workspace new prod
```

---

## Exercise 3

List all workspaces.

```bash
terraform workspace list
```

---

## Exercise 4

Switch to the production workspace.

```bash
terraform workspace select prod
```

---

## Exercise 5

Create an S3 bucket using the workspace name.

```hcl
bucket = "myapp-${terraform.workspace}-bucket"
```

---

# 🧩 Mini Project

Create a Terraform project that provisions AWS resources for multiple environments using workspaces.

Resources:

- EC2 Instance
- S3 Bucket

Workspace names:

- dev
- test
- prod

Project structure:

```text
terraform-workspaces/

├── main.tf
├── providers.tf
├── variables.tf
├── outputs.tf
├── backend.tf
└── versions.tf
```

Run:

```bash
terraform init

terraform workspace new dev

terraform workspace new test

terraform workspace new prod

terraform workspace select dev

terraform apply

terraform workspace select prod

terraform apply
```

Verify:

- Separate state files exist.
- Bucket names include the workspace.
- EC2 instance sizes differ by workspace.

---

# 📚 Further Reading

- Terraform Workspaces Documentation
- Terraform CLI Documentation
- Terraform Backend Documentation
- AWS S3 Backend Documentation
- HashiCorp Learn

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [12 - Backend Configuration](12-Backend-Configuration.md) | [Terraform Roadmap](README.md) | [14 - Provisioners.md](14-Provisioners.md) |

---

# 🚀 What's Next?

In **Chapter 14 – Provisioners**, you'll learn:

- ⚙️ Local-Exec Provisioner
- 🌐 Remote-Exec Provisioner
- 📁 File Provisioner
- 🔑 SSH Connections
- ⚠️ Provisioner Limitations
- 🏗️ Best Practices for Production Infrastructure
