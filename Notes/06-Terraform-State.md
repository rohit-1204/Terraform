# Terraform Notes
# Chapter 06 - Terraform State

> 📘 **Level:** Beginner to Intermediate
> ⏱️ **Estimated Reading Time:** 60–75 minutes
> 🛠️ **Practice Time:** 3–4 hours

---

# 📚 Table of Contents

1. What is Terraform State?
2. Why Terraform Needs State
3. Terraform State File
4. State Workflow
5. Local State
6. Remote State
7. State Commands
8. State File Structure
9. State Locking
10. Refreshing State
11. Best Practices
12. Summary
13. Interview Questions
14. Practice Exercises
15. Mini Project
16. Further Reading

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Terraform State
- Learn how Terraform tracks infrastructure
- Understand Local vs Remote State
- Manage state using Terraform commands
- Understand state locking
- Follow state management best practices

---

# 📖 What is Terraform State?

Terraform stores information about your infrastructure in a **State File**.

The state file maps:

- Resources defined in your Terraform code
- Resources actually created in AWS

Terraform uses this information to determine what changes are required during future deployments.

---

# 🤔 Why Terraform Needs State

Suppose you create an EC2 instance.

Terraform records details such as:

- Instance ID
- Public IP
- Private IP
- Region
- Tags
- Dependencies

Without the state file, Terraform would not know:

- Which resources already exist
- Which resources need updating
- Which resources should be deleted

---

# 💾 Terraform State File

After running:

```bash
terraform apply
```

Terraform creates:

```text
terraform.tfstate
```

Example project:

```text
terraform-demo/

├── main.tf
├── terraform.tfstate
├── terraform.tfstate.backup
└── .terraform/
```

---

# 🏗️ Terraform State Workflow

```text
Terraform Configuration

        │

        ▼

terraform plan

        │

        ▼

Compare with State File

        │

        ▼

AWS Infrastructure

        │

        ▼

terraform apply

        │

        ▼

Update State File
```

---

# 📄 What's Inside the State File?

The state file contains information like:

- Resource IDs
- ARNs
- Public IP addresses
- Private IP addresses
- Metadata
- Dependencies

Example (simplified):

```json
{
  "resources": [
    {
      "type": "aws_instance",
      "name": "web"
    }
  ]
}
```

> ⚠️ Never edit the state file manually.

---

# 🖥️ Local State

By default, Terraform stores the state file locally.

Example:

```text
terraform-demo/

├── terraform.tfstate
```

Advantages:

- Simple setup
- Good for learning
- No additional AWS resources required

Disadvantages:

- Not shared
- Risk of accidental deletion
- No state locking
- Not suitable for teams

---

# ☁️ Remote State

In production, store the state remotely.

A common AWS setup:

```text
Terraform

      │

      ▼

Amazon S3 Bucket

      │

      ▼

State File

      │

      ▼

Amazon DynamoDB

(State Locking)
```

Advantages:

- Shared across team members
- Highly available
- Versioning support
- Secure storage
- State locking

---

# 🔐 State Locking

State locking prevents multiple users from modifying infrastructure simultaneously.

Without locking:

```text
Developer A

        │

        ▼

terraform apply

        ▲

        │

Developer B

terraform apply

❌ Conflict
```

With locking:

```text
Developer A

↓

Lock Acquired

↓

Infrastructure Updated

↓

Lock Released

↓

Developer B Continues
```

AWS commonly uses:

- Amazon S3 → State storage
- Amazon DynamoDB → State locking

---

# 🔄 Refreshing State

Terraform can synchronize the state with real infrastructure.

Command:

```bash
terraform refresh
```

> **Note:** In newer Terraform versions, refreshing is automatically performed during `terraform plan` and `terraform apply`.

---

# 💻 Common State Commands

Show current state:

```bash
terraform show
```

List resources in state:

```bash
terraform state list
```

Show details of a resource:

```bash
terraform state show aws_instance.web
```

Move a resource:

```bash
terraform state mv old_name new_name
```

Remove a resource from state:

```bash
terraform state rm aws_instance.web
```

Pull remote state:

```bash
terraform state pull
```

Push state:

```bash
terraform state push terraform.tfstate
```

