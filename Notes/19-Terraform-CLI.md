# Terraform Notes
# Chapter 19 - Terraform CLI

> 📘 **Level:** Beginner to Intermediate
> ⏱️ **Estimated Reading Time:** 75–90 minutes
> 🛠️ **Practice Time:** 4–5 hours

---

# 📚 Table of Contents

1. Introduction
2. What is the Terraform CLI?
3. Terraform Workflow
4. Common Terraform CLI Commands
5. Terraform Help
6. Working Directory Commands
7. Validation Commands
8. Planning & Applying
9. State Commands
10. Workspace Commands
11. Output Commands
12. Formatting Commands
13. Best Practices
14. Summary
15. Interview Questions
16. Practice Exercises
17. Mini Project
18. Further Reading

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand the Terraform CLI
- Execute Terraform workflows
- Initialize projects
- Validate configurations
- Manage state
- Manage workspaces
- Troubleshoot Terraform commands

---

# 📖 Introduction

The **Terraform Command Line Interface (CLI)** is the primary tool for interacting with Terraform.

Using the CLI, you can:

- Initialize a project
- Download providers
- Validate configurations
- Generate execution plans
- Create infrastructure
- Destroy infrastructure
- Manage state
- Manage workspaces

Every Terraform project is controlled through CLI commands.

---

# 💡 What is Terraform CLI?

Terraform CLI is the command-line application installed on your computer.

Example:

```bash
terraform version
```

Output:

```text
Terraform v1.x.x
```

---

# 🔄 Terraform Workflow

A typical Terraform workflow:

```text
Write Configuration

        │

        ▼

terraform init

        │

        ▼

terraform validate

        │

        ▼

terraform plan

        │

        ▼

terraform apply

        │

        ▼

Infrastructure Created
```

---

# 📦 Initialize a Project

Initialize the working directory.

```bash
terraform init
```

What it does:

- Downloads providers
- Downloads modules
- Configures backend
- Creates `.terraform` directory

Example output:

```text
Terraform has been successfully initialized!
```

---

# ✔️ Validate Configuration

Check syntax and configuration.

```bash
terraform validate
```

Successful output:

```text
Success! The configuration is valid.
```

---

# 🎨 Format Configuration

Automatically format Terraform files.

```bash
terraform fmt
```

Format all files recursively.

```bash
terraform fmt -recursive
```

---

# 📋 Generate an Execution Plan

Preview infrastructure changes.

```bash
terraform plan
```

Terraform compares:

```text
Current State

↓

Desired State

↓

Execution Plan
```

No changes are made during `plan`.

---

# 🚀 Apply Changes

Create or modify infrastructure.

```bash
terraform apply
```

Skip confirmation.

```bash
terraform apply -auto-approve
```

---

# ❌ Destroy Infrastructure

Delete managed resources.

```bash
terraform destroy
```

Without confirmation:

```bash
terraform destroy -auto-approve
```

---

# 📊 Show Current State

Display the current Terraform state.

```bash
terraform show
```

Shows:

- Resources
- Attributes
- Outputs
- State information

---

# 📁 List Resources in State

```bash
terraform state list
```

Example:

```text
aws_vpc.main

aws_subnet.public

aws_instance.web
```

---

# 🔍 Show a Specific Resource

```bash
terraform state show aws_instance.web
```

Output:

```text
id = i-0123456789

instance_type = t3.micro
```

---

# 🗑️ Remove a Resource from State

Remove without deleting it in AWS.

```bash
terraform state rm aws_instance.web
```

The resource remains in AWS but is no longer tracked.

---

# 📥 Pull Remote State

Download the latest state.

```bash
terraform state pull
```

---

# 📤 Push State

Upload a local state file.

```bash
terraform state push terraform.tfstate
```

> Use with caution. This is rarely required.

---

# 🏠 Workspace Commands

Show current workspace.

```bash
terraform workspace show
```

Create workspace.

```bash
terraform workspace new dev
```

Switch workspace.

```bash
terraform workspace select prod
```

List workspaces.

```bash
terraform workspace list
```

Delete workspace.

```bash
terraform workspace delete test
```

---

# 📄 Output Commands

Display all outputs.

```bash
terraform output
```

Display one output.

```bash
terraform output instance_ip
```

Output in JSON.

```bash
terraform output -json
```

---

# ❓ Terraform Help

General help.

```bash
terraform -help
```

Help for a command.

```bash
terraform plan -help
```

---

# 📂 Working Directory Commands

Upgrade providers.

```bash
terraform init -upgrade
```

Reconfigure backend.

```bash
terraform init -reconfigure
```

---

# 💻 Useful Terraform CLI Commands

