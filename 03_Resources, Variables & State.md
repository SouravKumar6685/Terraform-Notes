# Terraform Resources, Variables & Data Types

![Meme](https://i.imgflip.com/4/4t0m5.jpg)  
*"When you realize Infrastructure as Code is just programming with extra steps!"* 😂

---

## 🚀 1️⃣ What are Resources in Terraform? 🏗️
Imagine you're building a house. 🏠 Each brick, window, and door is a **resource**. Similarly, in Terraform, **resources** define the parts of your cloud infrastructure.

🔹 **Think of it like ordering from Amazon:**
- You add a laptop to your cart (Terraform `resource`).
- You specify details like brand and RAM (resource attributes).
- When you place the order (`terraform apply`), Amazon delivers it to your home (AWS provisions the resource).

### 📝 **Example: Creating an AWS EC2 Instance**
Let's say we need a virtual computer (EC2 instance) on AWS:

```hcl
resource "aws_instance" "example" {
  ami           = "ami-0abcdef1234567890" # Amazon Machine Image (OS template)
  instance_type = "t2.micro"  # Small-sized virtual machine
}
```
✅ This tells Terraform: *"Hey Terraform, create an EC2 instance for me!"* 😎

### 🔍 **How to Find Your EC2 Instance in AWS Console?**
1. Open **AWS Console** 🖥️ → [AWS Login](https://aws.amazon.com/console/)
2. Click on **EC2** (search bar me likho EC2).
3. Click **Instances**.
4. You'll see your new instance running! 🎉

---

## 🎛️ 2️⃣ What are Variables in Terraform? 🤔
Imagine you own a burger shop. 🍔
- Customers can choose different buns, patties, and sauces.
- Instead of writing separate recipes for each burger, you use **variables**!

Terraform **variables** work the same way. They make your code reusable, just like a menu for your infrastructure. 📋

### 🏷️ **Types of Variables in Terraform**

#### 🏷️ **1. String Variable (Text Values)**
```hcl
variable "instance_type" {
  default = "t2.micro"
}
```
➡️ This defines a variable named `instance_type` with a default value of `t2.micro`.

#### 📋 **2. List Variable (Multiple Choices)**
```hcl
variable "availability_zones" {
  type    = list(string)
  default = ["us-east-1a", "us-east-1b"]
}
```
➡️ This allows choosing from a list of availability zones.

#### 🗺️ **3. Map Variable (Key-Value Pairs)**
```hcl
variable "instance_ami" {
  type = map(string)
  default = {
    us-east-1 = "ami-0abcdef1234567890"
    us-west-2 = "ami-0fedcba987654321"
  }
}
```
➡️ Different regions require different AMIs (OS templates), so we use a **map**.

### 💡 **Using Variables in Resources**
```hcl
resource "aws_instance" "example" {
  ami           = var.instance_ami["us-east-1"]
  instance_type = var.instance_type
}
```
✅ Terraform will pick values from the variables instead of hardcoding them.

### 🔍 **Where to See Your Instance in AWS?**
1. Run `terraform apply`.
2. Open **AWS Console** → **EC2** → **Instances**.
3. Look for the instance with the selected AMI and type.

---

## 🔢 3️⃣ Data Types in Terraform 🧠
Terraform supports different types of data, just like how we categorize school subjects! 📚

| Data Type      | Example |
|---------------|---------|
| **string**    | `"hello"` |
| **number**    | `42` |
| **bool**      | `true` |
| **list(string)** | `["ap-south-1", "us-east-1"]` |
| **map(string)** | `{ region = "us-east-1" }` |

### 🛠 **Example: Using Different Data Types Together**
```hcl
variable "config" {
  type = object({
    name    = string
    count   = number
    enabled = bool
  })
  default = {
    name    = "Test-Instance"
    count   = 3
    enabled = true
  }
}
```
➡️ Here, `config` stores a name, a number (count), and a boolean (enabled or disabled).

### 🔍 **Finding Your Terraform Instances in AWS**
1. Run `terraform apply`.
2. Open **AWS Console** → **EC2**.
3. Use the search bar to filter by **Test-Instance**.

---

## 🎯 **Conclusion & Next Steps**
✅ **Resources** define what to create in AWS.  
✅ **Variables** make your Terraform code flexible and reusable.  
✅ **Data types** help structure information properly.  

🚀 Now, it's time for you to explore Terraform hands-on! **Try running `terraform apply` and see the magic happen.** 🎩✨

