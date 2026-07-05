# Terraform Notes

A complete **Beginner to Advanced Terraform Guide** focused on **AWS Cloud Infrastructure**.

This repository is designed for:

- 🚀 DevOps Engineers
- ☁️ AWS Cloud Engineers
- 🏗️ Infrastructure Engineers
- 🔧 Platform Engineers
- 📦 Site Reliability Engineers (SRE)
- 💼 Students preparing for Terraform & AWS interviews

The course starts with Terraform fundamentals and gradually moves toward designing and managing production-ready AWS infrastructure using Infrastructure as Code (IaC).

---

# 📚 Course Roadmap

| No. | Chapter | Description |
|----|----------|-------------|
| 01 | [Introduction to Terraform](01-Introduction-to-Terraform.md) | What is Terraform, IaC, Benefits, Workflow |
| 02 | [Installing Terraform](02-Installing-Terraform.md) | Install Terraform on Windows, Linux & macOS |
| 03 | [Terraform Basics](03-Terraform-Basics.md) | HCL, Providers, Resources, Workflow |
| 04 | [Providers and Resources](04-Providers-and-Resources.md) | Providers, Resources and Resource Blocks |
| 05 | [Terraform Configuration Files](05-Terraform-Configuration-Files.md) | main.tf, variables.tf, outputs.tf, terraform.tfvars |
| 06 | [Terraform State](06-Terraform-State.md) | State File, State Management, State Commands |
| 07 | [Variables and Outputs](07-Variables-and-Outputs.md) | Input Variables, Output Values |
| 08 | [Terraform Functions](08-Terraform-Functions.md) | Built-in Functions |
| 09 | [Expressions and Loops](09-Expressions-and-Loops.md) | for_each, count, for expressions, conditionals |
| 10 | [Modules](10-Modules.md) | Reusable Infrastructure Modules |
| 11 | [Remote State](11-Remote-State.md) | AWS S3 Backend |
| 12 | [Backend Configuration](12-Backend-Configuration.md) | Local & Remote Backends |
| 13 | [Workspaces](13-Workspaces.md) | Dev, Test & Production Environments |
| 14 | [Provisioners](14-Provisioners.md) | local-exec, remote-exec, file Provisioner |
| 15 | [Data Sources](15-Data-Sources.md) | Fetch Existing AWS Resources |
| 16 | [Locals](16-Locals.md) | Local Variables |
| 17 | [Dependencies](17-Dependencies.md) | depends_on & Resource Dependency |
| 18 | [Lifecycle and Meta Arguments](18-Lifecycle-and-Meta-Arguments.md) | create_before_destroy, ignore_changes, prevent_destroy |
| 19 | [Terraform CLI](19-Terraform-CLI.md) | Essential Terraform Commands |
| 20 | [Terraform with AWS IAM](20-Terraform-with-AWS-IAM.md) | IAM Users, Groups, Policies & Roles |
| 21 | [Terraform with AWS VPC](21-Terraform-with-AWS-VPC.md) | VPC, Subnets, Route Tables, IGW |
| 22 | [Terraform with AWS EC2](22-Terraform-with-AWS-EC2.md) | EC2 Instances, Security Groups, Key Pairs |
| 23 | [Terraform with AWS S3](23-Terraform-with-AWS-S3.md) | Buckets, Versioning, Encryption |
| 24 | [Terraform with AWS RDS](24-Terraform-with-AWS-RDS.md) | Database Provisioning |
| 25 | [Terraform with AWS ALB & Auto Scaling](25-Terraform-with-AWS-ALB-AutoScaling.md) | Load Balancer & Auto Scaling Groups |
| 26 | [Terraform with AWS EKS](26-Terraform-with-AWS-EKS.md) | Deploy Kubernetes Cluster |
| 27 | [Terraform Security Best Practices](27-Terraform-Security-Best-Practices.md) | Secure Infrastructure |
| 28 | [Terraform Testing and Validation](28-Terraform-Testing-and-Validation.md) | fmt, validate, plan, linting |
| 29 | [Terraform in CI/CD](29-Terraform-in-CI-CD.md) | GitHub Actions & Jenkins Integration |
| 30 | [Terraform State Locking](30-Terraform-State-Locking.md) | S3 + DynamoDB |
| 31 | [Terraform Import](31-Terraform-Import.md) | Import Existing AWS Resources |
| 32 | [Terraform Drift Detection](32-Terraform-Drift-Detection.md) | Detect Infrastructure Changes |
| 33 | [Terraform Cost Optimization](33-Terraform-Cost-Optimization.md) | Reduce AWS Infrastructure Cost |
| 34 | [Terraform Troubleshooting](34-Terraform-Troubleshooting.md) | Debugging Common Errors |
| 35 | [Terraform Production Architecture](35-Terraform-Production-Architecture.md) | Production Infrastructure Design |
| 36 | [Terraform Interview Questions](36-Terraform-Interview-Questions.md) | Beginner to Advanced Interview Questions |
| 37 | [Terraform CheatSheet](37-Terraform-CheatSheet.md) | Quick Reference Guide |

---

# 🎯 Prerequisites

Before starting this course, you should have basic knowledge of:

- AWS Cloud Fundamentals
- Linux Commands
- Networking Basics
- Git & GitHub
- Basic Command Line Usage

---

# 🛠️ Tools Required

- Terraform
- AWS CLI
- Visual Studio Code
- Git
- GitHub Account
- AWS Free Tier Account

---

# 💻 What You'll Learn

After completing this course, you'll be able to:

- Write Infrastructure as Code (IaC)
- Create reusable Terraform modules
- Manage Terraform State
- Configure Remote State using AWS S3
- Implement State Locking using DynamoDB
- Provision AWS infrastructure
- Manage IAM, VPC, EC2, S3, RDS, and EKS
- Build production-ready infrastructure
- Integrate Terraform with CI/CD pipelines
- Troubleshoot Terraform deployments
- Prepare for Terraform interviews

---

# 🏗️ Learning Path

```text
Terraform Basics
        │
        ▼
Configuration Files
        │
        ▼
State Management
        │
        ▼
Variables & Outputs
        │
        ▼
Modules
        │
        ▼
AWS Infrastructure
        │
        ▼
Security
        │
        ▼
CI/CD
        │
        ▼
Production Architecture
        │
        ▼
Interview Preparation
```

---

# 📖 Best Practices

- Write reusable modules.
- Store state remotely.
- Enable state locking.
- Never commit secrets to Git.
- Use version control.
- Validate before applying.
- Use least privilege IAM permissions.
- Review Terraform plans carefully.
- Keep Terraform and providers updated.

---

# 📚 Recommended Learning Order

1. Terraform Fundamentals
2. Terraform Language (HCL)
3. Terraform State
4. Modules
5. AWS Resources
6. Security
7. CI/CD
8. Production Architecture
9. Troubleshooting
10. Interview Questions
11. CheatSheet

---

# 🤝 Contributing

Contributions are welcome!

If you'd like to improve these notes:

1. Fork the repository
2. Create a new branch
3. Commit your changes
4. Open a Pull Request

---

# 📄 License

This project is intended for learning and educational purposes.

Feel free to use these notes for personal learning, interview preparation, and teaching.

---

# 🚀 Next Steps

Start your Terraform journey with:

➡️ **[Chapter 01 - Introduction to Terraform](01-Introduction-to-Terraform.md)**

Happy Learning! 🎉
