# Terraform Notes
# Chapter 08 - Terraform Functions

> 📘 **Level:** Beginner to Intermediate
> ⏱️ **Estimated Reading Time:** 60–90 minutes
> 🛠️ **Practice Time:** 3–4 hours

---

# 📚 Table of Contents

1. Introduction
2. What are Terraform Functions?
3. Function Syntax
4. String Functions
5. Numeric Functions
6. Collection Functions
7. Map Functions
8. Encoding Functions
9. Date & Time Functions
10. IP Network Functions
11. Type Conversion Functions
12. Real-World Examples
13. Best Practices
14. Summary
15. Interview Questions
16. Practice Exercises
17. Mini Project
18. Further Reading

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Understand Terraform functions
- Use built-in functions effectively
- Manipulate strings, lists, and maps
- Convert data types
- Generate dynamic values
- Build reusable Terraform configurations

---

# 📖 Introduction

Terraform provides **built-in functions** that help you manipulate values without writing custom code.

Functions are commonly used to:

- Format strings
- Join lists
- Read files
- Encode JSON
- Generate CIDR blocks
- Convert data types
- Perform calculations

---

# 💡 What are Terraform Functions?

A function accepts one or more arguments and returns a value.

General syntax:

```hcl
function_name(argument1, argument2, ...)
```

Example:

```hcl
upper("terraform")
```

Output:

```text
TERRAFORM
```

---

# 📝 String Functions

## upper()

Converts text to uppercase.

```hcl
upper("aws")
```

Output:

```text
AWS
```

---

## lower()

Converts text to lowercase.

```hcl
lower("Terraform")
```

Output:

```text
terraform
```

---

## title()

Capitalizes each word.

```hcl
title("terraform on aws")
```

Output:

```text
Terraform On Aws
```

---

## trim()

Removes leading and trailing characters.

```hcl
trim("  terraform  ", " ")
```

Output:

```text
terraform
```

---

## replace()

Replace text.

```hcl
replace("terraform-demo", "-", "_")
```

Output:

```text
terraform_demo
```

---

## format()

Formats strings.

```hcl
format("%s-%s", "web", "01")
```

Output:

```text
web-01
```

---

# 🔢 Numeric Functions

## max()

```hcl
max(10, 20, 30)
```

Output:

```text
30
```

---

## min()

```hcl
min(10, 20, 30)
```

Output:

```text
10
```

---

## abs()

```hcl
abs(-25)
```

Output:

```text
25
```

---

## ceil()

```hcl
ceil(10.2)
```

Output:

```text
11
```

---

## floor()

```hcl
floor(10.9)
```

Output:

```text
10
```

---

# 📋 Collection Functions

## length()

```hcl
length(["a","b","c"])
```

Output:

```text
3
```

---

## concat()

```hcl
concat(["a"], ["b","c"])
```

Output:

```text
["a","b","c"]
```

---

## distinct()

Removes duplicates.

```hcl
distinct(["dev","dev","prod"])
```

Output:

```text
["dev","prod"]
```

---

## sort()

```hcl
sort(["z","a","c"])
```

Output:

```text
["a","c","z"]
```

---

## reverse()

```hcl
reverse(["a","b","c"])
```

Output:

```text
["c","b","a"]
```

---

## element()

```hcl
element(["dev","test","prod"],1)
```

Output:

```text
test
```

---

# 🗺️ Map Functions

## lookup()

```hcl
lookup(
  {
    dev  = "t2.micro"
    prod = "t3.large"
  },
  "dev"
)
```

Output:

```text
t2.micro
```

---

## keys()

```hcl
keys({
  Name="Server"
  Env="Dev"
})
```

Output:

```text
["Env","Name"]
```

---

## values()

```hcl
values({
  Name="Server"
  Env="Dev"
})
```

Output:

```text
["Dev","Server"]
```

---

# 📄 Encoding Functions

## jsonencode()

```hcl
jsonencode({
  Name = "WebServer"
})
```

Output:

```json
{"Name":"WebServer"}
```

---

## jsondecode()

```hcl
jsondecode("{\"env\":\"dev\"}")
```

Output:

```text
{
  env = "dev"
}
```

---

## base64encode()

```hcl
base64encode("Terraform")
```

---

## base64decode()

```hcl
base64decode("VGVycmFmb3Jt")
```

Output:

```text
Terraform
```

---

# 📅 Date & Time Functions

## timestamp()

```hcl
timestamp()
```

Example output:

```text
2026-07-05T10:30:45Z
```

---

## formatdate()

```hcl
formatdate(
  "YYYY-MM-DD",
  timestamp()
)
```

Output:

```text
2026-07-05
```

---

# 🌐 IP Network Functions

## cidrhost()

```hcl
cidrhost(
  "10.0.0.0/24",
  10
)
```

Output:

```text
10.0.0.10
```

---

## cidrsubnet()