---

# 📂 State File Structure

Example:

```text
terraform-demo/

├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
├── terraform.tfstate
├── terraform.tfstate.backup
└── .terraform/
```

Generated automatically:

- `terraform.tfstate`
- `terraform.tfstate.backup`

---

# 🏗️ State Lifecycle

```text
Write Configuration

        │

        ▼

terraform init

        │

        ▼

terraform plan

        │

        ▼

terraform apply

        │

        ▼

Create State File

        │

        ▼

Future Changes

        │

        ▼

Update State File
```

---

# ⚠️ State File Contains Sensitive Data

State files may contain:

- Access Keys
- Passwords
- Database Endpoints
- Resource IDs
- Private IPs
- Secrets

Never commit state files to Git.

Example `.gitignore`:

```text
.terraform/

terraform.tfstate

terraform.tfstate.*

*.tfvars
```

---

# 🏆 Best Practices

- ✅ Store state remotely in Amazon S3.
- ✅ Enable S3 bucket versioning.
- ✅ Use DynamoDB for state locking.
- ✅ Encrypt the state file.
- ✅ Restrict S3 bucket access using IAM.
- ✅ Never edit the state manually.
- ✅ Never commit state files to Git.
- ✅ Back up the state regularly.

---

# ⚠️ Common Mistakes

| Mistake | Solution |
|----------|----------|
| Deleting state file | Restore from backup or remote state |
| Editing state manually | Use Terraform state commands |
| Storing state in Git | Add state files to `.gitignore` |
| No state locking | Use DynamoDB |
| Using local state in production | Use remote backend |

---

# 📝 Key Takeaways

- Terraform State tracks infrastructure.
- State enables Terraform to detect changes.
- Local state is suitable for learning.
- Remote state is recommended for teams.
- State locking prevents concurrent modifications.
- State files may contain sensitive information.

---

# 📋 Summary

In this chapter, you learned:

- Terraform State
- State File
- Local State
- Remote State
- State Locking
- State Commands
- State Best Practices

---

# ❓ Interview Questions

## Beginner

1. What is Terraform State?
2. Why does Terraform use a state file?
3. What is `terraform.tfstate`?
4. What is Local State?
5. What is Remote State?

---

## Intermediate

6. Why is Remote State recommended?
7. What is State Locking?
8. How does DynamoDB help Terraform?
9. Explain `terraform state list`.
10. Why should the state file not be committed to Git?

---

## Advanced

11. What happens if the state file is deleted?
12. How does Terraform detect infrastructure drift?
13. How would you migrate local state to remote state?
14. Explain the lifecycle of a Terraform state file.
15. How would you secure Terraform state in AWS?

---

# 🎯 Practice Exercises

## Exercise 1

Create infrastructure.

```bash
terraform apply
```

---

## Exercise 2

Display the current state.

```bash
terraform show
```

---

## Exercise 3

List resources.

```bash
terraform state list
```

---

## Exercise 4

Show a specific resource.

```bash
terraform state show aws_instance.web
```

---

## Exercise 5

Destroy infrastructure.

```bash
terraform destroy
```

Observe how the state file changes after each operation.

---

# 🧩 Mini Project

Create a Terraform project that provisions:

- One EC2 Instance
- One S3 Bucket

After deployment:

1. View the state file.
2. List all managed resources.
3. Display resource details.
4. Destroy the infrastructure.

Commands:

```bash
terraform init

terraform apply

terraform show

terraform state list

terraform state show aws_instance.web

terraform destroy
```

---

# 📚 Further Reading

- Terraform State Documentation
- Remote State Documentation
- AWS S3 Backend Documentation
- Terraform State Commands
- HashiCorp Learn

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [05 - Terraform Configuration Files](05-Terraform-Configuration-Files.md) | [Terraform Roadmap](README.md) | [07 - Variables and Outputs](07-Variables-and-Outputs.md) |

---

# 🚀 What's Next?

In **Chapter 07 – Variables and Outputs**, you'll learn:

- 📝 Input Variables
- 📤 Output Values
- 🎯 Variable Types
- ⚙️ Default Values
- 🔒 Sensitive Variables
- 💡 Passing Values Between Terraform Configurations
