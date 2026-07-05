# Terraform Notes
# Chapter 22 - Terraform with AWS EC2 (Part 1)

> 📘 **Level:** Intermediate
> ⏱️ **Estimated Reading Time:** 90–120 minutes
> 🛠️ **Practice Time:** 5–6 hours

---

# 📚 Table of Contents

1. Introduction
2. What is Amazon EC2?
3. Why Provision EC2 with Terraform?
4. EC2 Components
5. Creating an EC2 Instance
6. Selecting an AMI
7. Choosing an Instance Type
8. Key Pairs
9. Security Groups
10. User Data
11. Elastic IP
12. EBS Volumes
13. IAM Roles for EC2
14. Production Architecture
15. Real-World Example (Part 2)
16. Best Practices (Part 2)

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Amazon EC2
- Launch EC2 instances using Terraform
- Configure Security Groups
- Use Key Pairs
- Attach IAM Roles
- Configure User Data
- Attach EBS Volumes
- Assign Elastic IPs

---

# 📖 Introduction

Amazon **Elastic Compute Cloud (EC2)** is AWS's virtual server service.

It allows you to launch virtual machines in minutes with different operating systems, CPU, memory, and storage configurations.

Terraform makes EC2 deployments:

- Repeatable
- Automated
- Version Controlled
- Consistent

Instead of manually launching servers from the AWS Console, Terraform allows you to describe your infrastructure as code.

---

# 💡 What is Amazon EC2?

Amazon EC2 is a virtual machine running inside AWS.

Example:

```text
AWS Cloud

       │

       ▼

Amazon EC2

       │

       ├── Ubuntu

       ├── Amazon Linux

       ├── Red Hat

       ├── Windows Server

       └── SUSE Linux
```

EC2 instances can be used for:

- Web Servers
- Application Servers
- Jenkins
- Kubernetes Nodes
- Databases
- CI/CD Runners
- Monitoring Servers

---

# 🎯 Why Use Terraform with EC2?

Benefits:

- Infrastructure as Code
- Automated Provisioning
- Version Control
- Easy Scaling
- Repeatable Deployments
- Consistent Configurations

---

# 🏗️ EC2 Components

A typical EC2 deployment includes:

| Component | Purpose |
|------------|----------|
| EC2 Instance | Virtual Machine |
| AMI | Operating System Image |
| Instance Type | CPU & Memory Configuration |
| Key Pair | SSH Access |
| Security Group | Firewall |
| EBS Volume | Persistent Storage |
| IAM Role | AWS API Access |
| Elastic IP | Static Public IP |

---

# 🖥️ Creating an EC2 Instance

Terraform resource:

```hcl
resource "aws_instance" "web" {

  ami           = "ami-xxxxxxxx"

  instance_type = "t3.micro"

}
```

Deploy:

```bash
terraform apply
```

Terraform creates:

```text
Amazon EC2

↓

t3.micro

↓

Running
```

---

# 📦 Selecting an AMI

AMI stands for **Amazon Machine Image**.

It contains:

- Operating System
- Packages
- Boot Configuration
- Root Volume

Examples:

| OS | Example |
|----|----------|
| Amazon Linux 2023 | AWS Recommended |
| Ubuntu 22.04 | Popular Linux Distribution |
| Red Hat Enterprise Linux | Enterprise Linux |
| Windows Server | Microsoft Workloads |

---

# 💻 Choosing an Instance Type

Instance types determine CPU, memory, and networking performance.

Examples:

| Instance Type | vCPU | Memory |
|---------------|------|--------|
| t2.micro | 1 | 1 GB |
| t3.micro | 2 | 1 GB |
| t3.small | 2 | 2 GB |
| t3.medium | 2 | 4 GB |
| m5.large | 2 | 8 GB |

Choose the instance type based on workload requirements.

---

# 🔑 Creating a Key Pair

Key Pairs are used for SSH access.

Terraform:

```hcl
resource "aws_key_pair" "admin" {

  key_name   = "terraform-key"

  public_key = file("~/.ssh/id_rsa.pub")

}
```

