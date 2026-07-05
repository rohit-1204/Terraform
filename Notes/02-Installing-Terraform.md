# Terraform Notes
# Chapter 02 - Installing Terraform

> 📘 **Level:** Beginner
> ⏱️ **Estimated Reading Time:** 45–60 minutes
> 🛠️ **Practice Time:** 2–3 hours

---

# 📚 Table of Contents

1. Prerequisites
2. Installing Terraform
3. Installing AWS CLI
4. Configuring AWS Credentials
5. Verifying Installation
6. Terraform Directory Structure
7. First Terraform Project
8. Useful Commands
9. Best Practices
10. Summary
11. Interview Questions
12. Practice Exercises
13. Mini Project
14. Further Reading

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Install Terraform
- Install AWS CLI
- Configure AWS credentials
- Verify Terraform installation
- Create your first Terraform project
- Understand the Terraform project structure

---

# 📖 Prerequisites

Before installing Terraform, ensure you have:

- AWS Account (Free Tier is sufficient)
- AWS IAM User with programmatic access
- Internet connection
- Administrator or sudo privileges
- Visual Studio Code (recommended)
- Git (recommended)

---

# 💻 Installing Terraform

## Windows

### Step 1

Download Terraform from the official HashiCorp website.

### Step 2

Extract the downloaded ZIP file.

Example:

```text
terraform.exe
```

### Step 3

Move the executable to a folder such as:

```text
C:\Terraform\
```

### Step 4

Add the folder to the Windows **PATH** environment variable.

### Step 5

Verify installation:

```bash
terraform version
```

---

## Linux (Ubuntu)

Update packages:

```bash
sudo apt update
```

Install required packages:

```bash
sudo apt install -y gnupg software-properties-common
```

Add the HashiCorp GPG key:

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
```

Add the HashiCorp repository:

```bash
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list
```

Install Terraform:

```bash
sudo apt update

sudo apt install terraform
```

Verify:

```bash
terraform version
```

---

## macOS

Using Homebrew:

```bash
brew tap hashicorp/tap

brew install hashicorp/tap/terraform
```

Verify:

```bash
terraform version
```

---

# ☁️ Installing AWS CLI

Terraform communicates with AWS using the AWS CLI credentials.

Verify installation:

```bash
aws --version
```

If not installed, download and install AWS CLI according to your operating system.

---

# 🔑 Configuring AWS Credentials

Configure credentials:

```bash
aws configure
```

Example:

```text
AWS Access Key ID: AKIA****************

AWS Secret Access Key: ************************

Default region: us-east-1

Default output format: json
```

Credentials are stored locally.

---

# 📁 AWS Credentials Location

Linux/macOS:

```text
~/.aws/credentials
```

Windows:

```text
C:\Users\<username>\.aws\credentials
```

Configuration file:

```text
~/.aws/config
```

---

# ✅ Verify AWS Connection

Display configured identity:

```bash
aws sts get-caller-identity
```

Expected output:

```json
{
  "UserId": "AIDAEXAMPLE",
  "Account": "123456789012",
  "Arn": "arn:aws:iam::123456789012:user/terraform-user"
}
```

---

# 📂 Terraform Project Structure

A simple project looks like:

```text
terraform-demo/

├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
└── .terraform/
```

Description:

| File | Purpose |
|------|---------|
| main.tf | Main infrastructure configuration |
| variables.tf | Input variables |
| outputs.tf | Output values |
| terraform.tfvars | Variable values |
| .terraform | Downloaded providers and plugins |

---

# 🚀 Creating Your First Project

Create a directory:

```bash
mkdir terraform-demo

cd terraform-demo
```

Create a configuration file:

```bash
touch main.tf
```

Initialize Terraform:

```bash
terraform init
```

Expected output:

```text
Terraform has been successfully initialized!
```

---

# 💻 Essential Commands

Check version:

```bash
terraform version
```

Initialize project:

```bash
terraform init
```

Validate configuration:

```bash
terraform validate
```

Format configuration:

```bash
terraform fmt
```

Show help:

```bash
terraform --help
```

---

# 🏗️ Installation Workflow

```text
Install Terraform

↓

Install AWS CLI

↓

Configure AWS Credentials

↓

Verify AWS Access

↓

Create Project

↓

terraform init

↓

Ready to Build Infrastructure
```

---

# 🏆 Best Practices

- ✅ Use an IAM user instead of the AWS root account.
- ✅ Grant only the required IAM permissions.
- ✅ Keep AWS credentials secure.
- ✅ Never commit credentials to Git.
- ✅ Verify installations before creating infrastructure.
- ✅ Keep Terraform and AWS CLI updated.

---

# ⚠️ Common Installation Issues

| Issue | Solution |
|--------|----------|
| terraform: command not found | Add Terraform to PATH |
| aws: command not found | Install AWS CLI |
| Invalid AWS credentials | Run `aws configure` again |
| Permission denied | Use administrator/sudo privileges |
| Provider download failed | Check internet connectivity |

---

# 📝 Key Takeaways

- Terraform must be installed before creating infrastructure.
- AWS CLI provides authentication for Terraform.
- IAM credentials are stored locally.
- `terraform init` prepares the working directory.
- Always verify installations before proceeding.

---

# 📋 Summary

In this chapter, you learned:

- Installing Terraform
- Installing AWS CLI
- Configuring AWS credentials
- Verifying AWS connectivity
- Creating the first Terraform project
- Essential Terraform commands

---

# ❓ Interview Questions

## Beginner

1. How do you install Terraform?
2. Why is AWS CLI required?
3. Where are AWS credentials stored?
4. What does `terraform init` do?
5. How do you verify the Terraform installation?

---

## Intermediate

6. Why should you avoid using the AWS root account?
7. Explain the purpose of the `.terraform` directory.
8. What files make up a Terraform project?
9. What happens during `terraform init`?
10. How does Terraform authenticate with AWS?

---

## Advanced

11. Explain Terraform provider installation.
12. How would you configure Terraform in a CI/CD pipeline?
13. How do environment variables work with AWS credentials?
14. What is the provider lock file?
15. How would you manage multiple AWS accounts?

---

# 🎯 Practice Exercises

## Exercise 1

Verify Terraform installation.

```bash
terraform version
```

---

## Exercise 2

Verify AWS CLI.

```bash
aws --version
```

---

## Exercise 3

Configure AWS credentials.

```bash
aws configure
```

---

## Exercise 4

Verify your AWS identity.

```bash
aws sts get-caller-identity
```

---

## Exercise 5

Initialize a Terraform project.

```bash
mkdir terraform-demo

cd terraform-demo

touch main.tf

terraform init
```

---

# 🧩 Mini Project

Create the following directory structure:

```text
terraform-demo/

├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
```

Then execute:

```bash
terraform init

terraform validate

terraform fmt
```

Ensure all commands complete successfully.

---

# 📚 Further Reading

- Terraform Installation Guide
- Terraform CLI Documentation
- AWS CLI Documentation
- AWS IAM Best Practices
- Terraform AWS Provider Documentation

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [01 - Introduction to Terraform](01-Introduction-to-Terraform.md) | [Terraform Roadmap](README.md) | [03 - Terraform Basics](03-Terraform-Basics.md) |

---

# 🚀 What's Next?

In **Chapter 03 – Terraform Basics**, you'll learn:

- 📝 HashiCorp Configuration Language (HCL)
- ☁️ Providers
- 📦 Resources
- 📄 Terraform Configuration Files
- 🚀 Terraform Workflow
- 💻 Your First AWS Infrastructure Using Terraform
