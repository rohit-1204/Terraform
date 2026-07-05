# Terraform Notes
# Chapter 18 - Lifecycle and Meta-Arguments

> 📘 **Level:** Intermediate
> ⏱️ **Estimated Reading Time:** 80–90 minutes
> 🛠️ **Practice Time:** 4–5 hours

---

# 📚 Table of Contents

1. Introduction
2. What are Meta-Arguments?
3. Common Meta-Arguments
4. What is the Lifecycle Block?
5. Lifecycle Rules
6. `create_before_destroy`
7. `prevent_destroy`
8. `ignore_changes`
9. `replace_triggered_by`
10. Combining Lifecycle Rules
11. Real-World AWS Examples
12. Best Practices
13. Summary
14. Interview Questions
15. Practice Exercises
16. Mini Project
17. Further Reading

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Terraform Meta-Arguments
- Configure the Lifecycle block
- Prevent accidental resource deletion
- Ignore selected infrastructure changes
- Replace resources safely
- Follow production-ready Terraform practices

---

# 📖 Introduction

Terraform resources support **Meta-Arguments**.

Unlike normal arguments (such as `instance_type` or `bucket`), meta-arguments control **how Terraform manages resources**.

Examples include:

- `count`
- `for_each`
- `depends_on`
- `provider`
- `lifecycle`

The most important meta-argument for production deployments is **Lifecycle**.

---

# 💡 What are Meta-Arguments?

Meta-Arguments change Terraform's behavior rather than configuring the resource itself.

Example:

```hcl
resource "aws_instance" "web" {

  count = 2

  ami = "ami-xxxxxxxx"

  instance_type = "t3.micro"

}
```

Here:

- `count` is a meta-argument.
- `ami` and `instance_type` are resource arguments.

---

# 📦 Common Meta-Arguments

| Meta-Argument | Purpose |
|--------------|---------|
| count | Create multiple identical resources |
| for_each | Create multiple unique resources |
| depends_on | Explicit dependency |
| provider | Use a specific provider configuration |
| lifecycle | Control resource lifecycle |

---

# 🔄 What is the Lifecycle Block?

The `lifecycle` block controls how Terraform creates, updates, replaces, and deletes resources.

Syntax:

```hcl
resource "aws_instance" "web" {

  ...

  lifecycle {

  }

}
```

---

# 🛡️ Lifecycle Rules

Terraform supports several lifecycle rules.

| Rule | Purpose |
|------|----------|
| create_before_destroy | Create replacement first |
| prevent_destroy | Prevent accidental deletion |
| ignore_changes | Ignore selected attribute changes |
| replace_triggered_by | Force replacement when another resource changes |

---

# 🚀 create_before_destroy

Normally Terraform destroys an old resource before creating a new one.

Default behavior:

```text
Old EC2

↓

Delete

↓

Create New EC2
```

Using:

```hcl
resource "aws_instance" "web" {

  ami = "ami-xxxxxxxx"

  instance_type = "t3.micro"

  lifecycle {

    create_before_destroy = true

  }

}
```

Terraform changes the order:

```text
Old EC2

↓

Create New EC2

↓

Delete Old EC2
```

Benefits:

- Reduced downtime
- Safer deployments
- Blue/Green style replacement

---

# 🚫 prevent_destroy

Protect critical resources.

Example:

```hcl
resource "aws_s3_bucket" "logs" {

  bucket = "company-log-bucket"

  lifecycle {

    prevent_destroy = true

  }

}
```

Now if someone runs:

```bash
terraform destroy
```

Terraform returns:

```text
Error:

Resource cannot be destroyed.
```

Useful for:

- Production databases
- Log buckets
- State buckets
- Critical networking

---

# 👀 ignore_changes

Ignore changes made outside Terraform.

Example:

```hcl
resource "aws_instance" "web" {

  ami = "ami-xxxxxxxx"

  instance_type = "t3.micro"

  tags = {

    Name = "WebServer"

  }

  lifecycle {

    ignore_changes = [

      tags

    ]

  }

}
```

If someone changes tags manually in AWS:

```text
Terraform

↓

No changes detected
```

Common uses:

- Tags updated by automation
- Auto Scaling desired capacity
- External management systems

---

# 🔁 replace_triggered_by

Force resource replacement when another resource changes.

Example:

```hcl
resource "aws_instance" "web" {

  ami = data.aws_ami.latest.id

  instance_type = "t3.micro"

  lifecycle {

    replace_triggered_by = [

      data.aws_ami.latest.id

    ]

  }

}
```

Whenever the AMI changes:

```text
New AMI

↓

Replace EC2 Instance
```

Useful when:

- New AMIs are released
- Launch templates change
- Configuration files require recreation

---

# 🏗️ Combining Lifecycle Rules

Example:

```hcl
resource "aws_instance" "web" {

  ami = "ami-xxxxxxxx"

  instance_type = "t3.micro"

  lifecycle {

    create_before_destroy = true

    ignore_changes = [

      tags

    ]

  }

}
```

Terraform:

- Creates a replacement first.
- Ignores tag modifications.

---

# 🌍 Real-World AWS Example

```hcl
resource "aws_db_instance" "database" {

  allocated_storage = 20

  engine = "mysql"

  instance_class = "db.t3.micro"

  username = "admin"

  password = "example-password"

  skip_final_snapshot = true

  lifecycle {

    prevent_destroy = true

  }

}
```

Accidental deletion is blocked.

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

Show state:

```bash
terraform show
```

---

# 🏗️ Lifecycle Workflow

```text
Terraform Plan

        │

        ▼

Read Lifecycle Rules

        │

        ▼

Determine Action

        │

        ▼

Create / Update / Replace

        │

        ▼

Update State
```

---

# 🏆 Best Practices

- ✅ Use `create_before_destroy` for production services.
- ✅ Protect critical resources with `prevent_destroy`.
- ✅ Use `ignore_changes` only when necessary.
- ✅ Avoid ignoring too many attributes.
- ✅ Test lifecycle behavior with `terraform plan`.
- ✅ Document lifecycle rules for team members.

---

# ⚠️ Common Mistakes

| Mistake | Solution |
|----------|----------|
| Ignoring all attributes | Ignore only required fields |
| Forgetting `prevent_destroy` | Protect critical resources |
| Overusing lifecycle rules | Use only when necessary |
| Unexpected replacements | Review `terraform plan` carefully |
| Relying on lifecycle for application deployment | Use CI/CD tools for deployments |

---

# 📝 Key Takeaways

- Meta-Arguments control Terraform behavior.
- Lifecycle rules improve deployment safety.
- `create_before_destroy` reduces downtime.
- `prevent_destroy` protects important resources.
- `ignore_changes` avoids unnecessary updates.
- `replace_triggered_by` automates resource replacement.

---

# 📋 Summary

In this chapter, you learned:

- Meta-Arguments
- Lifecycle Block
- create_before_destroy
- prevent_destroy
- ignore_changes
- replace_triggered_by

---

# ❓ Interview Questions

## Beginner

1. What are Terraform Meta-Arguments?
2. What is the Lifecycle block?
3. What does `prevent_destroy` do?
4. What is `ignore_changes`?
5. What is `create_before_destroy`?

---

## Intermediate

6. When should `prevent_destroy` be used?
7. How does `ignore_changes` work?
8. What is `replace_triggered_by`?
9. Why use lifecycle rules?
10. How do lifecycle rules affect deployments?

---

## Advanced

11. How would you protect production resources?
12. Why should `ignore_changes` be used carefully?
13. How does Terraform replace resources safely?
14. Explain lifecycle behavior during updates.
15. What lifecycle rules are commonly used in production?

---

# 🎯 Practice Exercises

## Exercise 1

Enable `create_before_destroy`.

```hcl
lifecycle {

  create_before_destroy = true

}
```

---

## Exercise 2

Prevent accidental deletion.

```hcl
lifecycle {

  prevent_destroy = true

}
```

---

## Exercise 3

Ignore tag changes.

```hcl
lifecycle {

  ignore_changes = [

    tags

  ]

}
```

---

## Exercise 4

Force replacement after AMI changes.

```hcl
replace_triggered_by = [

  data.aws_ami.latest.id

]
```

---

## Exercise 5

Run:

```bash
terraform plan
```

Observe lifecycle behavior before applying.

---

# 🧩 Mini Project

Create a Terraform project that provisions:

- EC2 Instance
- S3 Bucket
- RDS Database

Configure:

- `create_before_destroy` for EC2
- `prevent_destroy` for RDS
- `ignore_changes` for S3 tags

Project structure:

```text
terraform-lifecycle/

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
```

Verify:

- EC2 replacements occur without downtime.
- RDS deletion is blocked.
- Tag updates on S3 are ignored.

---

# 📚 Further Reading

- Terraform Lifecycle Documentation
- Terraform Meta-Arguments Documentation
- Terraform Language Documentation
- HashiCorp Learn
- AWS Provider Documentation

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [17 - Dependencies](17-Dependencies.md) | [Terraform Roadmap](README.md) | [19 - Terraform Import.md](19-Terraform-Import.md) |

---

# 🚀 What's Next?

In **Chapter 19 – Terraform Import**, you'll learn:

- 📥 What is Terraform Import?
- 🔄 Import Existing AWS Resources
- 📝 Import Blocks (Terraform 1.5+)
- ⚙️ Importing EC2, S3, and VPC Resources
- 🎯 Import Best Practices
