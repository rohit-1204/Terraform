# Terraform Notes
# Chapter 14 - Provisioners

> 📘 **Level:** Intermediate
> ⏱️ **Estimated Reading Time:** 75–90 minutes
> 🛠️ **Practice Time:** 4–5 hours

---

# 📚 Table of Contents

1. Introduction
2. What are Provisioners?
3. Why Use Provisioners?
4. Provisioner Types
5. Local-Exec Provisioner
6. Remote-Exec Provisioner
7. File Provisioner
8. Connection Block
9. Destroy-Time Provisioners
10. When to Avoid Provisioners
11. Best Practices
12. Summary
13. Interview Questions
14. Practice Exercises
15. Mini Project
16. Further Reading

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Terraform Provisioners
- Use Local-Exec Provisioners
- Configure Remote-Exec Provisioners
- Transfer files using File Provisioners
- Configure SSH connections
- Understand Provisioner limitations
- Follow Terraform best practices

---

# 📖 Introduction

Terraform is primarily used to **provision infrastructure**.

Sometimes, after a resource is created, you may need to:

- Run a shell command
- Copy a file
- Execute a script
- Perform post-deployment configuration

Terraform provides **Provisioners** for these tasks.

> ⚠️ Provisioners should be used only when no better alternative exists. Prefer cloud-native solutions such as **EC2 User Data**, **AWS Systems Manager (SSM)**, or configuration management tools like **Ansible** whenever possible.

---

# 💡 What are Provisioners?

Provisioners execute actions **after** a resource is created or **before** it is destroyed.

Example workflow:

```text
Terraform

        │

        ▼

Create EC2 Instance

        │

        ▼

Run Provisioner

        │

        ▼

Configuration Complete
```

---

# 🎯 Why Use Provisioners?

Provisioners can be used to:

- Run local scripts
- Execute remote commands
- Copy configuration files
- Perform one-time setup tasks

Common AWS use cases:

- Upload a configuration file
- Install software
- Register a server
- Execute a deployment script

---

# 📦 Provisioner Types

Terraform provides three commonly used provisioners.

| Provisioner | Purpose |
|-------------|---------|
| local-exec | Run commands on the machine executing Terraform |
| remote-exec | Run commands on the remote resource |
| file | Copy files to the remote resource |

---

# 💻 Local-Exec Provisioner

`local-exec` executes commands on your **local computer**, not on the AWS resource.

Example:

```hcl
resource "aws_instance" "web" {

  ami           = "ami-xxxxxxxx"

  instance_type = "t2.micro"

  provisioner "local-exec" {

    command = "echo EC2 Instance Created"

  }

}
```

After creation:

```text
EC2 Instance Created
```

---

# 📝 Saving Resource Information

Example:

```hcl
provisioner "local-exec" {

  command = "echo ${self.public_ip} > instance-ip.txt"

}
```

Result:

```text
instance-ip.txt

↓

54.12.34.56
```

---

# 🌐 Remote-Exec Provisioner

`remote-exec` executes commands **inside** the EC2 instance.

Example:

```hcl
resource "aws_instance" "web" {

  ami           = "ami-xxxxxxxx"

  instance_type = "t2.micro"

  provisioner "remote-exec" {

    inline = [

      "sudo yum update -y",

      "sudo yum install -y httpd",

      "sudo systemctl enable httpd",

      "sudo systemctl start httpd"

    ]

  }

}
```

---

# 🔐 Connection Block

`remote-exec` and `file` provisioners require a connection.

Example:

```hcl
connection {

  type = "ssh"

  user = "ec2-user"

  private_key = file("my-key.pem")

  host = self.public_ip

}
```

Fields:

| Option | Description |
|----------|-------------|
| type | Connection type (`ssh`) |
| user | Remote user |
| private_key | SSH private key |
| host | Target instance IP |

---

# 📁 File Provisioner

Copy files from your local machine to the EC2 instance.

Example:

```hcl
provisioner "file" {

  source = "index.html"

  destination = "/home/ec2-user/index.html"

}
```

Terraform uploads:

```text
Local Machine

↓

index.html

↓

EC2 Instance
```

---

# 🧹 Destroy-Time Provisioners

Provisioners can run before a resource is destroyed.

Example:

```hcl
provisioner "local-exec" {

  when = destroy

  command = "echo Instance Deleted"

}
```

Triggered by:

```bash
terraform destroy
```

---

# 🏗️ Multiple Provisioners

A resource can contain multiple provisioners.

Example:

```hcl
resource "aws_instance" "web" {

  ami           = "ami-xxxxxxxx"

  instance_type = "t2.micro"

  provisioner "file" {

    source = "config.conf"

    destination = "/home/ec2-user/config.conf"

  }

  provisioner "remote-exec" {

    inline = [

      "sudo mv /home/ec2-user/config.conf /etc/httpd/conf/"

    ]

  }

}
```

Execution order:

```text
Create Instance

↓

Upload File

↓

Execute Commands
```

---

# 🌍 Real-World Example

Create an EC2 instance and install Apache.

