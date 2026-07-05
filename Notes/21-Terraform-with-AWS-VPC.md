
# Terraform Notes
# Chapter 21 - Terraform with AWS VPC (Part 1)

> 📘 **Level:** Intermediate
> ⏱️ **Estimated Reading Time:** 90–120 minutes
> 🛠️ **Practice Time:** 5–6 hours

---

# 📚 Table of Contents

1. Introduction
2. What is Amazon VPC?
3. Why Use Terraform with VPC?
4. VPC Components
5. Creating a VPC
6. Creating Subnets
7. Internet Gateway
8. Route Tables
9. Route Table Associations
10. Elastic IP
11. NAT Gateway
12. Public vs Private Subnets
13. VPC Architecture
14. Real-World Example (Part 2)
15. Best Practices (Part 2)

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Amazon VPC fundamentals
- Create a VPC using Terraform
- Configure Public and Private Subnets
- Create Internet Gateway
- Configure Route Tables
- Deploy a NAT Gateway
- Understand Production VPC Architecture

---

# 📖 Introduction

Amazon **Virtual Private Cloud (VPC)** allows you to create an isolated virtual network inside AWS.

Every AWS resource such as:

- EC2
- RDS
- EKS
- Lambda (inside VPC)
- ECS

runs inside a VPC.

Terraform makes VPC creation repeatable, scalable, and version-controlled.

---

# 🌐 What is Amazon VPC?

A VPC is your private network in AWS.

Think of it as your own data center in the cloud.

Example:

```text
AWS Cloud

┌──────────────────────────────┐

        Amazon VPC

 ┌──────────────────────────┐

   EC2

   RDS

   ALB

   EKS

 └──────────────────────────┘

└──────────────────────────────┘
```

A VPC provides:

- Private IP addresses
- Routing
- Internet connectivity
- Security boundaries

---

# 🎯 Why Use Terraform with VPC?

Benefits:

- Infrastructure as Code
- Repeatable Deployments
- Version Control
- Automation
- Consistent Networking
- Easier Disaster Recovery

---

# 🏗️ VPC Components

A production VPC usually contains:

| Component | Purpose |
|------------|----------|
| VPC | Virtual Network |
| Public Subnet | Internet-facing resources |
| Private Subnet | Internal resources |
| Internet Gateway | Internet access |
| NAT Gateway | Internet for private subnet |
| Route Table | Controls routing |
| Security Group | Instance firewall |
| Network ACL | Subnet firewall |

---

# 🏠 Creating a VPC

Terraform resource:

```hcl
resource "aws_vpc" "main" {

  cidr_block = "10.0.0.0/16"

  enable_dns_support = true

  enable_dns_hostnames = true

  tags = {

    Name = "Production-VPC"

  }

}
```

Deploy:

```bash
terraform apply
```

Terraform creates:

```text
Amazon VPC

↓

10.0.0.0/16
```

---

# 📌 Understanding CIDR Blocks

CIDR defines the IP range of your VPC.

Example:

```text
10.0.0.0/16
```

Available IPs:

```text
10.0.0.1

↓

10.0.255.254
```

Common VPC CIDRs:

| CIDR | IP Addresses |
|-------|--------------|
| /16 | 65,536 |
| /20 | 4,096 |
| /24 | 256 |

---

# 🌍 Availability Zones

A production VPC should span multiple Availability Zones.

Example:

```text
us-east-1a

us-east-1b

us-east-1c
```

Benefits:

- High Availability
- Fault Tolerance
- Disaster Recovery

---

# 🌐 Creating Public Subnets

Example:

```hcl
resource "aws_subnet" "public_1" {

  vpc_id = aws_vpc.main.id

  cidr_block = "10.0.1.0/24"

  availability_zone = "us-east-1a"

  map_public_ip_on_launch = true

  tags = {

    Name = "Public-Subnet-1"

  }

}
```

---

Second subnet:

```hcl
resource "aws_subnet" "public_2" {

  vpc_id = aws_vpc.main.id

  cidr_block = "10.0.2.0/24"

  availability_zone = "us-east-1b"

  map_public_ip_on_launch = true

}
```

Architecture:

```text
VPC

├── Public Subnet 1

└── Public Subnet 2
```

---

# 🔒 Creating Private Subnets

```hcl
resource "aws_subnet" "private_1" {

  vpc_id = aws_vpc.main.id

  cidr_block = "10.0.10.0/24"

  availability_zone = "us-east-1a"

}
```

Another private subnet:

