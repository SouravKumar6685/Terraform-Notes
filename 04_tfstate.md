# Terraform State (tfstate) – Local & Remote

![Meme](https://i.imgflip.com/4/4t0m5.jpg)  
*"When you realize Terraform state is the source of truth!"* 😂

---

## 1️⃣ What is Terraform State? 📜
Terraform **state** is a file (`terraform.tfstate`) that tracks your deployed infrastructure. It helps Terraform understand what exists in the cloud and what needs to change.

### **Why is State Important?**
- Keeps track of resources Terraform manages.
- Enables dependency resolution between resources.
- Helps in team collaboration (when using remote state).

---

## 2️⃣ Local vs Remote State 🌍
### **🔹 Local State (Default) 🔹**
By default, Terraform stores the state **locally** in your project directory.
```sh
terraform apply  # Generates terraform.tfstate locally
```
📂 This creates a `terraform.tfstate` file that records infrastructure details.

🔎 **AWS Navigation:**
- No specific AWS navigation needed since state is stored locally.

---
### **🔹 Remote State (for Teams) 🔹**
Remote state is useful when multiple team members work on the same Terraform project. You can store it in **AWS S3** for collaboration.

```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state-bucket"
    key    = "terraform.tfstate"
    region = "us-east-1"
  }
}
```
This config tells Terraform to store the state in **AWS S3** instead of a local file.

🔎 **AWS Navigation:**
1. Open **AWS Console** → **S3**
2. Find the bucket `"my-terraform-state-bucket"`
3. Locate the `terraform.tfstate` file.

---

# Terraform Commands (init, plan, apply, destroy)

## 1️⃣ Essential Terraform Commands 🚀
| Command | Description |
|---------|-------------|
| `terraform init` | Initializes Terraform project and downloads providers. |
| `terraform plan` | Shows what changes Terraform will make. |
| `terraform apply` | Deploys infrastructure as per the configuration. |
| `terraform destroy` | Destroys all Terraform-managed resources. |

### **Command Usage with AWS**
#### 🛠️ **1. Initialize Terraform**
```sh
terraform init
```
🔹 This downloads AWS provider plugins and prepares Terraform.

#### 🔎 **AWS Navigation:**
No AWS navigation needed. This just sets up your local Terraform environment.

---

#### 📋 **2. Plan Infrastructure Changes**
```sh
terraform plan
```
🔹 Shows what resources will be created, updated, or destroyed.

#### 🔎 **AWS Navigation:**
No changes are made yet, so no AWS verification needed.

---

#### 🚀 **3. Apply Configuration**
```sh
terraform apply -auto-approve
```
🔹 Deploys the resources as per the Terraform script.

#### 🔎 **AWS Navigation:**
1. Open **AWS Console** → **EC2**
2. Check if instances are created as expected.

---

#### 💀 **4. Destroy Infrastructure**
```sh
terraform destroy -auto-approve
```
🔹 Removes all Terraform-managed infrastructure.

#### 🔎 **AWS Navigation:**
1. Open **AWS Console** → **EC2**
2. Confirm the instances are deleted.

---

## 🎯 **Conclusion**
✅ **Terraform state** helps keep track of your infrastructure.  
✅ **Local state** is fine for individuals; **remote state** is better for teams.  
✅ **Terraform commands** (`init`, `plan`, `apply`, `destroy`) are essential for managing resources.  

Now, go deploy something cool with Terraform! 🚀