```hcl
resource "aws_instance" "web" {

  ami           = "ami-xxxxxxxx"

  instance_type = "t2.micro"

  connection {

    type = "ssh"

    user = "ec2-user"

    private_key = file("my-key.pem")

    host = self.public_ip

  }

  provisioner "remote-exec" {

    inline = [

      "sudo yum update -y",

      "sudo yum install -y httpd",

      "sudo systemctl enable httpd",

      "sudo systemctl start httpd"

    ]

  }

}
```

---

# ⚠️ Limitations of Provisioners

Provisioners are **not idempotent** and can fail due to:

- SSH connectivity issues
- Network failures
- Script errors
- Manual resource changes

Terraform cannot always recover automatically.

Prefer:

- EC2 User Data
- AWS Systems Manager (SSM)
- AWS CloudFormation Init
- Configuration management tools (Ansible, Chef, Puppet)

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

---

# 🏗️ Provisioner Workflow

```text
Terraform Apply

        │

        ▼

Create EC2 Instance

        │

        ▼

SSH Connection

        │

        ▼

Copy Files

        │

        ▼

Run Commands

        │

        ▼

Provision Complete
```

---

# 🏆 Best Practices

- ✅ Use provisioners only when necessary.
- ✅ Prefer EC2 User Data for bootstrapping.
- ✅ Use AWS Systems Manager instead of SSH where possible.
- ✅ Keep scripts simple and idempotent.
- ✅ Secure SSH keys.
- ✅ Test scripts independently.
- ✅ Avoid long-running commands.
- ✅ Use configuration management tools for complex setups.

---

# ⚠️ Common Mistakes

| Mistake | Solution |
|----------|----------|
| Using provisioners for all configuration | Use User Data or SSM |
| Incorrect SSH user | Verify the AMI's default user |
| Wrong private key | Use the matching key pair |
| Long-running scripts | Keep scripts lightweight |
| Hardcoding secrets | Use AWS Secrets Manager or SSM Parameter Store |

---

# 📝 Key Takeaways

- Provisioners perform post-deployment tasks.
- `local-exec` runs locally.
- `remote-exec` runs on the remote resource.
- `file` copies files to remote resources.
- Provisioners should be a last resort.

---

# 📋 Summary

In this chapter, you learned:

- Terraform Provisioners
- Local-Exec
- Remote-Exec
- File Provisioner
- SSH Connection Block
- Destroy-Time Provisioners
- Provisioner Best Practices

---

# ❓ Interview Questions

## Beginner

1. What are Terraform Provisioners?
2. What is `local-exec`?
3. What is `remote-exec`?
4. What is the File Provisioner?
5. Why are connection blocks required?

---

## Intermediate

6. Explain the difference between `local-exec` and `remote-exec`.
7. When should provisioners be used?
8. What is a destroy-time provisioner?
9. What are the limitations of provisioners?
10. How do file provisioners work?

---

## Advanced

11. Why are provisioners considered a last resort?
12. What AWS alternatives exist for provisioners?
13. How would you securely execute commands on EC2 instances?
14. How do you troubleshoot failed provisioners?
15. How would you automate EC2 configuration without using provisioners?

---

# 🎯 Practice Exercises

## Exercise 1

Print a message using `local-exec`.

```hcl
provisioner "local-exec" {

  command = "echo Hello Terraform"

}
```

---

## Exercise 2

Save the EC2 public IP.

```hcl
command = "echo ${self.public_ip} > instance-ip.txt"
```

---

## Exercise 3

Install Apache using `remote-exec`.

```hcl
inline = [

  "sudo yum install -y httpd"

]
```

---

## Exercise 4

Copy a file using the File Provisioner.

```hcl
source = "index.html"

destination = "/home/ec2-user/index.html"
```

---

## Exercise 5

Run a command during resource destruction.

```hcl
when = destroy
```

---

# 🧩 Mini Project

Create a Terraform project that:

- Launches an EC2 instance
- Copies a web page using the File Provisioner
- Installs Apache using Remote-Exec
- Saves the public IP locally using Local-Exec

Project structure:

```text
terraform-provisioners/

├── main.tf
├── providers.tf
├── variables.tf
├── outputs.tf
├── index.html
└── my-key.pem
```

Run:

```bash
terraform init

terraform validate

terraform plan

terraform apply

terraform destroy
```

Verify:

- Apache is installed.
- `index.html` is copied to the instance.
- `instance-ip.txt` is created locally.

---

# 📚 Further Reading

- Terraform Provisioners Documentation
- Terraform Connection Block Documentation
- AWS EC2 User Data Documentation
- AWS Systems Manager Documentation
- HashiCorp Learn

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [13 - Workspaces](13-Workspaces.md) | [Terraform Roadmap](README.md) | [15 - Data Sources.md](15-Data-Sources.md) |

---

# 🚀 What's Next?

In **Chapter 15 – Data Sources**, you'll learn:

- 🔍 What are Data Sources?
- 📖 Reading Existing AWS Resources
- 🌐 Using Existing VPCs, Subnets, and AMIs
- 🔗 Combining Data Sources with Resources
- 🎯 Real-World AWS Examples
