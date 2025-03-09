# **🚀 AWS Infrastructure with Terraform**  

## **1️⃣ AWS VPC, Subnets, and Route Tables using Terraform**  
Creating a **custom VPC** with subnets, route tables, and an internet gateway.

### **📌 Steps:**  
1️⃣ **Create a VPC**  
2️⃣ **Define Public & Private Subnets**  
3️⃣ **Create an Internet Gateway & Route Tables**  
4️⃣ **Associate Subnets with Route Tables**  

### **VPC & Subnets Configuration**
```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_vpc" "my_vpc" {
  cidr_block = "10.0.0.0/16"
  tags = { Name = "MyVPC" }
}

resource "aws_subnet" "public_subnet" {
  vpc_id                  = aws_vpc.my_vpc.id
  cidr_block              = "10.0.1.0/24"
  map_public_ip_on_launch = true
  availability_zone       = "us-east-1a"
  tags = { Name = "Public Subnet" }
}

resource "aws_subnet" "private_subnet" {
  vpc_id                  = aws_vpc.my_vpc.id
  cidr_block              = "10.0.2.0/24"
  availability_zone       = "us-east-1b"
  tags = { Name = "Private Subnet" }
}
```

### **Route Table & Internet Gateway**
```hcl
resource "aws_internet_gateway" "gw" {
  vpc_id = aws_vpc.my_vpc.id
  tags = { Name = "Internet Gateway" }
}

resource "aws_route_table" "public_rt" {
  vpc_id = aws_vpc.my_vpc.id
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.gw.id
  }
  tags = { Name = "Public Route Table" }
}

resource "aws_route_table_association" "public_assoc" {
  subnet_id      = aws_subnet.public_subnet.id
  route_table_id = aws_route_table.public_rt.id
}
```
✔ **VPC with Internet access is ready!** 🎉

---

## **2️⃣ Security Groups & IAM Roles Management**  
Setting up **Security Groups** for EC2 and creating an **IAM Role**.

### **Security Group (Allow SSH & HTTP)**
```hcl
resource "aws_security_group" "web_sg" {
  vpc_id = aws_vpc.my_vpc.id
  name   = "web-sg"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

### **IAM Role for EC2**
```hcl
resource "aws_iam_role" "ec2_role" {
  name = "ec2-role"
  assume_role_policy = jsonencode({
    Statement = [{
      Effect = "Allow"
      Principal = { Service = "ec2.amazonaws.com" }
      Action = "sts:AssumeRole"
    }]
  })
}

resource "aws_iam_instance_profile" "ec2_profile" {
  name = "ec2-profile"
  role = aws_iam_role.ec2_role.name
}
```
✔ **EC2 instances can now assume the IAM role!** 🎯

---

## **3️⃣ AWS RDS, DynamoDB & Elastic Load Balancer Setup**  
Deploying a **managed database (RDS)**, **NoSQL (DynamoDB)**, and an **Elastic Load Balancer**.

### **RDS Database (MySQL)**
```hcl
resource "aws_db_instance" "rds" {
  identifier             = "mydb"
  allocated_storage      = 20
  engine                = "mysql"
  instance_class        = "db.t3.micro"
  username             = "admin"
  password             = "password123"
  publicly_accessible   = false
  vpc_security_group_ids = [aws_security_group.web_sg.id]
  db_subnet_group_name   = aws_db_subnet_group.rds_subnet_group.name
}

resource "aws_db_subnet_group" "rds_subnet_group" {
  name       = "rds_subnet_group"
  subnet_ids = [aws_subnet.private_subnet.id]
}
```

✔ **MySQL RDS database ready!** 🎯

### **DynamoDB Table**
```hcl
resource "aws_dynamodb_table" "users" {
  name         = "UsersTable"
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "user_id"

  attribute {
    name = "user_id"
    type = "S"
  }
}
```
✔ **DynamoDB Table created!** 🔥

### **Application Load Balancer**
```hcl
resource "aws_lb" "web_alb" {
  name               = "web-alb"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.web_sg.id]
  subnets           = [aws_subnet.public_subnet.id]
}

resource "aws_lb_target_group" "web_tg" {
  name     = "web-tg"
  port     = 80
  protocol = "HTTP"
  vpc_id   = aws_vpc.my_vpc.id
}

resource "aws_lb_listener" "web_listener" {
  load_balancer_arn = aws_lb.web_alb.arn
  port             = 80
  protocol         = "HTTP"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.web_tg.arn
  }
}
```
✔ **ALB distributes traffic between instances!** ⚡

---

## **4️⃣ Auto Scaling Groups (ASG) with Terraform**  
Automatically scales EC2 instances based on CPU usage.

### **Launch Template**
```hcl
resource "aws_launch_template" "web_lt" {
  name_prefix   = "web-"
  image_id      = "ami-12345678"
  instance_type = "t2.micro"
  key_name      = "my-key"

  network_interfaces {
    associate_public_ip_address = true
    security_groups             = [aws_security_group.web_sg.id]
  }
}
```

### **Auto Scaling Group**
```hcl
resource "aws_autoscaling_group" "web_asg" {
  desired_capacity     = 2
  max_size            = 5
  min_size            = 1
  vpc_zone_identifier = [aws_subnet.public_subnet.id]

  launch_template {
    id      = aws_launch_template.web_lt.id
    version = "$Latest"
  }
}
```

### **Auto Scaling Policy (Scale up when CPU > 60%)**
```hcl
resource "aws_autoscaling_policy" "scale_up" {
  name                   = "scale-up"
  scaling_adjustment     = 1
  adjustment_type        = "ChangeInCapacity"
  cooldown              = 60
  autoscaling_group_name = aws_autoscaling_group.web_asg.name
}
```
✔ **Auto Scaling handles traffic spikes dynamically!** 🚀

---

# **🎯 Summary**
✅ **VPC Setup** – Public & Private subnets, Internet Gateway, Route Tables  
✅ **Security Groups & IAM Roles** – Secure EC2 & manage permissions  
✅ **AWS Services** – RDS (MySQL), DynamoDB (NoSQL), ALB (Traffic Distribution)  
✅ **Auto Scaling Group** – Dynamically scales EC2 instances  

💡 **Next Steps:**  
🔹 Terraform **Modules** for reusable code  
🔹 **CI/CD Pipeline** using Terraform & GitHub Actions  
🔹 Terraform **State Management & Remote Backends**  

**🔥 Keep Automating with Terraform! 🚀**