```hcl
resource "aws_subnet" "private_2" {

  vpc_id = aws_vpc.main.id

  cidr_block = "10.0.11.0/24"

  availability_zone = "us-east-1b"

}
```

Architecture:

```text
VPC

├── Public Subnet

└── Private Subnet
```

---

# 🌍 Internet Gateway

Internet Gateway (IGW) enables internet access.

Terraform:

```hcl
resource "aws_internet_gateway" "igw" {

  vpc_id = aws_vpc.main.id

  tags = {

    Name = "Production-IGW"

  }

}
```

Workflow:

```text
Internet

↓

Internet Gateway

↓

Public Subnet
```

---

# 🛣️ Route Tables

Route Tables define where traffic goes.

Terraform:

```hcl
resource "aws_route_table" "public" {

  vpc_id = aws_vpc.main.id

}
```

Create a route:

```hcl
resource "aws_route" "internet" {

  route_table_id = aws_route_table.public.id

  destination_cidr_block = "0.0.0.0/0"

  gateway_id = aws_internet_gateway.igw.id

}
```

Meaning:

```text
All Internet Traffic

↓

Internet Gateway
```

---

# 🔗 Associate Route Table

Attach the route table to a subnet.

```hcl
resource "aws_route_table_association" "public" {

  subnet_id = aws_subnet.public_1.id

  route_table_id = aws_route_table.public.id

}
```

Now the subnet has internet connectivity.

---

# 🌐 Elastic IP

A NAT Gateway requires an Elastic IP.

Terraform:

```hcl
resource "aws_eip" "nat" {

  domain = "vpc"

}
```

AWS allocates a static public IP.

---

# 🚪 NAT Gateway

Private subnets cannot access the internet directly.

Use a NAT Gateway.

Terraform:

```hcl
resource "aws_nat_gateway" "nat" {

  allocation_id = aws_eip.nat.id

  subnet_id = aws_subnet.public_1.id

  tags = {

    Name = "Production-NAT"

  }

}
```

Workflow:

```text
Private Subnet

↓

NAT Gateway

↓

Internet Gateway

↓

Internet
```

---

# 🌍 Public vs Private Subnets

| Public Subnet | Private Subnet |
|---------------|----------------|
| Has Internet Access | No Direct Internet |
| Hosts ALB | Hosts Databases |
| Hosts Bastion | Hosts Backend APIs |
| Public IP | Private IP |
| Route via IGW | Route via NAT Gateway |

---

# 🏗️ Basic VPC Architecture

```text
                     Internet

                         │

                Internet Gateway

                         │

        ┌────────────────┴────────────────┐

        ▼                                 ▼

 Public Subnet A                   Public Subnet B

        │                                 │

      ALB                              Bastion

        │                                 │

──────────── Private Network ─────────────

        │                                 │

 Private Subnet A                 Private Subnet B

        │                                 │

      EC2                             RDS

                 NAT Gateway
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

List VPCs:

```bash
aws ec2 describe-vpcs
```

List Subnets:

```bash
aws ec2 describe-subnets
```

List Route Tables:

```bash
aws ec2 describe-route-tables
```

---

# 📚 End of Part 1

In **Part 2**, you'll learn:

- 🌍 Complete Production VPC Deployment
- 🔒 Security Groups
- 🚧 Network ACLs
- 🏗️ Multi-AZ Architecture
- ⚠️ Common Mistakes
- 🏆 Best Practices
- ❓ Interview Questions
- 🎯 Practice Exercises
- 🧩 Mini Project
- 📖 Navigation to Chapter 22 (Terraform with AWS EC2)

# Terraform Notes
# Chapter 21 - Terraform with AWS VPC (Part 2)

> 📘 **Continuation of Chapter 21**

---

# 🌍 Real-World Production VPC

A production VPC usually spans **multiple Availability Zones (AZs)** for high availability and fault tolerance.

Architecture:

```text
                           Internet

                               │

                     Internet Gateway (IGW)

                               │

       ┌───────────────────────┴───────────────────────┐

       ▼                                               ▼

 Public Subnet (AZ-1)                          Public Subnet (AZ-2)

       │                                               │

       ALB                                        NAT Gateway

       │                                               │

────────────── Private Network (VPC) ─────────────────────────

       │                                               │

 Private App Subnet                          Private App Subnet

       │                                               │

      EC2                                           EC2

       │                                               │

────────────── Database Layer ────────────────────────────────

       ▼                                               ▼

 Private DB Subnet                           Private DB Subnet

       │                                               │

                     Amazon RDS (Multi-AZ)
