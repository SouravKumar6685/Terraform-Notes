

# **Terraform Advanced Concepts 🚀**

## 1️⃣ **Terraform Provisioners (Remote & Local Execution)**
Terraform **Provisioners** allow us to execute scripts or commands on local or remote machines during resource creation or destruction.

### **Types of Provisioners**  
🔹 **Local Provisioner** – Runs scripts on the machine where Terraform is executed.  
🔹 **Remote Provisioner** – Runs scripts on a remote resource (like an EC2 instance).  

### **Example: Local Provisioner**
Runs a local script after resource creation.
```hcl
resource "null_resource" "example" {
  provisioner "local-exec" {
    command = "echo 'Terraform Applied!' > result.txt"
  }
}
```
✔ This creates a `result.txt` file on the local machine.

---

### **Example: Remote Provisioner**
Runs commands on a remote server.
```hcl
resource "aws_instance" "web" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  provisioner "remote-exec" {
    inline = [
      "sudo apt update",
      "sudo apt install nginx -y"
    ]
  }

  connection {
    type        = "ssh"
    user        = "ubuntu"
    private_key = file("~/.ssh/id_rsa")
    host        = self.public_ip
  }
}
```
✔ Installs Nginx on an AWS EC2 instance after provisioning.

---

## 2️⃣ **Terraform Data Sources – Accessing Existing AWS Resources**
Terraform **Data Sources** help fetch information about existing resources (like VPCs, Security Groups) instead of hardcoding values.

### **Example: Fetch an Existing AWS VPC**
```hcl
data "aws_vpc" "default" {
  default = true
}

resource "aws_subnet" "example" {
  vpc_id = data.aws_vpc.default.id
  cidr_block = "10.0.1.0/24"
}
```
✔ Retrieves the default VPC ID and uses it in a new subnet.

---

### **Example: Fetch an Existing Security Group**
```hcl
data "aws_security_group" "existing_sg" {
  name = "my-security-group"
}

resource "aws_instance" "web" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
  security_groups = [data.aws_security_group.existing_sg.name]
}
```
✔ Fetches an existing Security Group and attaches it to an EC2 instance.

---

## 3️⃣ **Terraform Dynamic Blocks & Loops**
Terraform **Dynamic Blocks** allow looping within resource configurations for flexibility.

### **Example: Dynamic Security Group Rules**
```hcl
resource "aws_security_group" "example" {
  name = "dynamic-sg"

  dynamic "ingress" {
    for_each = [80, 443, 22]
    content {
      from_port   = ingress.value
      to_port     = ingress.value
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
  }
}
```
✔ Creates multiple ingress rules dynamically for ports 80, 443, and 22.

---

### **Example: Dynamic Tags**
```hcl
resource "aws_instance" "web" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  dynamic "tags" {
    for_each = {
      Name        = "MyEC2"
      Environment = "Production"
      Owner       = "DevOps"
    }
    content {
      key   = tags.key
      value = tags.value
    }
  }
}
```
✔ Adds multiple tags dynamically to the EC2 instance.

---

## 4️⃣ **Terraform Functions & Conditionals**
Terraform provides **built-in functions & conditionals** for dynamic configurations.

### **Example: Using `length()` Function**
```hcl
variable "subnets" {
  default = ["subnet-123", "subnet-456", "subnet-789"]
}

output "subnet_count" {
  value = length(var.subnets)
}
```
✔ Counts the number of subnets.

---

### **Example: Conditionals (`count` vs `for_each`)**
```hcl
resource "aws_instance" "web" {
  count         = 2
  ami           = "ami-12345678"
  instance_type = "t2.micro"
}
```
✔ Creates **2 instances** using `count`.

```hcl
variable "names" {
  default = ["web1", "web2"]
}

resource "aws_instance" "web" {
  for_each = toset(var.names)
  ami           = "ami-12345678"
  instance_type = "t2.micro"
  tags = {
    Name = each.value
  }
}
```
✔ Creates instances dynamically based on a list of names.

---

### **Example: Conditional Expressions (`? :` Operator)**
```hcl
variable "env" {
  default = "dev"
}

output "instance_type" {
  value = var.env == "prod" ? "t3.large" : "t2.micro"
}
```
✔ Chooses instance type based on the environment.

---

# **🎯 Summary**
✅ **Provisioners** automate scripts (local & remote).  
✅ **Data Sources** fetch existing AWS resources.  
✅ **Dynamic Blocks** optimize configurations.  
✅ **Functions & Conditionals** make Terraform smarter.  

🚀 **Next Steps** – Terraform Modules & CI/CD Integration! 😎