Architecture:

```text
Developer

     │

SSH Key Pair

     │

     ▼

EC2 Instance
```

---

# 🔒 Creating a Security Group

A Security Group acts as a firewall.

Terraform:

```hcl
resource "aws_security_group" "web" {

  name = "web-sg"

  vpc_id = aws_vpc.main.id

  ingress {

    from_port = 22

    to_port = 22

    protocol = "tcp"

    cidr_blocks = ["YOUR_PUBLIC_IP/32"]

  }

  ingress {

    from_port = 80

    to_port = 80

    protocol = "tcp"

    cidr_blocks = ["0.0.0.0/0"]

  }

  egress {

    from_port = 0

    to_port = 0

    protocol = "-1"

    cidr_blocks = ["0.0.0.0/0"]

  }

}
```

Traffic flow:

```text
Internet

      │

Security Group

      │

EC2 Instance
```

---

# 🌐 Launching EC2 in a VPC

```hcl
resource "aws_instance" "web" {

  ami = "ami-xxxxxxxx"

  instance_type = "t3.micro"

  subnet_id = aws_subnet.public_1.id

  vpc_security_group_ids = [

    aws_security_group.web.id

  ]

}
```

The instance launches inside the specified subnet.

---

# 📜 Using User Data

User Data automatically runs commands during instance startup.

Example:

```hcl
resource "aws_instance" "web" {

  ami = "ami-xxxxxxxx"

  instance_type = "t3.micro"

  user_data = <<EOF
#!/bin/bash

yum update -y

yum install httpd -y

systemctl enable httpd

systemctl start httpd
EOF

}
```

This installs and starts the Apache web server automatically.

---

# 🌍 Elastic IP

Elastic IP provides a static public IP.

Terraform:

```hcl
resource "aws_eip" "web" {

  instance = aws_instance.web.id

  domain = "vpc"

}
```

Benefits:

- Static IP address
- Survives instance reboots
- Easy DNS mapping

---

# 💾 Attaching an EBS Volume

Create an EBS volume:

```hcl
resource "aws_ebs_volume" "data" {

  availability_zone = "us-east-1a"

  size = 20

}
```

Attach it:

```hcl
resource "aws_volume_attachment" "data" {

  device_name = "/dev/sdf"

  volume_id = aws_ebs_volume.data.id

  instance_id = aws_instance.web.id

}
```

Architecture:

```text
EC2 Instance

      │

      ▼

20 GB EBS Volume
```

---

# 🔐 IAM Role for EC2

Instead of storing AWS credentials inside the instance, attach an IAM Role.

```hcl
resource "aws_instance" "web" {

  ami = "ami-xxxxxxxx"

  instance_type = "t3.micro"

  iam_instance_profile = aws_iam_instance_profile.profile.name

}
```

Workflow:

```text
EC2 Instance

      │

IAM Role

      │

AWS API Access
```

No Access Keys are required.

---

# 🏗️ Basic EC2 Architecture

```text
                    Internet

                        │

                 Elastic IP

                        │

                Security Group

                        │

                 EC2 Instance

                        │

        ┌───────────────┴───────────────┐

        ▼                               ▼

    EBS Volume                    IAM Role

                        │

                    AWS Services
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

Preview:

```bash
terraform plan
```

Deploy:

```bash
terraform apply
```

Destroy:

```bash
terraform destroy
```

List EC2 instances:

```bash
aws ec2 describe-instances
```

List Key Pairs:

```bash
aws ec2 describe-key-pairs
```

List Security Groups:

```bash
aws ec2 describe-security-groups
```

---

# 📚 End of Part 1

In **Part 2**, you'll learn:

- 🌍 Production EC2 Architecture
- 📦 Launch Templates
- 🚀 Auto Scaling Basics
- 🏷️ EC2 Tagging Strategy
- 🏆 Best Practices
- ⚠️ Common Mistakes
- ❓ Interview Questions
- 🎯 Practice Exercises
- 🧩 Mini Project
- 📖 Navigation to Chapter 23 (Terraform with AWS S3)
