# Terraform Notes
# Chapter 17 - Dependencies

> 📘 **Level:** Intermediate
> ⏱️ **Estimated Reading Time:** 60–75 minutes
> 🛠️ **Practice Time:** 3–4 hours

---

# 📚 Table of Contents

1. Introduction
2. What are Dependencies?
3. Implicit Dependencies
4. Explicit Dependencies
5. The `depends_on` Meta-Argument
6. Dependency Graph
7. Dependency Order
8. Real-World AWS Examples
9. Best Practices
10. Summary
11. Interview Questions
12. Practice Exercises
13. Mini Project
14. Further Reading

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Terraform Dependencies
- Identify implicit dependencies
- Create explicit dependencies
- Use `depends_on`
- Understand Terraform's dependency graph
- Build reliable AWS infrastructure

---

# 📖 Introduction

Infrastructure resources often depend on one another.

For example:

- An EC2 instance requires a subnet.
- A subnet requires a VPC.
- A route table requires an Internet Gateway.
- An Application Load Balancer requires target groups.

Terraform automatically determines the order in which resources should be created using **dependencies**.

---

# 💡 What are Dependencies?

A **dependency** defines the relationship between two or more resources.

Terraform uses dependencies to determine:

- Creation order
- Update order
- Deletion order

Example:

```text
VPC

↓

Subnet

↓

EC2 Instance
```

Terraform creates the VPC first because the subnet depends on it.

---

# 🔗 Implicit Dependencies

An **implicit dependency** is created automatically when one resource references another.

Example:

```hcl
resource "aws_vpc" "main" {

  cidr_block = "10.0.0.0/16"

}

resource "aws_subnet" "public" {

  vpc_id = aws_vpc.main.id

  cidr_block = "10.0.1.0/24"

}
```

Terraform understands:

```text
Subnet

↓

Depends On

↓

VPC
```

No manual configuration is required.

---

# ⚙️ Explicit Dependencies

Sometimes Terraform cannot determine the dependency automatically.

Use `depends_on` to explicitly define it.

Example:

```hcl
resource "aws_instance" "web" {

  ami = "ami-xxxxxxxx"

  instance_type = "t2.micro"

  depends_on = [

    aws_security_group.web

  ]

}
```

Terraform ensures:

```text
Security Group

↓

EC2 Instance
```

---

# 📝 The `depends_on` Meta-Argument

Syntax:

```hcl
depends_on = [

  resource.resource_name

]
```

Example:

```hcl
resource "aws_internet_gateway" "igw" {

  vpc_id = aws_vpc.main.id

}

resource "aws_route_table" "public" {

  vpc_id = aws_vpc.main.id

  depends_on = [

    aws_internet_gateway.igw

  ]

}
```

Terraform creates:

```text
VPC

↓

Internet Gateway

↓

Route Table
```

---

# 🌐 Multiple Dependencies

A resource can depend on multiple resources.

Example:

```hcl
resource "aws_instance" "app" {

  ami = "ami-xxxxxxxx"

  instance_type = "t3.micro"

  depends_on = [

    aws_security_group.web,

    aws_subnet.public

  ]

}
```

Terraform waits until both resources are created.

---

# 📊 Terraform Dependency Graph

Terraform builds a dependency graph before applying changes.

Example:

```text
VPC
 │
 ├────────────┐
 │            │
 ▼            ▼
Subnet     Internet Gateway
 │            │
 └─────┐      │
       ▼      ▼
   Route Table
        │
        ▼
   EC2 Instance
```

Terraform uses this graph to execute operations safely and efficiently.

---

# 🚀 Dependency Order

Terraform follows this sequence:

```text
terraform plan

        │

        ▼

Analyze Resources

        │

        ▼

Build Dependency Graph

        │

        ▼

Create Resources

        │

        ▼

Update State
```

---

# 🌍 Real-World AWS Example

```hcl
resource "aws_vpc" "main" {

  cidr_block = "10.0.0.0/16"

}

resource "aws_subnet" "public" {

  vpc_id = aws_vpc.main.id

  cidr_block = "10.0.1.0/24"

}

resource "aws_security_group" "web" {

  name = "web-sg"

  vpc_id = aws_vpc.main.id

}

resource "aws_instance" "web" {

  ami = "ami-xxxxxxxx"

  instance_type = "t3.micro"

  subnet_id = aws_subnet.public.id

  vpc_security_group_ids = [

    aws_security_group.web.id

  ]

}
```

