# Terraform Notes
# Chapter 15 - Data Sources

> 📘 **Level:** Intermediate
> ⏱️ **Estimated Reading Time:** 70–90 minutes
> 🛠️ **Practice Time:** 4–5 hours

---

# 📚 Table of Contents

1. Introduction
2. What are Data Sources?
3. Why Use Data Sources?
4. Data Source Syntax
5. Reading Existing AWS Resources
6. Common AWS Data Sources
7. Using Data Sources with Resources
8. Filtering Data Sources
9. Data Sources vs Resources
10. Best Practices
11. Summary
12. Interview Questions
13. Practice Exercises
14. Mini Project
15. Further Reading

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Terraform Data Sources
- Read existing AWS resources
- Use data sources with resources
- Filter AWS resources
- Build reusable Terraform configurations
- Follow best practices

---

# 📖 Introduction

Terraform is commonly used to **create** infrastructure.

However, many times you need to **use existing infrastructure** instead of creating everything from scratch.

Examples:

- Existing VPC
- Existing Subnet
- Existing Security Group
- Existing AMI
- Existing Availability Zones

Terraform uses **Data Sources** to read existing resources.

---

# 💡 What are Data Sources?

A **Data Source** allows Terraform to retrieve information about infrastructure that already exists.

Unlike a resource:

- A **resource** creates or manages infrastructure.
- A **data source** only reads information.

---

# 🎯 Why Use Data Sources?

Benefits:

- Reuse existing AWS resources
- Avoid hardcoding IDs
- Improve portability
- Simplify infrastructure
- Reduce duplication

---

# 📝 Data Source Syntax

General syntax:

```hcl
data "<provider>_<resource>" "<name>" {

  ...

}
```

Example:

```hcl
data "aws_region" "current" {}
```

Use the value:

```hcl
data.aws_region.current.name
```

---

# 📦 Reading the Current AWS Region

Example:

```hcl
data "aws_region" "current" {}
```

Output:

```hcl
output "current_region" {

  value = data.aws_region.current.name

}
```

---

# 🌍 Reading AWS Availability Zones

```hcl
data "aws_availability_zones" "available" {

  state = "available"

}
```

Output:

```hcl
output "zones" {

  value = data.aws_availability_zones.available.names

}
```

Example output:

```text
[
  "ap-south-1a",
  "ap-south-1b",
  "ap-south-1c"
]
```

---

# 🖥️ Reading an Existing AMI

```hcl
data "aws_ami" "amazon_linux" {

  most_recent = true

  owners = ["amazon"]

  filter {

    name = "name"

    values = [

      "al2023-ami-*"

    ]

  }

}
```

Use it:

```hcl
resource "aws_instance" "web" {

  ami = data.aws_ami.amazon_linux.id

  instance_type = "t2.micro"

}
```

---

# 🌐 Reading an Existing VPC

```hcl
data "aws_vpc" "main" {

  filter {

    name = "tag:Name"

    values = [

      "production-vpc"

    ]

  }

}
```

Output:

```hcl
output "vpc_id" {

  value = data.aws_vpc.main.id

}
```

---

# 📡 Reading Existing Subnets

```hcl
data "aws_subnets" "private" {

  filter {

    name = "vpc-id"

    values = [

      data.aws_vpc.main.id

    ]

  }

}
```

---

# 🔐 Reading a Security Group

```hcl
data "aws_security_group" "web" {

  filter {

    name = "group-name"

    values = [

      "web-sg"

    ]

  }

}
```

Use:

```hcl
vpc_security_group_ids = [

  data.aws_security_group.web.id

]
```

---

# 🌍 Reading AWS Caller Identity

Useful for obtaining the current AWS Account ID.

```hcl
data "aws_caller_identity" "current" {}
```

Outputs:

```hcl
output "account_id" {

  value = data.aws_caller_identity.current.account_id

}
```

---

# 🏗️ Using Data Sources with Resources

Example:

```hcl
data "aws_ami" "amazon_linux" {

  most_recent = true

  owners = ["amazon"]

}

resource "aws_instance" "web" {

  ami = data.aws_ami.amazon_linux.id

  instance_type = "t2.micro"

}
```

Terraform:

```text
Read AMI

      │

      ▼

Create EC2 Instance
```

---

# 🔍 Filtering Data Sources

Example:

```hcl
data "aws_subnets" "private" {

  filter {

    name = "tag:Environment"

    values = [

      "production"

    ]

  }

}
```

Multiple filters:

```hcl
filter {

  name = "vpc-id"

  values = [

    "vpc-123456"

  ]

}

filter {

  name = "availability-zone"

  values = [

    "ap-south-1a"

  ]

}
```

---

# ⚖️ Data Sources vs Resources

