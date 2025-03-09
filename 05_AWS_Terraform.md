# Terraform AWS Setup Guide

![Meme](https://i.imgflip.com/4/4t0m5.jpg)  
*"When you realize managing AWS manually is too much work, so you let Terraform do it for you!"* 😂

---

## 1️⃣ Terraform Provider Setup for AWS ⚙️
To connect Terraform with AWS, it is necessary to define a **provider**.

### **Example: AWS Provider Setup**
```hcl
provider "aws" {
  region = "us-east-1"
}
```
🔹 This code specifies that we will create resources in the `us-east-1` region.

### **AWS Navigation:**
Check **AWS Console → IAM → Users** to verify API access.

---

## 2️⃣ AWS Authentication with Terraform 🔑
There are multiple ways to authenticate with AWS:

### **Method 1: Environment Variables (Recommended)**
```sh
export AWS_ACCESS_KEY_ID="your-access-key"
export AWS_SECRET_ACCESS_KEY="your-secret-key"
```

### **Method 2: Terraform Configuration File**
```hcl
provider "aws" {
  access_key = "your-access-key"
  secret_key = "your-secret-key"
  region     = "us-east-1"
}
```

### **AWS Navigation:**
Go to **AWS Console → IAM → Users → Security Credentials** to create keys.

---

## 3️⃣ Launching an AWS EC2 Instance with Terraform 🖥️
A simple example of creating an EC2 instance:

```hcl
resource "aws_instance" "my_ec2" {
  ami           = "ami-0c55b159cbfafe1f0"  # Amazon Linux AMI
  instance_type = "t2.micro"
  tags = {
    Name = "MyTerraformInstance"
  }
}
```

### **AWS Navigation:**
After running `terraform apply`, check **AWS Console → EC2 → Instances**.

---

## 4️⃣ Creating an AWS S3 Bucket using Terraform 🗄️
A simple example of creating an S3 bucket:

```hcl
resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-terraform-bucket"
  acl    = "private"
}
```

### **AWS Navigation:**
After running `terraform apply`, go to **AWS Console → S3** and check your bucket.

---

## 5️⃣ Terraform Outputs & Formatting 📜
Retrieving useful outputs from Terraform is important.

### **Example: Getting EC2 Public IP**
```hcl
output "instance_ip" {
  value = aws_instance.my_ec2.public_ip
}
```

### **AWS Navigation:**
Run `terraform output instance_ip` to get the IP and check it in **AWS Console → EC2 → Instances**.

---

## 🎯 **Conclusion**
✅ **Terraform Provider** is essential to connect with AWS.  
✅ **Authentication** can be done via environment variables or config files.  
✅ **EC2 & S3** can be easily created using Terraform.  
✅ **Outputs & Formatting** help retrieve infrastructure details.  


