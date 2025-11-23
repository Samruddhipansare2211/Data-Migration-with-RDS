# 🚀 Database Migration from EC2 Localhost (MySQL/MariaDB) to Amazon RDS 

This guide outlines a detailed, step-by-step method to transfer your MySQL/MariaDB database from an EC2 server (localhost) to an Amazon RDS MySQL deployment.

## 🧰 Prerequisites

- AWS account with EC2 & RDS permissions
- SSH key pair (`.pem` file)
- RDS endpoint, username, password
- EC2 instance access (Linux)
- Basic Shell & MySQL knowledge

## 🧭 Overview

Migration involves:

1. Set up EC2 instance
2. Create database & backup
3. Set up RDS & restore backup

---

# ⚙️ Part 1: Set Up EC2 Instance

## Step 1 — Launch EC2

- AWS Console → EC2 → Launch instance  
- OS: Amazon Linux  
- Enable Auto-assign Public IP  
- Download `.pem` key  

## Step 2 — Connect via SSH

```bash
ssh -i path/to/key.pem ec2-user@<EC2_PUBLIC_IP>
```

## Step 3 — Install MariaDB

```bash
sudo yum update -y
sudo yum install mariadb105-server -y
sudo service mariadb start
sudo systemctl enable mariadb.service
sudo service mariadb status
```

---

# 🗃️ Part 2: Create Database & Backup

## Step 1 — Login to MySQL

```bash
sudo mysql
```

## Step 2 — Set Root Password

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'pass@123';
EXIT;
```

Login again:

```bash
sudo mysql -u root -p
```

## Step 3 — Create DB & Table

```sql
CREATE DATABASE studentdb;
USE studentdb;

CREATE TABLE students (
  roll_no INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100),
  contact VARCHAR(20),
  address VARCHAR(255)
);
```

## Step 4 — Insert Sample Data

```sql
INSERT INTO students (name, contact, address)
VALUES
('Samruddhi', '5689741236', 'Pune'),
('Shravani', '8976541236', 'Otur'),
('Srushti', '8958974536', 'Kothrud'),
('Anushka', '5248974536', 'Mumbai'),
('Anushri', '7895974536', 'Narayangaon');
```

Verify:

```sql
SELECT * FROM students;
EXIT;
```

## Step 5 — Create Backup

```bash
mysqldump -u root -p studentdb > Mydb.sql
```

Check:

```bash
ls
grep -i "create table" Mydb.sql
```

---

# ☁️ Part 3: Setup RDS & Restore

## Step 1 — Create RDS MySQL Instance

- RDS → Create database → Easy Create  
- Engine: MySQL  
- Free tier  
- Set admin password  
- Select EC2 as compute resource  

## Step 2 — Connect EC2 to RDS

```bash
mysql -u admin -p -h <RDS_ENDPOINT>
```

## Step 3 — Create DB in RDS

```sql
CREATE DATABASE studentdb;
EXIT;
```

## Step 4 — Restore Backup

```bash
mysql -u admin -p -h <RDS_ENDPOINT> studentdb < Mydb.sql
```

## Step 5 — Verify Data

```bash
mysql -u admin -p -h <RDS_ENDPOINT> 
USE studentdb;
SHOW TABLES;
SELECT * FROM students;
```

---

# 🔐 Cleanup

- Delete `.pem` and backup from EC2
- Disable public RDS access
- Take RDS snapshot

---

# ✍️ Author

**Samruddhi Pansare**  
Cloud Computing Enthusiast | AWS Learner  
📧 pansaresamruddhi11@gmail.com  
🔗 **LinkedIn:** [Samruddhi Pansare](https://www.linkedin.com/in/samruddhi-pansare-b34371328/)

