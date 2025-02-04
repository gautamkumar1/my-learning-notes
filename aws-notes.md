### Introduction to Cloud
- Suppose you make a nodejs app which listens on Port 8080. Now you want the world to access your app. For this, you have to do the following steps:

- You have to buy a public static IP address. So, that another person can visit “http://<your_IP>:8080” to access your site.
Ensure that your laptop is ON for 24 hrs all day.
- Your laptop has limited specs (RAM, CPU, etc); if more users come to your laptop, you may have to increase its size to handle the users, and it will be a burden.
You will have to protect it from cyber-attacks and also physical attacks (stealing or getting damaged due to flood, etc).

## Regions and Availability Zones

## Regions

- AWS Region → A geographical area with multiple data centers.

- Regions are independent → Resources in one region are not visible in another.

- Choosing a Region:
    Latency → Closer regions = Faster performance.
    Compliance → Follows local laws.
    Disaster Recovery → Redundant backups.

- Example: If your business is in India, use Mumbai (ap-south-1) for low latency.

- Latency → Time taken for data to travel (lower latency = faster response). 🚀

# Availability Zones

## What are Availability Zones (AZs)?
- **Isolated locations** within an AWS Region.
- Each AWS Region has **multiple AZs** (typically **three or more**).

## Example:
- In **Mumbai (`ap-south-1`)**, AWS has physical data centers **spread across different locations within a 100 km radius**.

## Why Use Multiple Availability Zones?
1. **Redundancy & High Availability** → Protects applications from failure in a single data center.
2. **Better Disaster Recovery** → Ensures minimal downtime by distributing resources across multiple AZs.

Deploying across multiple AZs **enhances reliability and fault tolerance** of cloud applications. 

# AWS EC2 

## What is AWS EC2?
- **EC2 (Elastic Compute Cloud)** is a virtual machine in the cloud that provides computing resources.
- It has a **public IP address**, allowing you to run applications accessible via the internet.
- Essentially, it's like renting a **cloud laptop** to deploy and host your applications.

## Why Use EC2?
- Your local laptop **does not** have a public IP, so apps running locally **can't be accessed** by others.
- EC2 provides a **publicly accessible virtual machine**, allowing users to visit `http://<EC2_IP>:8080` from anywhere.

## Use Cases
- Deploy applications like **Node.js, FastAPI, Go, Spring Boot**, etc.
- Store and process data.
- Host websites and services.

EC2 enables seamless cloud deployment and internet accessibility for your applications. 

# AWS Instance

## 1. What is an Instance?
- A **virtual server** in AWS.
- Runs an **operating system** (Linux, Windows, etc.).
- Can be configured with **different CPU, memory, and storage**.
- We will use **Linux Ubuntu** for our learning.

## 2. What is an Instance Type?
- Defines the **hardware specifications** of an instance.
- Includes **CPU, RAM, and performance levels**.
- Examples:
  - **t2.micro** → Small, general-purpose instance.
  - **m5.large** → More powerful instance.

## 3. What is an Elastic IP Address?
- A **static public IP** assigned to an instance.
- Makes the instance **accessible from the internet**.

AWS instances provide flexible cloud computing with customizable configurations. 

# Security Group

## What is a Security Group?
A **Security Group** is a set of rules that control traffic to and from your EC2 instances. It acts as a virtual firewall that manages traffic based on IP address, port, and protocol.

## Key Concepts:
1. **Inbound Rules**:
   - Define which traffic is allowed to enter your EC2 instance.
   - Example: Allow SSH traffic (Port 22) only from your specific IP address or expose Port 8080 for everyone to access your app.

2. **Outbound Rules**:
   - Define which traffic is allowed to leave your EC2 instance.
   - Example: Restrict outbound traffic to allow only your EC2 instance to connect to your database, preventing other services from connecting.

## How to Use:
- You can **name** the Security Group and attach it to one or more EC2 instances.
- A **single EC2 instance** can have multiple security groups attached, and one security group can be attached to multiple EC2 instances.

## Example Use Cases:
- Secure SSH access by limiting it to your own IP.
- Expose app access globally by opening Port 8080.
- Secure database connections by limiting outgoing traffic from EC2 to your DB instance.

# Deploy Node.js Application on EC2

## Step-by-Step Guide to Deploy Node.js App

### 1. Launch EC2 Instance:
   - Create an EC2 instance (Amazon Linux 2/Ubuntu).
   - Set up SSH keys and security groups to allow SSH access and open port 80/3000.

### 2. Connect to EC2 Instance:
   - Use SSH to connect to the EC2 instance.
   ```bash
   ssh -i your-key.pem ec2-user@your-ec2-public-ip
   ```





