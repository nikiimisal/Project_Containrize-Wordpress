# 🐳 WordPress Containerization 

> Two-Tier Architecture (Docker Mini Project)

This document explains how to deploy **WordPress with MySQL using Docker containers** on an **AWS EC2 instance**.
The project is beginner-friendly and designed for **hands-on DevOps practice**.

---

## 📌 Project Objective

* Launch an EC2 instance for a mini project
* Deploy **MySQL (Database Tier)** using Docker
* Deploy **WordPress (Application Tier)** using Docker
* Connect WordPress with MySQL using environment variables
* Understand container-based **Two-Tier Architecture**

<p align="center">
  <img src="https://github.com/nikiimisal/Project_Containrize-Wordpress/blob/main/img/wordpress%20continizer.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>
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

### ⚙️ Step 1: Launch EC2 Instance

* Instance Type: `t2.micro`
* OS: Amazon Linux 2
* Security Group:

  * Allow **SSH (22)**
  * Allow **HTTP (80)**

> 🔹 Best Practice:
> EC2 can be launched using **Terraform / Jenkins**.
> For better understanding, name the instance:
> **`Ansible-Docker-WordPress`**

<p align="center">
  <img src="https://github.com/nikiimisal/Project_Containrize-Wordpress/blob/main/img/Screenshot%202026-01-04%20180857.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>
---

### 🔐 Server Login & Basic Setup

```bash
ssh -i key.pem ec2-user@<public-ip>
```

### Change Hostname (Optional – for clarity)

```bash
sudo hostnamectl set-hostname wordpress-server
exit
```

### Switch to Root User

```bash
sudo -i

```

---

## 🐳 Step 2: Install & Verify Docker

if you wan see installaction process [click here](https://github.com/nikiimisal/Docker_info-and-setup)

```bash
yum update -y
yum install docker -y
systemctl start docker
systemctl enable docker
```

Verify:

```bash
docker --version
docker ps
docker ps -a
```

(Old nginx containers may appear in exited state — this does not affect the project.)

---

## 🗄 Step 3: MySQL Container Setup (Database Tier)

### Step 1️: Pull MySQL Image from Docker Hub


```bash
docker pull mysql:5.7   # Optional
```

### Step 2️: Run MySQL Container

```bash
docker run -d --name mydb -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=wordpressdb mysql
```


### Environment Variables Explanation

* `MYSQL_ROOT_PASSWORD` → Root password
* `MYSQL_DATABASE` → WordPress database
* `MYSQL_USER` → WordPress DB user
* `MYSQL_PASSWORD` → WordPress DB password

To view environment variables, you can check the official MySQL Docker repository.<br>
👉[click here](https://hub.docker.com/_/mysql)
Verify:

```bash
docker ps
```
<p align="center">
  <img src="https://github.com/nikiimisal/Project_Containrize-Wordpress/blob/main/img/Screenshot%202026-01-04%20173335.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>
---

## 🔐 Step 4: Validate MySQL Database

```bash
docker exec -it mydb /bin/bash
```

```bash
mysql -u root -p
```

```sql
show databases;
use wordpressdb;
show tables;
```

At this stage, the database exists but tables are **empty** (WordPress not connected yet).


<p align="center">
  <img src="https://github.com/nikiimisal/Project_Containrize-Wordpress/blob/main/img/Screenshot%202026-01-04%20174626.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>

---



## 🌐 Step 5: WordPress Container Setup (Application Tier)

### Step i: Pull WordPress Image

```bash
docker pull wordpress:latest   # Optional
```
or

### Step ii: Run WordPress Container (Linked with MySQL)

```bash
 docker run -d -p 80:80 --name wordpressapp -e WORDPRESS_DB_HOST=mydb -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=root -e WORDPRESS_DB_NAME=wordpressdb --link mydb:mysql wordpress
```

### WordPress Environment Variables

* `WORDPRESS_DB_HOST` → MySQL container name
* `WORDPRESS_DB_USER` → DB username
* `WORDPRESS_DB_PASSWORD` → DB password
* `WORDPRESS_DB_NAME` → Database name

  To view environment variables, you can check the official Wordpress Docker repository.<br>
👉[click here](https://hub.docker.com/_/wordpress)

Verify:

```bash
docker ps
```
<p align="center">
  <img src="https://github.com/nikiimisal/Project_Containrize-Wordpress/blob/main/img/Screenshot%202026-01-04%20164943.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>

---

## 🔗 Container Linking Explanation

* `--link mysql-container:mysql`
* Allows WordPress container to communicate with MySQL container
* Uses container name as hostname

---

## 🌍 Step 6: Access WordPress Application

Open browser:

```
http://<EC2-Public-IP>
```

You should see the **WordPress installation page**.
| **Wordpress-Consol**    | ****          | ****          |
|--------------------------------|------------------------------------|------------------------------------|
| ![VS](https://github.com/nikiimisal/Project_Containrize-Wordpress/blob/main/img/Screenshot%202026-01-04%20165151.png?raw=true) | ![AWS](https://github.com/nikiimisal/Project_Containrize-Wordpress/blob/main/img/Screenshot%202026-01-04%20165338.png?raw=true) | ![AWS](https://github.com/nikiimisal/Project_Containrize-Wordpress/blob/main/img/Screenshot%202026-01-04%20165338.png?raw=true) |

---

## ✅ Step 7: Verify DB–WordPress Connectivity

```bash
docker exec -it mydb /bin/bash
mysql -u root -p
```

```sql
use wordpressdb;
show tables;
```

✅ WordPress tables are now created automatically:

* wp_posts
* wp_users
* wp_options
* wp_comments
* etc.

This confirms **successful application–database integration**.

<p align="center">
  <img src="https://github.com/nikiimisal/Project_Containrize-Wordpress/blob/main/img/Screenshot%202026-01-04%20174626.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>

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
