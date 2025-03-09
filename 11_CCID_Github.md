# 🚀 **Terraform CI/CD with GitHub Actions & Jenkins**  

Terraform ko **GitHub Actions ya Jenkins** ke saath automate karenge, jisme **Linting, Code Formatting, Drift Detection, and Auto Fixes** bhi include honge. ⚡  

---

## **1️⃣ Terraform with GitHub Actions**
GitHub Actions se Terraform **plan, apply, linting & state management** automate karenge.

### **📌 Steps:**  
✅ **GitHub Actions Workflow likhna**  
✅ **Terraform Linting & Formatting**  
✅ **Terraform Plan & Apply Automation**  
✅ **Terraform State Management with S3 & DynamoDB**  

---

### **🛠 Step 1: Create `.github/workflows/terraform.yml`**
```yaml
name: Terraform CI/CD

on:
  push:
    branches:
      - main
  pull_request:

jobs:
  terraform:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v2

      - name: Terraform Format
        run: terraform fmt -check -diff

      - name: Terraform Init
        run: terraform init

      - name: Terraform Validate
        run: terraform validate

      - name: Terraform Plan
        run: terraform plan -out=tfplan

      - name: Terraform Apply
        if: github.ref == 'refs/heads/main'
        run: terraform apply -auto-approve tfplan
```
✔ **GitHub Actions will now run Terraform workflows on every commit!** 🚀  

---

## **2️⃣ Terraform with Jenkins**
Jenkins pipeline likhenge jo **Terraform ko automate** karega.

### **📌 Steps:**  
✅ **Jenkins Pipeline Create**  
✅ **Terraform Linting & Validation**  
✅ **Terraform Plan & Apply**  
✅ **Terraform State Store in S3**  

---

### **🛠 Step 1: Create Jenkinsfile**
```groovy
pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-key')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/your-repo/terraform-infra.git'
            }
        }

        stage('Terraform Init') {
            steps {
                sh 'terraform init'
            }
        }

        stage('Terraform Format & Validate') {
            steps {
                sh 'terraform fmt -check'
                sh 'terraform validate'
            }
        }

        stage('Terraform Plan') {
            steps {
                sh 'terraform plan -out=tfplan'
            }
        }

        stage('Terraform Apply') {
            when {
                branch 'main'
            }
            steps {
                sh 'terraform apply -auto-approve tfplan'
            }
        }
    }
}
```
✔ **Jenkins will now handle Terraform automation!** 🎯  

---

## **3️⃣ Terraform Linting & Code Formatting**
Terraform code **best practices follow kare, formatting sahi ho, aur errors detect ho**.  

### **📌 Steps:**  
✅ **Linting with `tflint`**  
✅ **Code Formatting with `terraform fmt`**  
✅ **Validation with `terraform validate`**  

---

### **🛠 Step 1: Install TFLint**
```bash
curl -s https://raw.githubusercontent.com/terraform-linters/tflint/master/install.sh | bash
```

### **🛠 Step 2: Run Linting**
```bash
tflint --init
tflint
```
✔ **Code linting ready!** ✅  

---

### **🛠 Step 3: Auto Format Code**
```bash
terraform fmt -recursive
```
✔ **Code formatted properly!** 🎯  

---

## **4️⃣ Terraform Drift Detection & Automated Fixes**
Drift detection **detect karega agar infrastructure manually change ho jaye** aur **auto-fix karne ka process setup karenge**.  

### **📌 Steps:**  
✅ **Detect Infrastructure Drift**  
✅ **Auto Fix Drifts with Terraform Apply**  

---

### **🛠 Step 1: Check for Drift**
```bash
terraform refresh
terraform plan
```
✔ **Drift detection ready!** 🔍  

---

### **🛠 Step 2: Automate Fixing with GitHub Actions**
```yaml
- name: Check Terraform Drift
  run: terraform plan -detailed-exitcode || echo "Terraform Drift Detected!"

- name: Auto Fix Drift
  if: failure()
  run: terraform apply -auto-approve
```
✔ **Drift detected & fixed automatically!** 🔥  

---

# **🎯 Summary**
✅ **GitHub Actions se Terraform Automate**  
✅ **Jenkins Pipeline se Terraform Deployment**  
✅ **Linting, Formatting, & Best Practices**  
✅ **Terraform Drift Detection & Auto Fixes**  

🚀 **Next Steps:**  
🔹 **Terraform Remote Backend with S3 & DynamoDB**  
🔹 **CI/CD for Kubernetes with Terraform**  
🔹 **Security Best Practices for Terraform**  

🔥 **Terraform Full Automation Done!** 🚀