```

Advantages:

- High Availability
- Fault Tolerance
- Scalability
- Better Security

---

# 🔒 Security Groups

A **Security Group** acts as a virtual firewall for EC2 instances and other AWS resources.

Terraform:

```hcl
resource "aws_security_group" "web" {

  name        = "web-sg"

  description = "Allow HTTP and SSH"

  vpc_id      = aws_vpc.main.id

  ingress {

    from_port   = 80

    to_port     = 80

    protocol    = "tcp"

    cidr_blocks = ["0.0.0.0/0"]

  }

  ingress {

    from_port   = 22

    to_port     = 22

    protocol    = "tcp"

    cidr_blocks = ["YOUR_PUBLIC_IP/32"]

  }

  egress {

    from_port   = 0

    to_port     = 0

    protocol    = "-1"

    cidr_blocks = ["0.0.0.0/0"]

  }

  tags = {

    Name = "Web-Security-Group"

  }

}
```

Security Group flow:

```text
Internet

     │

     ▼

Security Group

     │

     ▼

EC2 Instance
```

---

# 🚧 Network ACLs (NACLs)

Network ACLs provide security at the **subnet level**.

Terraform:

```hcl
resource "aws_network_acl" "main" {

  vpc_id = aws_vpc.main.id

  subnet_ids = [

    aws_subnet.public_1.id

  ]

  ingress {

    protocol   = "tcp"

    rule_no    = 100

    action     = "allow"

    cidr_block = "0.0.0.0/0"

    from_port  = 80

    to_port    = 80

  }

  egress {

    protocol   = "-1"

    rule_no    = 100

    action     = "allow"

    cidr_block = "0.0.0.0/0"

    from_port  = 0

    to_port    = 0

  }

}
```

---

# 🔄 Security Group vs Network ACL

| Security Group | Network ACL |
|---------------|-------------|
| Instance level | Subnet level |
| Stateful | Stateless |
| Supports Allow rules only | Supports Allow and Deny rules |
| Applied to ENIs | Applied to Subnets |
| Easier to manage | More granular control |

---

# 🌐 Multi-AZ VPC Design

A production VPC should use multiple Availability Zones.

Example:

```text
VPC

├── AZ-1

│   ├── Public Subnet

│   ├── Private App Subnet

│   └── Private DB Subnet

│

└── AZ-2

    ├── Public Subnet

    ├── Private App Subnet

    └── Private DB Subnet
```

Benefits:

- Higher Availability
- Automatic Failover
- Better Load Distribution

---

# 🌍 Complete Production Architecture

```text
                          Internet

                              │

                     Internet Gateway

                              │

                 Application Load Balancer

                              │

        ┌─────────────────────┴─────────────────────┐

        ▼                                           ▼

   EC2 Instance 1                             EC2 Instance 2

        │                                           │

────────────── Private Application Tier ──────────────

        │                                           │

     Security Groups                        Security Groups

        │                                           │

────────────── Database Tier ─────────────────────────

                      Amazon RDS

                    (Multi-AZ Deployment)
```

---

# 💼 Real-World Deployment Scenario

Imagine deploying an e-commerce application.

Infrastructure:

- 1 VPC
- 2 Public Subnets
- 2 Private App Subnets
- 2 Private Database Subnets
- Internet Gateway
- NAT Gateway
- Application Load Balancer
- EC2 Auto Scaling Group
- Amazon RDS (Multi-AZ)

Traffic flow:

```text
User

↓

Internet

↓

Application Load Balancer

↓

EC2 Instances

↓

