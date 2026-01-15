# 🌐 Three-Tier Web Application Infrastructure on AWS

## 📌 Overview

This project demonstrates the design and deployment of a **secure, highly available, and scalable three-tier web application infrastructure on AWS**.  

The solution follows AWS best practices for **network isolation, security, automation, and fault tolerance**, and is suitable for hosting a production-grade **dynamic web application**.

The architecture is divided into:

- **Presentation Tier** – Application Load Balancer
- **Application Tier** – Auto-scaled EC2 web servers
- **Data Tier** – Amazon RDS (MySQL)

---

## ☁️ AWS Services Used

- Amazon VPC
- Amazon EC2
- Application Load Balancer (ALB)
- Amazon RDS (MySQL)
- Amazon Route 53
- Amazon S3
- AWS IAM
- AWS Secrets Manager
- EC2 Instance Connect Endpoint
- NAT Gateway
- Internet Gateway

---

## 🏗️ Architecture Design

- Multi-AZ deployment for high availability
- Public and private subnet separation
- No public access to application or database instances
- Secure SSH access using EC2 Instance Connect Endpoint
- Load-balanced and auto-scaled application layer

---

## 🌐 Networking (VPC)

A custom VPC was created with resources distributed across **two Availability Zones**.

### **Subnets**

- **2 Public Subnets**
  - Application Load Balancer
  - NAT Gateways
- **2 Private Application Subnets**
  - EC2 Web Servers (Auto Scaling Group)
- **2 Private Database Subnets**
  - Amazon RDS (Primary & Standby)

### **Connectivity**

- **Internet Gateway** provides internet access to public subnets
- **NAT Gateways** allow private subnets outbound internet access
- Private subnets have **no inbound internet access**

---

## 🔐 Security Configuration

Five security groups were created to enforce least-privilege access:

- **EC2 Instance Connect Endpoint Security Group**
  - Secure SSH access to private EC2 instances

- **Application Load Balancer Security Group**
  - Inbound: HTTP (80), HTTPS (443)

- **Web Server Security Group**
  - Inbound HTTP/HTTPS from ALB only
  - SSH access from EC2 Instance Connect Endpoint

- **Database Migration Security Group**
  - SSH access for database migration tasks

- **Database Security Group**
  - MySQL (3306) access from:
    - Web server security group
    - Database migration security group

---

## 🔑 IAM & Secrets Management

- IAM roles and policies were attached to EC2 instances
- Permissions granted for:
  - Retrieving application files from Amazon S3
  - Accessing database credentials from AWS Secrets Manager
- Database credentials are stored securely with **no hard-coded secrets**

---

## 📦 Application Storage (S3)

An Amazon S3 bucket was created to store:

- Application source files (ZIP archives)
- Database migration SQL scripts
- Deployment assets

EC2 instances retrieve required files securely using IAM roles.

---

## 🗄️ Database Layer (RDS)

- Amazon RDS (MySQL) deployed in private database subnets
- Multi-AZ configuration for high availability
- Database is not publicly accessible
- Access restricted via security groups

---

## 🔄 Database Migration

A temporary EC2 instance (`db-migrate`) was used to migrate data into RDS:

1. Instance accessed securely using **EC2 Instance Connect Endpoint**
2. A bootstrap Bash script executed to:
   - Update system packages
   - Install required dependencies
   - Retrieve database credentials from AWS Secrets Manager
   - Download migration SQL files from Amazon S3
3. **Flyway** was installed and used to apply database migrations
4. After migration, the instance was no longer required

---

## ⚙️ Application Deployment

- EC2 web servers were configured using a bootstrap Bash script
- The script:
  - Installs required runtime and dependencies
  - Downloads application files from S3
  - Configures the web server environment

---

## ⚖️ Load Balancing

- A **Target Group** was created to forward traffic on port 80
- An **Application Load Balancer** was configured to:
  - Listen on HTTP (80) and HTTPS (443)
  - Distribute traffic across healthy EC2 instances in multiple AZs

---

## <span style="color: #059669">📈 Scalability & Automation</span>

### **Amazon Machine Image (AMI)**

- A custom AMI was created from a fully configured EC2 instance
- Ensures consistent and repeatable deployments

### **Launch Template**

- Defines:
  - AMI
  - Instance type
  - Security groups
  - IAM role
  - User data script

### **Auto Scaling Group**

- Automatically launches and terminates EC2 instances
- Maintains high availability across both AZs
- Enables horizontal scaling based on demand

---

## 🌍 DNS & Traffic Routing

- A custom domain was configured in **Amazon Route 53**
- DNS records route traffic directly to the Application Load Balancer
- Provides reliable and highly available access to the application

---

## ✅ Key Outcomes

- Designed and deployed a **production-ready three-tier architecture**
- Implemented secure private networking and access control
- Automated deployment and scaling of application servers
- Applied AWS best practices for availability, security, and maintainability

---

## 📌 Project Purpose

This project showcases real-world cloud engineering skills, including:

- Infrastructure design
- Network and security architecture
- Automation and scalability
- Operational reliability on AWS