Terraform automatically creates resources in the correct order.

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

Visualize the dependency graph:

```bash
terraform graph
```

---

# 🏗️ Dependency Workflow

```text
Terraform Code

        │

        ▼

Analyze References

        │

        ▼

Build Dependency Graph

        │

        ▼

Create Resources

        │

        ▼

AWS Infrastructure
```

---

# 🏆 Best Practices

- ✅ Let Terraform infer dependencies whenever possible.
- ✅ Use `depends_on` only when necessary.
- ✅ Avoid unnecessary explicit dependencies.
- ✅ Keep resource references clear.
- ✅ Test dependency changes with `terraform plan`.
- ✅ Use `terraform graph` to visualize complex configurations.

---

# ⚠️ Common Mistakes

| Mistake | Solution |
|----------|----------|
| Overusing `depends_on` | Rely on implicit dependencies when possible |
| Missing explicit dependency | Add `depends_on` when Terraform cannot infer it |
| Hardcoding resource IDs | Reference Terraform resources instead |
| Circular dependencies | Redesign the resource relationships |
| Ignoring the dependency graph | Use `terraform graph` to inspect it |

---

# 📝 Key Takeaways

- Terraform automatically detects most dependencies.
- Resource references create implicit dependencies.
- `depends_on` creates explicit dependencies.
- Terraform builds a dependency graph before execution.
- Correct dependency management ensures predictable deployments.

---

# 📋 Summary

In this chapter, you learned:

- Terraform Dependencies
- Implicit Dependencies
- Explicit Dependencies
- `depends_on`
- Dependency Graph
- Resource Creation Order

---

# ❓ Interview Questions

## Beginner

1. What is a Terraform dependency?
2. What is an implicit dependency?
3. What is an explicit dependency?
4. What is `depends_on`?
5. Why are dependencies important?

---

## Intermediate

6. How does Terraform determine resource order?
7. When should `depends_on` be used?
8. What is the dependency graph?
9. How do implicit dependencies work?
10. How can you visualize dependencies?

---

## Advanced

11. Why should `depends_on` be used sparingly?
12. How does Terraform detect implicit dependencies?
13. What is a circular dependency?
14. How would you troubleshoot dependency issues?
15. How do dependencies improve infrastructure reliability?

---

# 🎯 Practice Exercises

## Exercise 1

Create a VPC.

```hcl
resource "aws_vpc" "main" {

  cidr_block = "10.0.0.0/16"

}
```

---

## Exercise 2

Create a subnet using the VPC.

```hcl
vpc_id = aws_vpc.main.id
```

---

## Exercise 3

Create a Security Group.

```hcl
resource "aws_security_group" "web" {}
```

---

## Exercise 4

Use `depends_on`.

```hcl
depends_on = [

  aws_security_group.web

]
```

---

## Exercise 5

Generate the dependency graph.

```bash
terraform graph
```

---

# 🧩 Mini Project

Create a Terraform project that provisions:

- One VPC
- One Public Subnet
- One Internet Gateway
- One Route Table
- One Security Group
- One EC2 Instance

Ensure dependencies are correctly handled using implicit references and `depends_on` where required.

Project structure:

```text
terraform-dependencies/

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

terraform graph

terraform destroy
```

Verify:

- Resources are created in the correct order.
- `terraform graph` displays the expected dependency relationships.
- Infrastructure is destroyed in reverse dependency order.

---

# 📚 Further Reading

- Terraform Dependency Documentation
- Terraform Meta-Arguments Documentation
- Terraform Graph Documentation
- HashiCorp Learn
- AWS Provider Documentation

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [16 - Locals](16-Locals.md) | [Terraform Roadmap](README.md) | [18 - Lifecycle Meta-Arguments.md](18-Lifecycle-Meta-Arguments.md) |

---

# 🚀 What's Next?

In **Chapter 18 – Lifecycle Meta-Arguments**, you'll learn:

- 🔄 The `lifecycle` Block
- 🛡️ `create_before_destroy`
- 🚫 `prevent_destroy`
- 👀 `ignore_changes`
- 🔁 `replace_triggered_by`
- 🏗️ Production Best Practices
