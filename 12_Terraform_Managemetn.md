# 🚀 **Terraform Security & Secret Management Best Practices**  

Terraform ka **Security, Secret Management aur IAM Policy Best Practices** cover karenge. ⚡  

---

# **1️⃣ Secret Management with AWS Secrets Manager & SSM Parameter Store**  

Terraform se **AWS Secrets Manager aur SSM Parameter Store** integrate karenge taki **secrets (DB password, API keys, credentials) securely store ho sakein**. 🛡️  

---

### **🛠 AWS Secrets Manager – Store & Retrieve Secrets**  

### **📌 Steps:**  
✅ **Terraform se AWS Secrets Manager Resource Create**  
✅ **Secret Store karna (DB Password, API Key)**  
✅ **Secrets Read & Use in Terraform**  

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
✔ **Secrets securely store ho gaye!** 🔐  

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
✔ **Terraform ab secrets securely use kar sakta hai!** 🔑  

---

## **🛠 SSM Parameter Store – Store & Retrieve Parameters**  
```hcl
resource "aws_ssm_parameter" "db_password" {
  name  = "/prod/db/password"
  type  = "SecureString"
  value = "SuperSecurePassword123!"
}
```
✔ **AWS SSM Parameter Store bhi use kar sakte hain for secure storage!** 🔒  

---

# **2️⃣ IAM Policy & Role Management Best Practices**
AWS IAM **secure karne ke liye** Terraform ka best practice use karenge.  

### **📌 Best Practices:**  
✅ **Principle of Least Privilege (Minimum Access Grant karo)**  
✅ **IAM Policies ko Reuse karo (Avoid Hardcoding)**  
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
✔ **Only EC2 ko ye role assume karne ki permission di gayi hai!** 🔐  

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
✔ **S3 ka sirf read-only access diya gaya hai!** 🛡️  

---

# **3️⃣ Terraform Security Scanning Tools (tfsec, checkov)**  

Infrastructure-as-Code (IaC) security ke liye **tfsec & checkov** ka use karenge jo Terraform code ke security vulnerabilities ko detect karte hain.  

### **📌 Steps:**  
✅ **tfsec & checkov Install karna**  
✅ **Terraform Security Scan Run karna**  
✅ **Security Issues Fix karna**  

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
✔ **Security Issues mil gaye!** 🔍  

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
✔ **checkov ne Terraform ke security vulnerabilities detect kiye!** 🛡️  

---

# **🎯 Summary**
✅ **AWS Secrets Manager & SSM se Secure Secrets Storage**  
✅ **IAM Policy & Role Best Practices Implement**  
✅ **tfsec & checkov se Terraform Code Security Scan**  

🚀 **Next Steps:**  
🔹 **Terraform Remote State Encryption**  
🔹 **Kubernetes Security with Terraform**  
🔹 **CloudTrail & CloudWatch for Terraform Auditing**  

🔥 **Terraform Security & Secret Management Done!** 🚀