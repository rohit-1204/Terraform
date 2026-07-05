# Terraform Notes
# Chapter 09 - Expressions and Loops

> 📘 **Level:** Beginner to Intermediate
> ⏱️ **Estimated Reading Time:** 75–90 minutes
> 🛠️ **Practice Time:** 4–5 hours

---

# 📚 Table of Contents

1. Introduction
2. Terraform Expressions
3. Conditional Expressions
4. Arithmetic Operators
5. Comparison Operators
6. Logical Operators
7. The `count` Meta-Argument
8. The `for_each` Meta-Argument
9. `for` Expressions
10. Dynamic Blocks
11. Real-World Examples
12. Best Practices
13. Summary
14. Interview Questions
15. Practice Exercises
16. Mini Project
17. Further Reading

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Terraform expressions
- Use conditional logic
- Create multiple resources using `count`
- Manage resources using `for_each`
- Build collections with `for` expressions
- Generate dynamic infrastructure

---

# 📖 Introduction

Terraform expressions make configurations **dynamic** instead of hardcoded.

Instead of writing multiple resource blocks manually, you can:

- Create multiple resources
- Apply conditions
- Iterate through collections
- Generate reusable infrastructure

---

# 💡 What are Expressions?

Expressions are used to calculate or produce values.

Example:

```hcl
instance_type = var.instance_type
```

Terraform evaluates the expression before creating the resource.

Another example:

```hcl
bucket = lower(var.bucket_name)
```

---

# ❓ Conditional Expressions

Syntax:

```hcl
condition ? true_value : false_value
```

Example:

```hcl
instance_type = var.environment == "prod" ? "t3.large" : "t2.micro"
```

If:

```text
environment = prod
```

Output:

```text
t3.large
```

Otherwise:

```text
t2.micro
```

---

# ➕ Arithmetic Operators

| Operator | Example | Result |
|----------|---------|--------|
| + | `5 + 2` | 7 |
| - | `10 - 3` | 7 |
| * | `4 * 5` | 20 |
| / | `20 / 4` | 5 |
| % | `9 % 2` | 1 |

Example:

```hcl
locals {

  total_storage = 20 * 3

}
```

---

# ⚖️ Comparison Operators

| Operator | Meaning |
|-----------|---------|
| == | Equal |
| != | Not Equal |
| > | Greater Than |
| >= | Greater Than or Equal |
| < | Less Than |
| <= | Less Than or Equal |

Example:

```hcl
var.environment == "dev"
```

---

# 🔗 Logical Operators

| Operator | Meaning |
|----------|---------|
| && | AND |
| \|\| | OR |
| ! | NOT |

Example:

```hcl
var.environment == "prod" && var.enable_backup
```

---

# 🔢 Using `count`

The `count` meta-argument creates multiple identical resources.

Example:

```hcl
resource "aws_instance" "web" {

  count = 3

  ami = "ami-xxxxxxxx"

  instance_type = "t2.micro"

}
```

Terraform creates:

```text
aws_instance.web[0]

aws_instance.web[1]

aws_instance.web[2]
```

---

# 📋 Accessing Resources with `count`

Example:

```hcl
output "instance_ids" {

  value = aws_instance.web[*].id

}
```

Specific instance:

```hcl
aws_instance.web[0].public_ip
```

---

# 📦 Using `count` Conditionally

Example:

```hcl
resource "aws_instance" "web" {

  count = var.create_instance ? 1 : 0

  ami = "ami-xxxxxxxx"

  instance_type = "t2.micro"

}
```

If:

```text
create_instance = false
```

Terraform creates:

```text
0 instances
```

---

# 🔁 Using `for_each`

`for_each` creates one resource for each item in a collection.

Example:

```hcl
resource "aws_s3_bucket" "bucket" {

  for_each = toset([

    "dev",

    "test",

    "prod"

  ])

  bucket = "${each.key}-demo-bucket"

}
```

Terraform creates:

```text
dev-demo-bucket

test-demo-bucket

prod-demo-bucket
```

---

# 🗺️ Using `for_each` with Maps

Example:

```hcl
variable "instance_types" {

  default = {

    dev  = "t2.micro"

    test = "t3.small"

    prod = "t3.large"

  }

}
```

Resource:

```hcl
resource "aws_instance" "web" {

  for_each = var.instance_types

  ami = "ami-xxxxxxxx"

  instance_type = each.value

  tags = {

    Environment = each.key

  }

}
```

---

# 🔄 `count` vs `for_each`

| `count` | `for_each` |
|----------|------------|
| Uses numbers | Uses keys |
| Best for identical resources | Best for unique resources |
| Access with index | Access with key |
| Can reorder unexpectedly | Stable resource identities |

Example:

```text
count

↓

web[0]

web[1]

web[2]
```

```text
for_each

↓

web["dev"]

web["test"]

web["prod"]
```

---

# 🧮 `for` Expressions

Create a new collection from an existing one.

Example:

```hcl
locals {

  upper_names = [

    for env in [

      "dev",

      "test",

      "prod"

    ] :

    upper(env)

  ]

}
```

Output:

```text
DEV

TEST

PROD
```