| Data Source | Resource |
|--------------|----------|
| Reads infrastructure | Creates infrastructure |
| Uses `data` block | Uses `resource` block |
| Read-only | Managed by Terraform |
| Existing resources | New resources |

Example:

```hcl
data "aws_vpc" "main" {}
```

vs

```hcl
resource "aws_vpc" "main" {}
```

---

# 🌍 Real-World Example

Launch an EC2 instance inside an existing VPC.

```hcl
data "aws_vpc" "main" {

  filter {

    name = "tag:Name"

    values = [

      "production-vpc"

    ]

  }

}

data "aws_subnets" "private" {

  filter {

    name = "vpc-id"

    values = [

      data.aws_vpc.main.id

    ]

  }

}

data "aws_ami" "amazon_linux" {

  most_recent = true

  owners = ["amazon"]

}

resource "aws_instance" "app" {

  ami = data.aws_ami.amazon_linux.id

  subnet_id = data.aws_subnets.private.ids[0]

  instance_type = "t3.micro"

}
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

Display outputs:

```bash
terraform output
```

---

# 🏗️ Data Source Workflow

```text
Terraform Configuration

        │

        ▼

Read Existing Resources

        │

        ▼

Retrieve IDs

        │

        ▼

Create New Resources

        │

        ▼

AWS Infrastructure
```

---

# 🏆 Best Practices

- ✅ Use data sources instead of hardcoding IDs.
- ✅ Filter resources using tags.
- ✅ Use descriptive data source names.
- ✅ Reuse existing infrastructure.
- ✅ Keep filters simple and accurate.
- ✅ Validate assumptions with outputs during development.
- ✅ Prefer tags over fixed resource IDs.

---

# ⚠️ Common Mistakes

| Mistake | Solution |
|----------|----------|
| Hardcoding AMI IDs | Use `aws_ami` data source |
| Hardcoding VPC IDs | Read the existing VPC |
| Using incorrect filters | Verify resource tags |
| Assuming a single result | Ensure filters return the expected resources |
| Confusing data sources with resources | Remember data sources are read-only |

---

# 📝 Key Takeaways

- Data sources retrieve information about existing infrastructure.
- Data sources never create resources.
- Filters help locate the correct AWS resources.
- Data sources improve reusable Terraform code.
- Existing infrastructure can be combined with newly created resources.

---

# 📋 Summary

In this chapter, you learned:

- Terraform Data Sources
- Reading Existing AWS Resources
- Filtering Resources
- Using Data Sources with Resources
- Data Sources vs Resources

---

# ❓ Interview Questions

## Beginner

1. What is a Terraform Data Source?
2. What is the difference between a data source and a resource?
3. Why use data sources?
4. How do you read the current AWS region?
5. How do you retrieve an existing AMI?

---

## Intermediate

6. How do filters work in data sources?
7. What is `aws_caller_identity` used for?
8. Why should tags be used with data sources?
9. How do you use a data source in a resource?
10. What happens if a data source returns no matching resource?

---

## Advanced

11. How would you design reusable Terraform code using data sources?
12. Why are data sources preferred over hardcoded resource IDs?
13. How would you deploy resources into an existing VPC?
14. Explain the relationship between data sources and modules.
15. How would you troubleshoot data source lookup failures?

---

# 🎯 Practice Exercises

## Exercise 1

Read the current AWS region.

```hcl
data "aws_region" "current" {}
```

---

## Exercise 2

Retrieve available Availability Zones.

```hcl
data "aws_availability_zones" "available" {}
```

---

## Exercise 3

Find the latest Amazon Linux AMI.

```hcl
data "aws_ami" "amazon_linux" {

  most_recent = true

}
```

---

## Exercise 4

Read an existing VPC.

```hcl
data "aws_vpc" "main" {}
```

---

## Exercise 5

Deploy an EC2 instance using an existing AMI and subnet.

---

# 🧩 Mini Project

Create a Terraform project that:

- Reads an existing VPC
- Reads existing private subnets
- Reads the latest Amazon Linux AMI
- Creates an EC2 instance using those resources
- Outputs the VPC ID, Subnet ID, and Instance ID

Project structure:

```text
terraform-data-sources/

├── main.tf
├── providers.tf
├── variables.tf
├── outputs.tf
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

- Terraform Data Sources Documentation
- AWS Provider Documentation
- Terraform Language Documentation
- HashiCorp Learn
- AWS EC2 Documentation

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [14 - Provisioners](14-Provisioners.md) | [Terraform Roadmap](README.md) | [16 - Locals.md](16-Locals.md) |

---

# 🚀 What's Next?

In **Chapter 16 – Locals**, you'll learn:

- 🏷️ What are Local Values?
- 🧮 Creating Reusable Expressions
- 🔄 Simplifying Terraform Code
- 📦 Combining Variables, Functions, and Locals
- 🎯 Real-World AWS Examples