Amazon RDS
```

---

# 🏆 Best Practices

- ✅ Use a `/16` CIDR block for production VPCs.
- ✅ Spread resources across multiple Availability Zones.
- ✅ Place databases in private subnets.
- ✅ Place Load Balancers in public subnets.
- ✅ Use NAT Gateway for outbound internet access from private subnets.
- ✅ Enable DNS Hostnames and DNS Resolution.
- ✅ Use Security Groups as the primary firewall.
- ✅ Apply the Principle of Least Privilege.
- ✅ Tag all VPC resources.
- ✅ Use Terraform modules for reusable VPC configurations.

---

# ⚠️ Common Mistakes

| Mistake | Solution |
|----------|----------|
| Deploying databases in public subnets | Always use private subnets |
| Using one Availability Zone | Use Multi-AZ architecture |
| Opening SSH to `0.0.0.0/0` | Restrict SSH to trusted IPs |
| Missing route table associations | Associate every subnet correctly |
| No NAT Gateway | Private instances cannot access updates |
| Forgetting Internet Gateway | Public subnets lose internet connectivity |
| Not tagging resources | Use consistent tagging |

---

# 💻 Useful AWS CLI Commands

List VPCs:

```bash
aws ec2 describe-vpcs
```

List Subnets:

```bash
aws ec2 describe-subnets
```

List Route Tables:

```bash
aws ec2 describe-route-tables
```

List Internet Gateways:

```bash
aws ec2 describe-internet-gateways
```

List NAT Gateways:

```bash
aws ec2 describe-nat-gateways
```

List Security Groups:

```bash
aws ec2 describe-security-groups
```

---

# 📝 Key Takeaways

- A VPC is an isolated virtual network in AWS.
- Public subnets are used for internet-facing resources.
- Private subnets host internal services and databases.
- Internet Gateway provides internet access to public subnets.
- NAT Gateway enables outbound internet access for private subnets.
- Route Tables define network traffic paths.
- Security Groups protect individual resources.
- Network ACLs secure subnets.
- Multi-AZ deployments improve availability and resilience.

---

# 📋 Summary

In this chapter, you learned:

- Amazon VPC
- CIDR Blocks
- Public and Private Subnets
- Internet Gateway
- NAT Gateway
- Route Tables
- Security Groups
- Network ACLs
- Multi-AZ Architecture
- Production Networking Best Practices

---

# ❓ Interview Questions

## Beginner

1. What is an Amazon VPC?
2. What is the purpose of a subnet?
3. What is an Internet Gateway?
4. What is a Route Table?
5. What is a NAT Gateway?

---

## Intermediate

6. What is the difference between a public and private subnet?
7. Explain the purpose of Security Groups.
8. What is a Network ACL?
9. Why should databases be deployed in private subnets?
10. Why are multiple Availability Zones important?

---

## Advanced

11. Explain the traffic flow from a user to an EC2 instance in a VPC.
12. Compare Security Groups and Network ACLs.
13. How would you design a production VPC for high availability?
14. Why is a NAT Gateway required in private subnets?
15. How can Terraform help manage AWS networking infrastructure?

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

Create two public subnets in different Availability Zones.

---

## Exercise 3

Attach an Internet Gateway and configure a public Route Table.

---

## Exercise 4

Create a NAT Gateway and configure private Route Tables.

---

## Exercise 5

Create a Security Group allowing:

- HTTP (80)
- HTTPS (443)
- SSH (22) from your IP only

---

# 🧩 Mini Project

Build a production-ready AWS VPC.

Infrastructure:

- 1 VPC
- 2 Public Subnets
- 2 Private Application Subnets
- 2 Private Database Subnets
- Internet Gateway
- NAT Gateway
- Public Route Table
- Private Route Tables
- Security Groups
- Network ACLs

Project structure:

```text
terraform-aws-vpc/

├── main.tf
├── vpc.tf
├── subnet.tf
├── route-table.tf
├── internet-gateway.tf
├── nat-gateway.tf
├── security-group.tf
├── nacl.tf
├── variables.tf
├── outputs.tf
├── providers.tf
├── versions.tf
├── terraform.tfvars
└── README.md
```

Run:

```bash
terraform init

terraform fmt

terraform validate

terraform plan

terraform apply

terraform destroy
```

Expected outcome:

- Production-ready VPC created.
- Public and Private subnets configured.
- Internet access available for public resources.
- Private resources access the internet through the NAT Gateway.
- Secure networking configured using Security Groups and Network ACLs.

---

# 📚 Further Reading

- Terraform AWS Provider Documentation
- AWS VPC User Guide
- AWS Well-Architected Framework
- Terraform Registry
- HashiCorp Learn

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [20 - Terraform with AWS IAM](20-Terraform-with-AWS-IAM.md) | [Terraform Roadmap](README.md) | [22 - Terraform with AWS EC2](22-Terraform-with-AWS-EC2.md) |

---

# 🚀 What's Next?

In **Chapter 22 – Terraform with AWS EC2**, you'll learn:

- 🖥️ Launch EC2 Instances with Terraform
- 🔑 Create and Use Key Pairs
- 🔒 Configure Security Groups
- 🌐 Assign Elastic IP Addresses
- 📜 Use User Data Scripts
- 💾 Attach EBS Volumes
- 🏗️ Production-Ready EC2 Deployments