| Command | Purpose |
|----------|---------|
| terraform version | Show Terraform version |
| terraform init | Initialize project |
| terraform validate | Validate configuration |
| terraform fmt | Format files |
| terraform plan | Preview changes |
| terraform apply | Apply changes |
| terraform destroy | Destroy infrastructure |
| terraform show | Display state |
| terraform output | Show outputs |
| terraform state list | List resources |
| terraform state show | Show resource details |
| terraform workspace list | List workspaces |

---

# 🏗️ CLI Workflow

```text
Write Code

        │

        ▼

terraform fmt

        │

        ▼

terraform validate

        │

        ▼

terraform plan

        │

        ▼

terraform apply

        │

        ▼

terraform output
```

---

# 🌍 Real-World Example

Deploy an EC2 instance.

Initialize:

```bash
terraform init
```

Validate:

```bash
terraform validate
```

Preview:

```bash
terraform plan
```

Deploy:

```bash
terraform apply
```

Verify outputs:

```bash
terraform output
```

Destroy:

```bash
terraform destroy
```

---

# 🏆 Best Practices

- ✅ Run `terraform fmt` before committing code.
- ✅ Always validate configurations.
- ✅ Review `terraform plan` before applying.
- ✅ Store state remotely in production.
- ✅ Use workspaces for environments.
- ✅ Use version control with Terraform files.
- ✅ Keep Terraform CLI updated.

---

# ⚠️ Common Mistakes

| Mistake | Solution |
|----------|----------|
| Skipping `terraform init` | Initialize every new project |
| Applying without reviewing | Always inspect the execution plan |
| Editing state manually | Use `terraform state` commands |
| Ignoring formatting | Run `terraform fmt` |
| Using `-auto-approve` in production | Require manual approval |

---

# 📝 Key Takeaways

- Terraform CLI manages infrastructure.
- `init` prepares the project.
- `validate` checks syntax.
- `plan` previews changes.
- `apply` provisions infrastructure.
- `destroy` removes resources.
- `state` commands manage Terraform state.

---

# 📋 Summary

In this chapter, you learned:

- Terraform CLI
- Terraform Workflow
- State Commands
- Workspace Commands
- Output Commands
- Validation & Formatting

---

# ❓ Interview Questions

## Beginner

1. What is the Terraform CLI?
2. What does `terraform init` do?
3. Why use `terraform plan`?
4. What is `terraform apply`?
5. What does `terraform destroy` do?

---

## Intermediate

6. What is the purpose of `terraform validate`?
7. Explain `terraform fmt`.
8. What are `terraform state` commands?
9. How do workspace commands work?
10. How do you inspect Terraform outputs?

---

## Advanced

11. Why should `terraform plan` always be reviewed?
12. How do you troubleshoot failed CLI commands?
13. When should `terraform state rm` be used?
14. What is the difference between `show` and `output`?
15. How would you automate Terraform CLI commands in a CI/CD pipeline?

---

# 🎯 Practice Exercises

## Exercise 1

Check the Terraform version.

```bash
terraform version
```

---

## Exercise 2

Initialize a project.

```bash
terraform init
```

---

## Exercise 3

Format and validate.

```bash
terraform fmt

terraform validate
```

---

## Exercise 4

Generate a plan and apply it.

```bash
terraform plan

terraform apply
```

---

## Exercise 5

Inspect the state.

```bash
terraform state list

terraform show
```

---

# 🧩 Mini Project

Create a Terraform project that:

- Creates an AWS VPC
- Creates an EC2 instance
- Outputs the public IP
- Uses workspaces for `dev` and `prod`

Project structure:

```text
terraform-cli-demo/

├── main.tf
├── variables.tf
├── outputs.tf
├── providers.tf
├── versions.tf
└── terraform.tfvars
```

Run the following commands:

```bash
terraform init

terraform fmt

terraform validate

terraform plan

terraform apply

terraform output

terraform workspace new dev

terraform workspace list

terraform destroy
```

Verify:

- Infrastructure is created successfully.
- Outputs display the instance details.
- Workspace commands work correctly.
- Resources are destroyed successfully.

---

# 📚 Further Reading

- Terraform CLI Documentation
- Terraform Command Reference
- Terraform State Documentation
- HashiCorp Learn
- AWS Provider Documentation

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [18 - Lifecycle and Meta-Arguments](18-Lifecycle-and-Meta-Arguments.md) | [Terraform Roadmap](README.md) | [20 - Terraform Import.md](20-Terraform-Import.md) |

---

# 🚀 What's Next?

In **Chapter 20 – Terraform Import**, you'll learn:

- 📥 Import existing AWS resources
- 🔄 Bring manually created infrastructure under Terraform management
- 📝 Import blocks (Terraform 1.5+)
- 🏗️ Import EC2, S3, VPC, and Security Groups
- 🎯 Import best practices
