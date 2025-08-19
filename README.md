PROJECT-1 : 3-TIRE ARCHITECTURE 

A 3-tier architecture is a structured approach to software 
design that separates an application into three logical tiers: 

1. Presentation Tier (Web Tier) – The user-facing layer 
that handles UI interactions, displays content, and 
forwards requests to the application tier. It typically 
consists of web servers (e.g., EC2 instances in an Auto 
Scaling Group) behind an Application Load Balancer for 
distributing traffic efficiently. 

2. Application Tier (Logic Tier) – The intermediary layer 
that processes user requests, applies business logic, and 
communicates with the database. It consists of 
application servers that execute core functionalities and 
enforce business rules. 

3. Data Tier (Database Tier) – The storage layer 
responsible for data management and retrieval. This 
includes relational database instances like Amazon RDS, 
ensuring secure data persistence with controlled access. 

---

## AWS 3-Tier Project Setup

### Step 1: Create VPC
- Select VPC Only
- Name: `3-tier`
- CIDR: `10.0.0.0/16`
- Enable DNS hostname

### Step 2: Create Subnets
- Attach to VPC
- 2 Public Subnets:
  - Public-1a (10.0.1.0/24) in ap-south-1a
  - Public-1b (10.0.2.0/24) in ap-south-1b
- 4 Private Subnets:
  - Private-1a-1 (10.0.3.0/24) in ap-south-1a
  - Private-1b-1 (10.0.4.0/24) in ap-south-1b
  - Private-1a-2 (10.0.5.0/24) in ap-south-1a
  - Private-1b-2 (10.0.6.0/24) in ap-south-1b

### Step 3: Internet Gateway
- Create IGW (`igw`)
- Attach to VPC

### Step 4: Route Tables
- Edit VPC route table and subnet associations
- Allow internet access

### Step 5: NAT Gateways
- Create 2 NAT gateways (Public-1a, Public-1b)
- Allocate Elastic IPs

### Step 6: EC2 Instances
- Instances: public-1a, public-1b, private-1a-1, private-1b-1
- AMI: Amazon Linux 2023
- Type: t2.micro
- One key pair for all instances
- Security Group: 
  - SSH (22) 0.0.0.0/0
  - HTTP (80) 0.0.0.0/0

### Step 7: Launch Template
- Create template for ASG

### Step 8: Target Groups
- Public Target: (public-1a, public-1b)
- Private Target: (private-1a-1, private-1b-1)

### Step 9: Load Balancers
- Create `public-lb`, `private-lb`
- Attach target groups

### Step 10: Attach Load Balancers
- Edit target groups → Attach to LB

### Step 11: Auto Scaling
- Create ASG
- Attach launch template and LB

### Step 12: RDS Setup
- Create subnet groups (private-1a-2, private-1b-2)
- Engine: MySQL
- Multi-AZ with 2 instances
- Public access: Yes
- Create DB user/password

### Step 13: Connect RDS from EC2 (Private)
```bash
sudo -i
sudo dnf install https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm -y
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
sudo dnf install mysql-community-client -y
mysql -h <rds-endpoint> -u admin -p
```
SQL Commands:
```sql
CREATE DATABASE loki;
USE loki;
CREATE TABLE Users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);
INSERT INTO Users (name, email) VALUES ('Sandy', 'sandy14@gmail.com');
INSERT INTO Users (name, email) VALUES ('Sahil', 'sahil1426@gmail.com');
SELECT * FROM Users;
```

### Step 14: Connect from Web Tier (Public)
- Same MySQL client installation as above
- Run queries to verify access

---

Author: Sandesh Sabale  
Email: sandeshsable014@gmail.com
