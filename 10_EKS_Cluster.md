# 🚀 **AWS EKS Cluster Setup with Terraform & Kubernetes Deployment**  

We will **automate AWS EKS (Elastic Kubernetes Service) using Terraform**, where we will deploy a **Kubernetes cluster, Pods & Services, and Helm charts**. ⚡  

---

## **1️⃣ AWS EKS Cluster Setup with Terraform**  

### **📌 Steps:**  
1️⃣ **VPC & Subnets** – Setting up networking for EKS  
2️⃣ **IAM Roles & Policies** – Granting AWS access to Kubernetes  
3️⃣ **EKS Cluster Creation** – Managed control plane  
4️⃣ **Node Group (EC2 Instances)** – Worker nodes for Kubernetes  

---

### **🛠 Step 1: VPC & Subnets for EKS**
```hcl
resource "aws_vpc" "eks_vpc" {
  cidr_block = "10.0.0.0/16"
  enable_dns_support = true
  enable_dns_hostnames = true
  tags = { Name = "EKS-VPC" }
}

resource "aws_subnet" "eks_subnet_1" {
  vpc_id            = aws_vpc.eks_vpc.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = "us-east-1a"
  tags = { Name = "EKS-Subnet-1" }
}

resource "aws_subnet" "eks_subnet_2" {
  vpc_id            = aws_vpc.eks_vpc.id
  cidr_block        = "10.0.2.0/24"
  availability_zone = "us-east-1b"
  tags = { Name = "EKS-Subnet-2" }
}
```
✔ **Networking ready!** 🎯  

---

### **🛠 Step 2: IAM Role for EKS Cluster**
```hcl
resource "aws_iam_role" "eks_cluster_role" {
  name = "eks-cluster-role"
  assume_role_policy = jsonencode({
    Statement = [{
      Effect = "Allow"
      Principal = { Service = "eks.amazonaws.com" }
      Action = "sts:AssumeRole"
    }]
  })
}

resource "aws_iam_role_policy_attachment" "eks_policy" {
  role       = aws_iam_role.eks_cluster_role.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonEKSClusterPolicy"
}
```
✔ **IAM Role created for EKS!** 🔥  

---

### **🛠 Step 3: Create EKS Cluster**
```hcl
resource "aws_eks_cluster" "eks" {
  name     = "my-eks-cluster"
  role_arn = aws_iam_role.eks_cluster_role.arn
  vpc_config {
    subnet_ids = [aws_subnet.eks_subnet_1.id, aws_subnet.eks_subnet_2.id]
  }
}
```
✔ **EKS Cluster created!** 🚀  

---

### **🛠 Step 4: Create Worker Nodes (Node Group)**
```hcl
resource "aws_eks_node_group" "eks_nodes" {
  cluster_name    = aws_eks_cluster.eks.name
  node_role_arn   = aws_iam_role.eks_cluster_role.arn
  subnet_ids      = [aws_subnet.eks_subnet_1.id, aws_subnet.eks_subnet_2.id]
  scaling_config {
    desired_size = 2
    min_size     = 1
    max_size     = 3
  }
}
```
✔ **Worker nodes ready for Pods deployment!** ⚡  

---

## **2️⃣ Deploying Pods & Services on EKS using Terraform**  
We will create a simple **Nginx Deployment & Service** inside the EKS cluster.  

### **📌 Steps:**  
✅ **Setup Kubernetes Provider in Terraform**  
✅ **Deployment (Pods running Nginx)**  
✅ **Service (LoadBalancer for external access)**  

---

### **🛠 Step 1: Configure Kubernetes Provider**
```hcl
provider "kubernetes" {
  host                   = aws_eks_cluster.eks.endpoint
  token                  = data.aws_eks_cluster_auth.eks.token
  cluster_ca_certificate = base64decode(aws_eks_cluster.eks.certificate_authority.0.data)
}
```
✔ **Terraform can now interact with Kubernetes!** ✅  

---

### **🛠 Step 2: Deploy Nginx Pods**
```hcl
resource "kubernetes_deployment" "nginx" {
  metadata {
    name = "nginx"
    labels = { app = "nginx" }
  }
  spec {
    replicas = 2
    selector { match_labels = { app = "nginx" } }
    template {
      metadata { labels = { app = "nginx" } }
      spec {
        container {
          image = "nginx:latest"
          name  = "nginx"
          port {
            container_port = 80
          }
        }
      }
    }
  }
}
```
✔ **Nginx Pods deployed!** 🚀  

---

### **🛠 Step 3: Expose Nginx with LoadBalancer**
```hcl
resource "kubernetes_service" "nginx_service" {
  metadata {
    name = "nginx-service"
  }
  spec {
    selector = { app = "nginx" }
    port {
      port        = 80
      target_port = 80
    }
    type = "LoadBalancer"
  }
}
```
✔ **Nginx accessible on public IP!** 🌍  

---

## **3️⃣ Helm & Terraform – Kubernetes App Deployment**  
Deploying **Kubernetes applications using Helm** and managing them with Terraform.  

### **📌 Steps:**  
✅ **Helm Provider Setup**  
✅ **Install Nginx Ingress Controller using Helm**  
✅ **Deploy Applications with Helm Charts**  

---

### **🛠 Step 1: Install Helm Provider**
```hcl
provider "helm" {
  kubernetes {
    host                   = aws_eks_cluster.eks.endpoint
    token                  = data.aws_eks_cluster_auth.eks.token
    cluster_ca_certificate = base64decode(aws_eks_cluster.eks.certificate_authority.0.data)
  }
}
```
✔ **Helm ready for deployments!** 🚀  

---

### **🛠 Step 2: Install Nginx Ingress using Helm**
```hcl
resource "helm_release" "nginx_ingress" {
  name       = "nginx-ingress"
  repository = "https://kubernetes.github.io/ingress-nginx"
  chart      = "ingress-nginx"
  namespace  = "default"

  set {
    name  = "controller.service.type"
    value = "LoadBalancer"
  }
}
```
✔ **Ingress Controller installed!** 🌍  

---

### **🛠 Step 3: Deploy Application with Helm Chart**
```hcl
resource "helm_release" "my_app" {
  name       = "my-app"
  repository = "https://my-helm-repo.com"
  chart      = "my-app-chart"
  namespace  = "default"
}
```
✔ **Helm-based application deployed!** 🎯  

---

# **🎯 Summary**
✅ **AWS EKS Cluster Setup with Terraform**  
✅ **Deploying Pods & Services using Terraform**  
✅ **Kubernetes App Deployment using Helm & Terraform**  

🚀 **Next Steps:**  
🔹 **Terraform State Management (Remote Backend)**  
🔹 **CI/CD Pipeline for Kubernetes Deployment**  
🔹 **Prometheus & Grafana Monitoring using Helm**  

🔥 **Automate Everything with Terraform!** 🚀
