# 🚀 **Terraform Security & Secret Management Best Practices**  

We'll cover **Security, Secret Management, and IAM Policy Best Practices** in Terraform. ⚡  

---

# **1️⃣ Secret Management with AWS Secrets Manager & SSM Parameter Store**  

We'll integrate **AWS Secrets Manager and SSM Parameter Store** with Terraform to **securely store secrets (DB passwords, API keys, credentials, etc.)** 🛡️  

---

### **🛠 AWS Secrets Manager – Store & Retrieve Secrets**  

### **📌 Steps:**  
✅ **Create AWS Secrets Manager Resource using Terraform**  
✅ **Store Secrets (DB Password, API Key, etc.)**  
✅ **Read & Use Secrets in Terraform**  

---

### **🛠 Step 1: Define Secrets in AWS Secrets Manager**  
```hcl
resource "aws_secretsmanager_secret" "db_secret" {
  name = "database_password"
}

resource "aws_secretsmanager_secret_version" "db_secret_version" {
  secret_id     = aws_secretsmanager_secret.db_secret.id
  secret_string = jsonencode({
    username = "admin"
    password = "SuperSecurePassword123!"
  })
}
```
✔ **Secrets are securely stored!** 🔐  

---

### **🛠 Step 2: Fetch Secret in Terraform**  
```hcl
data "aws_secretsmanager_secret" "db_secret" {
  name = "database_password"
}

data "aws_secretsmanager_secret_version" "db_secret_version" {
  secret_id = data.aws_secretsmanager_secret.db_secret.id
}

output "db_password" {
  value     = nonsensitive(jsondecode(data.aws_secretsmanager_secret_version.db_secret_version.secret_string)["password"])
  sensitive = true
}
```
✔ **Terraform can now securely use secrets!** 🔑  

---

## **🛠 SSM Parameter Store – Store & Retrieve Parameters**  
```hcl
resource "aws_ssm_parameter" "db_password" {
  name  = "/prod/db/password"
  type  = "SecureString"
  value = "SuperSecurePassword123!"
}
```
✔ **AWS SSM Parameter Store can also be used for secure storage!** 🔒  

---

# **2️⃣ IAM Policy & Role Management Best Practices**  
We'll use Terraform best practices to **secure AWS IAM**.  

### **📌 Best Practices:**  
✅ **Follow the Principle of Least Privilege (Grant Minimum Access)**  
✅ **Reuse IAM Policies (Avoid Hardcoding)**  
✅ **Attach Policies to Groups Instead of Users**  
✅ **Use Managed Policies Instead of Inline Policies**  
✅ **Rotate IAM Access Keys Regularly**  

---

### **🛠 IAM Role with Least Privilege**  
```hcl
resource "aws_iam_role" "app_role" {
  name = "app_role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect = "Allow"
      Principal = { Service = "ec2.amazonaws.com" }
      Action = "sts:AssumeRole"
    }]
  })
}
```
✔ **Only EC2 is allowed to assume this role!** 🔐  

---

### **🛠 Secure IAM Policy – Minimum Required Permissions**  
```hcl
resource "aws_iam_policy" "s3_readonly" {
  name        = "S3ReadOnlyPolicy"
  description = "Allow read-only access to S3"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect   = "Allow"
      Action   = ["s3:GetObject", "s3:ListBucket"]
      Resource = ["arn:aws:s3:::my-bucket", "arn:aws:s3:::my-bucket/*"]
    }]
  })
}
```
✔ **Only read-only access to S3 is granted!** 🛡️  

---

# **3️⃣ Terraform Security Scanning Tools (tfsec, checkov)**  

For Infrastructure-as-Code (IaC) security, we'll use **tfsec & checkov** to detect security vulnerabilities in Terraform code.  

### **📌 Steps:**  
✅ **Install tfsec & checkov**  
✅ **Run Terraform Security Scan**  
✅ **Fix Security Issues**  

---

### **🛠 Step 1: Install tfsec**  
```bash
brew install tfsec  # macOS
choco install tfsec  # Windows
curl -s https://raw.githubusercontent.com/aquasecurity/tfsec/master/scripts/install_linux.sh | bash  # Linux
```
✔ **tfsec installed!** ✅  

---

### **🛠 Step 2: Scan Terraform Code with tfsec**  
```bash
tfsec .
```
✔ **Security Issues Detected!** 🔍  

---

### **🛠 Step 3: Install checkov for Deep Security Scan**  
```bash
pip install checkov
```
✔ **checkov installed!** ✅  

---

### **🛠 Step 4: Run checkov Security Scan**  
```bash
checkov -d .
```
✔ **checkov detected security vulnerabilities in Terraform!** 🛡️  

---

# **🎯 Summary**  
✅ **Secure Secrets Storage using AWS Secrets Manager & SSM**  
✅ **Implement IAM Policy & Role Best Practices**  
✅ **Perform Terraform Code Security Scan using tfsec & checkov**  

🚀 **Next Steps:**  
🔹 **Terraform Remote State Encryption**  
🔹 **Kubernetes Security with Terraform**  
🔹 **CloudTrail & CloudWatch for Terraform Auditing**  

🔥 **Terraform Security & Secret Management Done!** 🚀
