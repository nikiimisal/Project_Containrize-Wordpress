# 🐳 WordPress Containerization – Two-Tier Architecture (Docker Mini Project)

This document explains how to deploy **WordPress with MySQL using Docker containers** on an **AWS EC2 instance**.
The project is beginner-friendly and designed for **hands-on DevOps practice & interviews**.

---

## 📌 Project Objective

* Launch an EC2 instance for a mini project
* Deploy **MySQL (Database Tier)** using Docker
* Deploy **WordPress (Application Tier)** using Docker
* Connect WordPress with MySQL using environment variables
* Understand container-based **Two-Tier Architecture**

---

## 🏗 Architecture Overview (Two-Tier)

```
User (Browser)
     |
     v
WordPress Container (App Tier)
     |
     v
MySQL Container (DB Tier)
```

---

## ⚙️ Infrastructure Setup

### Step 1️⃣ Launch EC2 Instance

* Instance Type: `t2.micro`
* OS: Amazon Linux 2
* Security Group:

  * Allow **SSH (22)**
  * Allow **HTTP (80)**

> 🔹 Best Practice:
> EC2 can be launched using **Terraform / Jenkins**.
> For better understanding, name the instance:
> **`Ansible-Docker-WordPress`**

---

## 🔐 Server Login & Basic Setup

```bash
ssh -i key.pem ec2-user@<public-ip>
```

### Change Hostname (Optional – for clarity)

```bash
sudo hostnamectl set-hostname wordpress-server
exec bash
```

### Switch to Root User

```bash
sudo -i
```

---

## 🐳 Install Docker

```bash
yum update -y
yum install docker -y
systemctl start docker
systemctl enable docker
```

Verify:

```bash
docker --version
```

---

## 🗄 MySQL Container Setup (Database Tier)

### Step 1️⃣ Pull MySQL Image from Docker Hub

```bash
docker pull mysql:5.7
```

### Step 2️⃣ Run MySQL Container

```bash
docker run -d \
--name mysql-container \
-e MYSQL_ROOT_PASSWORD=rootpass \
-e MYSQL_DATABASE=wordpressdb \
-e MYSQL_USER=wpuser \
-e MYSQL_PASSWORD=wppass \
mysql:5.7
```

### Environment Variables Explanation

* `MYSQL_ROOT_PASSWORD` → Root password
* `MYSQL_DATABASE` → WordPress database
* `MYSQL_USER` → WordPress DB user
* `MYSQL_PASSWORD` → WordPress DB password

Verify:

```bash
docker ps
```

---

## 🌐 WordPress Container Setup (Application Tier)

### Step 1️⃣ Pull WordPress Image

```bash
docker pull wordpress:latest
```

### Step 2️⃣ Run WordPress Container (Linked with MySQL)

```bash
docker run -d \
--name wordpress-container \
-p 80:80 \
--link mysql-container:mysql \
-e WORDPRESS_DB_HOST=mysql \
-e WORDPRESS_DB_USER=wpuser \
-e WORDPRESS_DB_PASSWORD=wppass \
-e WORDPRESS_DB_NAME=wordpressdb \
wordpress:latest
```

### WordPress Environment Variables

* `WORDPRESS_DB_HOST` → MySQL container name
* `WORDPRESS_DB_USER` → DB username
* `WORDPRESS_DB_PASSWORD` → DB password
* `WORDPRESS_DB_NAME` → Database name

Verify:

```bash
docker ps
```

---

## 🔗 Container Linking Explanation

* `--link mysql-container:mysql`
* Allows WordPress container to communicate with MySQL container
* Uses container name as hostname

---

## 🌐 Access WordPress Application

Open browser:

```
http://<EC2-Public-IP>
```

You should see the **WordPress installation page**.

📸 Take screenshots for:

* WordPress Welcome Screen
* Running Containers (`docker ps`)

---

## 🧪 Verification Commands

```bash
docker logs mysql-container
docker logs wordpress-container
```

```bash
docker exec -it mysql-container mysql -u wpuser -p
SHOW DATABASES;
```

---

## 🎯 Key Docker Concepts Used

* Docker Images & Containers
* Environment Variables
* Container Linking
* Port Mapping
* Two-Tier Architecture

---

## 🔮 Enhancements (Next Level)

* Use **Docker Networks** instead of `--link`
* Persist data using **Docker Volumes**
* Convert setup into **Docker Compose**
* Automate deployment using **Ansible**
* Provision infra using **Terraform**

---

## 🧠 Simple Explanation (Interview Ready)

> “This project deploys WordPress and MySQL as separate Docker containers on an EC2 instance, forming a two-tier architecture where WordPress acts as the application layer and MySQL as the database layer.”

---

## 👨‍💻 Author

**Nikhil Misal**
DevOps | Docker | Ansible | Terraform

---

## 📌 Reference Repository

🔗 [https://github.com/nikiimisal/Project-code-3-tier-arc-using_Ansible](https://github.com/nikiimisal/Project-code-3-tier-arc-using_Ansible)