```hcl
cidrsubnet(
  "10.0.0.0/16",
  8,
  1
)
```

Output:

```text
10.0.1.0/24
```

---

# 🔄 Type Conversion Functions

## tostring()

```hcl
tostring(100)
```

Output:

```text
100
```

---

## tonumber()

```hcl
tonumber("200")
```

Output:

```text
200
```

---

## tolist()

```hcl
tolist(["a","b"])
```

---

## tomap()

```hcl
tomap({
  Name = "Server"
})
```

---

# 🏗️ Real-World Example

```hcl
variable "environment" {

  default = "dev"

}

locals {

  bucket_name = lower(
    format(
      "%s-app-bucket",
      var.environment
    )
  )

}

resource "aws_s3_bucket" "demo" {

  bucket = local.bucket_name

}
```

If:

```text
environment = DEV
```

Bucket name becomes:

```text
dev-app-bucket
```

---

# 💻 Testing Functions

Open Terraform Console:

```bash
terraform console
```

Example:

```text
> upper("terraform")

"TERRAFORM"

> length(["a","b","c"])

3

> max(1,5,10)

10
```

Exit:

```text
exit
```

---

# 🏆 Best Practices

- ✅ Use functions instead of hardcoded values.
- ✅ Keep expressions readable.
- ✅ Use `terraform console` for testing.
- ✅ Use locals for complex expressions.
- ✅ Avoid deeply nested functions.
- ✅ Use `lookup()` for optional map values.
- ✅ Validate variable inputs.

---

# ⚠️ Common Mistakes

| Mistake | Solution |
|----------|----------|
| Incorrect data type | Use conversion functions |
| Hardcoded strings | Use `format()` |
| Duplicate list values | Use `distinct()` |
| Complex expressions | Move to `locals` |
| Testing directly in code | Use `terraform console` |

---

# 📝 Key Takeaways

- Functions simplify Terraform code.
- Functions manipulate strings, lists, maps, and numbers.
- `terraform console` is useful for testing.
- Functions improve readability and reusability.
- Use locals to simplify complex expressions.

---

# 📋 Summary

In this chapter, you learned:

- Terraform Functions
- String Functions
- Numeric Functions
- Collection Functions
- Map Functions
- Encoding Functions
- Date Functions
- Network Functions
- Type Conversion Functions

---

# ❓ Interview Questions

## Beginner

1. What are Terraform functions?
2. How do you call a function?
3. What does `upper()` do?
4. What does `length()` return?
5. What is `terraform console`?

---

## Intermediate

6. Explain `lookup()`.
7. Difference between `concat()` and `join()`?
8. What is `jsonencode()` used for?
9. Why use `format()`?
10. Explain `cidrsubnet()`.

---

## Advanced

11. When should you use locals with functions?
12. How do functions improve reusable modules?
13. Explain type conversion functions.
14. How do Terraform functions help dynamic infrastructure?
15. Which functions are commonly used in AWS Terraform projects?

---

# 🎯 Practice Exercises

## Exercise 1

Convert a string to uppercase.

```hcl
upper("terraform")
```

---

## Exercise 2

Find the number of items.

```hcl
length(["dev","test","prod"])
```

---

## Exercise 3

Retrieve a value from a map.

```hcl
lookup(
  {
    env = "development"
  },
  "env"
)
```

---

## Exercise 4

Generate a subnet.

```hcl
cidrsubnet(
  "10.0.0.0/16",
  8,
  2
)
```

---

## Exercise 5

Test all functions.

```bash
terraform console
```

---

# 🧩 Mini Project

Create a Terraform project that:

- Creates an S3 bucket
- Generates the bucket name using `format()` and `lower()`
- Uses `lookup()` for instance types
- Uses `timestamp()` in tags
- Displays outputs

Example structure:

```text
terraform-functions-demo/

├── main.tf
├── variables.tf
├── outputs.tf
├── locals.tf
└── terraform.tfvars
```

Run:

```bash
terraform init

terraform validate

terraform plan

terraform apply

terraform console

terraform destroy
```

---

# 📚 Further Reading

- Terraform Function Documentation
- Terraform Console Documentation
- HashiCorp Learn
- AWS Provider Documentation
- Terraform Language Reference

---

# 📚 Navigation

| ⬅️ Previous | 🏠 Home | ➡️ Next |
|------------|---------|---------|
| [07 - Variables and Outputs](07-Variables-and-Outputs.md) | [Terraform Roadmap](README.md) | [09 - Expressions and Loops](09-Expressions-and-Loops.md) |

---

# 🚀 What's Next?

In **Chapter 09 – Expressions and Loops**, you'll learn:

- 🔁 Conditional Expressions
- 🔄 `count`
- 📋 `for_each`
- 🧮 `for` Expressions
- 🎯 Dynamic Resource Creation
- 🏗️ Real-World AWS Examples