---

# 🗺️ `for` Expression with Maps

Example:

```hcl
locals {

  environments = {

    for key, value in var.instance_types :

    key => upper(value)

  }

}
```

---

# 🏗️ Dynamic Blocks

Dynamic blocks reduce repetitive nested configuration.

Example:

```hcl
dynamic "ingress" {

  for_each = [22, 80, 443]

  content {

    from_port = ingress.value

    to_port   = ingress.value

    protocol  = "tcp"

    cidr_blocks = [

      "0.0.0.0/0"

    ]

  }

}
```

Terraform creates three ingress rules automatically.

---

# 🌍 Real-World Example

Create EC2 instances for multiple environments.

```hcl
variable "servers" {

  default = {

    dev  = "t2.micro"

    test = "t3.small"

    prod = "t3.medium"

  }

}

resource "aws_instance" "server" {

  for_each = var.servers

  ami = "ami-xxxxxxxx"

  instance_type = each.value

  tags = {

    Name = each.key

  }

}
```

Terraform creates:

```text
Development Server

Testing Server

Production Server
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

Destroy:

```bash
terraform destroy
```

Console:

```bash
terraform console
```

---

# 🏗️ Expression Flow

```text
Variables

        │

        ▼

Expressions

        │

        ▼

count / for_each

        │

        ▼

Resources

        │

        ▼

AWS Infrastructure
```

---

# 🏆 Best Practices

- ✅ Use `for_each` for unique resources.
- ✅ Use `count` for identical resources.
- ✅ Keep expressions readable.
- ✅ Use locals for complex logic.
- ✅ Avoid deeply nested expressions.
- ✅ Use descriptive keys with `for_each`.
- ✅ Prefer maps over long conditional statements.

---

# ⚠️ Common Mistakes

| Mistake | Solution |
|----------|----------|
| Using `count` for unique resources | Use `for_each` |
| Hardcoding repeated resources | Use loops |
| Complex inline expressions | Move logic to `locals` |
| Unstable indexing with `count` | Use keyed collections |
| Missing conditions | Use conditional expressions |

---

# 📝 Key Takeaways

- Expressions make Terraform dynamic.
- Conditional expressions simplify decision-making.
- `count` creates identical resources.
- `for_each` creates uniquely identified resources.
- `for` expressions transform collections.
- Dynamic blocks reduce repetitive configuration.

---

# 📋 Summary

In this chapter, you learned:

- Expressions
- Conditional Expressions
- Operators
- `count`
- `for_each`
- `for` Expressions
- Dynamic Blocks

---

# ❓ Interview Questions

## Beginner

1. What is a Terraform expression?
2. What is a conditional expression?
3. What is `count`?
4. What is `for_each`?
5. What is the difference between `count` and `for_each`?

---

## Intermediate

6. When should you use `count`?
7. When should you use `for_each`?
8. Explain `for` expressions.
9. What are dynamic blocks?
10. How do you reference resources created with `for_each`?

---

## Advanced

11. Why is `for_each` preferred over `count` in many production environments?
12. How do dynamic blocks improve Terraform configurations?
13. Explain resource identity with `count` and `for_each`.
14. How would you create resources from a map of objects?
15. How can expressions improve reusable Terraform modules?

---

# 🎯 Practice Exercises

## Exercise 1

Create three EC2 instances using `count`.

```hcl
count = 3
```

---

## Exercise 2

Create three S3 buckets using `for_each`.

```hcl
for_each = toset([

  "dev",

  "test",

  "prod"

])
```

---

## Exercise 3

Use a conditional expression.

```hcl
var.environment == "prod"

? "t3.large"

: "t2.micro"
```

---

## Exercise 4

Transform a list using a `for` expression.

```hcl
[
  for env in [

    "dev",

    "test"

  ] :

  upper(env)
]
```

---

## Exercise 5

Create dynamic ingress rules for:

- Port 22
- Port 80
- Port 443

---

# 🧩 Mini Project

Create a Terraform project that:

- Creates EC2 instances using `for_each`
- Creates S3 buckets using `count`
- Uses conditional expressions for instance types
- Generates security group rules using dynamic blocks

Project structure:

```text
terraform-expressions-demo/

├── main.tf
├── variables.tf
├── locals.tf
├── outputs.tf
└── terraform.tfvars
```

Run:

```bash
terraform init

terraform validate

terraform plan

terraform apply

terraform destroy
```

---

# 📚 Further Reading

- Terraform Expressions Documentation
- Meta-Arguments Documentation
- Dynamic Blocks Documentation
- HashiCorp Learn
- AWS Provider Documentation

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [08 - Terraform Functions](08-Terraform-Functions.md) | [Terraform Roadmap](README.md) | [10 - Terraform Modules](10-Terraform-Modules.md) |

---

# 🚀 What's Next?

In **Chapter 10 – Terraform Modules**, you'll learn:

- 📦 What are Modules?
- 🏗️ Root vs Child Modules
- 🔄 Creating Reusable Modules
- 📁 Module Structure
- 📝 Module Inputs and Outputs
- 🌍 Publishing and Using Modules from the Terraform Registry
