# AWS Cloud Practitioner Notes

Welcome to my learning repository for the AWS Cloud Practitioner certification! This repository serves as a compilation of notes, key concepts, and important information I've gathered while preparing for the exam. The goal is to create a comprehensive and easy-to-reference resource for anyone studying for the AWS Cloud Practitioner certification.

## Table of Contents

* [Introduction](#introduction)
* [Identity and Access Management (IAM)](#identity-and-access-management-iam)
    * [IAM Policy Structure](#iam-policy-structure)
    * [Shared Responsibility Model for IAM](#shared-responsibility-model-for-iam)
* [Elastic Compute Cloud (EC2)](#elastic-compute-cloud-ec2)
    * [Introduction to EC2](#introduction-to-ec2)
    * [EC2 Sizing & Configuration Options](#ec2-sizing--configuration-options)
    * [Introduction to Security Groups](#introduction-to-security-groups)
    * [Security Groups - Good to Know](#security-groups---good-to-know)
    * [Classic Ports to Know](#classic-ports-to-know)
    * [Connecting to EC2 Instances](#connecting-to-ec2-instances)
    * [EC2 Section - Summary](#ec2-section---summary)
* [Elastic Block Store (EBS)](#elastic-block-store-ebs)
    * [What's an EBS Volume?](#whats-an-ebs-volume)
    * [EBS - Delete on Termination Attribute](#ebs---delete-on-termination-attribute)
    * [EBS Snapshots](#ebs-snapshots)
    * [EBS Snapshots Features](#ebs-snapshots-features)
* [Amazon Machine Image (AMI)](#amazon-machine-image-ami)
* [EC2 Instance Store](#ec2-instance-store)
* [Elastic File System (EFS)](#elastic-file-system-efs)
* [Amazon FSx](#amazon-fsx)
* [Storage and Image Services - Summary](#storage-and-image-services---summary)
* [Scalability & High Availability](#scalability--high-availability)
    * [Scalability](#scalability)
    * [High Availability](#high-availability)
* [Load Balancing](#load-balancing)
    * [Why Use an Elastic Load Balancer (ELB)?](#why-use-an-elastic-load-balancer-elb)
    * [Types of Load Balancers Offered by AWS](#types-of-load-balancers-offered-by-aws)
    * [Example EC2 User Data for Web Server (for Load Balancing Practice)](#example-ec2-user-data-for-web-server-for-load-balancing-practice)
* [Auto Scaling Group (ASG)](#auto-scaling-group-asg)
    * [Auto Scaling Groups - Scaling Strategies](#auto-scaling-groups---scaling-strategies)

---

## Introduction

This section provides an overview of the AWS Cloud Practitioner certification and what you can expect to learn. It covers the fundamentals of the AWS Cloud, including its global infrastructure, core services, security, pricing, and support models. Understanding these foundational concepts is crucial for anyone looking to build a career in the cloud.

---

## Identity and Access Management (IAM)

AWS Identity and Access Management (IAM) is a web service that helps you securely control access to AWS resources. With IAM, you can manage who is authenticated (signed in) and authorized (has permissions) to use resources.

### IAM Policy Structure

IAM policies are JSON documents that define permissions. They specify what actions are allowed or denied on which resources, and under what conditions.

A typical IAM policy consists of the following key elements:

* **Version**:
    * Specifies the version of the policy language.
    * Always include `"2012-10-17"`.
* **Id (optional)**:
    * An identifier for the policy.
* **Statement (required)**:
    * One or more individual statements, each defining a set of permissions.
    * Each statement itself consists of:
        * **Sid (optional)**: An identifier for the statement.
        * **Effect**:
            * Determines whether the statement `Allow`s or `Deny`s access.
            * `Deny` statements override `Allow` statements.
        * **Principal**:
            * The account/user/role to which this policy applies.
            * Example: `"AWS": ["arn:aws:iam::123456789012:root"]` (for a root account).
        * **Action**:
            * A list of specific API actions that this policy `Allow`s or `Deny`s.
            * Example: `"s3:GetObject"`, `"s3:PutObject"`.
        * **Resource**:
            * A list of AWS resources to which the actions apply.
            * Example: `"arn:aws:s3:::mybucket/*"` (for all objects in `mybucket`).
        * **Condition (optional)**:
            * Specifies conditions for when this policy is in effect. This allows for fine-grained control based on factors like time of day, source IP address, or whether MFA is used.

**Example Policy Structure (as seen in the image):**

```json
{
  "Version": "2012-10-17",
  "Id": "S3-Account-Permissions",
  "Statement": [
    {
      "Sid": "1",
      "Effect": "Allow",
      "Principal": {
        "AWS": ["arn:aws:iam::123456789012:root"]
      },
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": ["arn:aws:s3:::mybucket/*"]
    }
  ]
}
```

### Shared Responsibility Model for IAM

The AWS Shared Responsibility Model defines what security responsibilities AWS handles and what responsibilities the customer (You) handles. For IAM, this model is crucial:

**AWS's Responsibilities (Security of the Cloud):**

* Infrastructure (global network security): AWS is responsible for protecting the infrastructure that runs all of the services offered in the AWS Cloud. This infrastructure includes the hardware, software, networking, and facilities that run AWS services. 
* Configuration and vulnerability analysis: AWS ensures the underlying infrastructure is secure and free from vulnerabilities. 
* Compliance validation: AWS maintains compliance with various global security standards.

**Your Responsibilities (Security in the Cloud):**

* Users, Groups, Roles, Policies management and monitoring: You are responsible for configuring and managing your IAM entities and the permissions associated with them. 
* Enable MFA on all accounts: It's your responsibility to enable Multi-Factor Authentication for enhanced security on all your AWS accounts and IAM users. 
* Rotate all your keys often: Regularly rotating access keys and other credentials is a key security best practice. 
* Use IAM tools to apply appropriate permissions: You must correctly configure IAM policies to grant only the necessary permissions (least privilege). 
* Analyze access patterns & review permissions: Continuously monitor and review who has access to what, and analyze access patterns to identify and rectify any overly permissive policies.

---

## Elastic Compute Cloud (EC2)

### Introduction to EC2

EC2, or Elastic Compute Cloud, is one of AWS's most popular offerings and represents an **Infrastructure as a Service (IaaS)** model. It fundamentally provides the capability to:

* **Rent virtual machines (EC2 Instances)**: On-demand, scalable compute capacity.
* **Store data on virtual drives (EBS)**: Persistent block storage for EC2 instances.
* **Distribute load across machines (ELB)**: Elastic Load Balancing to distribute incoming application traffic across multiple EC2 instances.
* **Scale services using an auto-scaling group (ASG)**: Automatically adjust the number of EC2 instances in response to changing demand.

Understanding EC2 is fundamental to comprehending how the Cloud operates and how applications can be hosted and scaled on AWS.

### EC2 Sizing & Configuration Options

When launching an EC2 instance, you have several configuration options:

* **Operating System (OS)**: Choose from various operating systems like Linux (including AWS Linux/Amazon Machine Image - AMI), Windows, or macOS.
* **Compute Power & Cores (CPU)**: Select the desired processing power.
* **Random-Access Memory (RAM)**: Define the amount of memory available to the instance.
* **Storage Space**:
    * **Network-attached storage**: Elastic Block Store (EBS) volumes or Elastic File System (EFS).
    * **Hardware (EC2 Instance Store)**: Ephemeral storage directly attached to the host computer (data lost on termination).
* **Network Card**: Configure aspects like network speed and assign a Public IP address.
* **Firewall Rules**: Defined by **Security Groups**.
* **Bootstrap Script (EC2 User Data)**: A script that runs only at the first launch of an instance to perform initial configurations (e.g., installing software, configuring services).

**Key Notes:**
* The `t2.micro` instance type is often available under the AWS Free Tier.
* It's common to use AWS-provided AMIs, such as AWS Linux, for ease of use and optimization.
* The **User Data** field allows you to pass scripts for advanced configurations during the first instantiation.
* You can explore various instance types and their specifications here: [https://aws.amazon.com/ec2/instance-types/](https://aws.amazon.com/ec2/instance-types/)

### Introduction to Security Groups

Security Groups are a fundamental component of network security in AWS, acting as virtual firewalls that control inbound and outbound traffic to and from your EC2 instances.

* They **only contain allow rules**; you cannot create "deny" rules within a security group.
* Security Group rules can reference traffic by **IP address range (CIDR block)** or by **other Security Groups**.

### Security Groups - Good to Know

* **Can be attached to multiple instances**: A single security group can protect multiple EC2 instances.
* **Locked down to a region/VPC combination**: Security groups are specific to the Virtual Private Cloud (VPC) they are created in and are regional resources.
* **Lives "outside" the EC2 instance**: If traffic is blocked by a security group, the EC2 instance itself won't even "see" that traffic attempt. This means the security group filters traffic *before* it reaches the instance.
* **It's good practice to maintain one separate security group for SSH access**.
* **Troubleshooting Tip 1**: If your application is not accessible and you experience a **timeout**, it's most likely a **security group issue** (inbound rule blocking traffic).
* **Troubleshooting Tip 2**: If your application gives a **"connection refused"** error, then it's typically an **application error** or the application service is not launched/listening on the expected port on the EC2 instance.
* **Default Behavior**:
    * **All inbound traffic is blocked by default**.
    * **All outbound traffic is authorized by default**.

### Classic Ports to Know

Understanding common network ports is crucial for configuring security groups:

* **22 = SSH (Secure Shell)**: Used to log into Linux instances, execute commands, and transfer files securely.
* **21 = FTP (File Transfer Protocol)**: Used to upload/download files to/from a file share. (Note: FTP is often considered less secure than SFTP/SCP).
* **22 = SFTP (Secure File Transfer Protocol)**: Used to upload files securely, leveraging SSH.
* **80 = HTTP**: Used to access unsecured websites.
* **443 = HTTPS**: Used to access secured websites (encrypted communication).
* **3389 = RDP (Remote Desktop Protocol)**: Used to log into Windows instances with a graphical interface.

### Connecting to EC2 Instances

To connect to your EC2 instances:

1.  **Elastic IP (EIP)**: If you need a persistent public IP address that doesn't change when your instance stops/starts, allocate an Elastic IP and associate it with your EC2 instance. Navigate to `Network & Security -> Elastic IPs -> Allocate Elastic IP -> Link to your instance`.
2.  **SSH (for Linux Instances)**:
    * Download the private key (`.pem`) file for your instance when you launch it.
    * Set appropriate permissions for the key file: `chmod 400 file.pem`
    * Connect using the command: `ssh -i “file.pem” ec2-user@<PUBLIC_IP>` (replace `ec2-user` with the appropriate username for your AMI, e.g., `ubuntu`, `centos`, `admin`).
3.  **EC2 Instance Connect**:
    * Provides a simple and secure way to connect to your EC2 instances directly from the AWS Management Console or AWS CLI.
    * **Important Note**: Be careful not to configure sensitive values (like AWS credentials) directly via `aws configure` if you're using instance connect without proper IAM roles. **ALWAYS manage sensitive configurations and permissions via IAM Roles attached to the EC2 instance** to follow the principle of least privilege and enhance security.

### EC2 Section - Summary

To summarize the key components of EC2:

* **EC2 Instance**: The virtual server, defined by its **AMI** (Operating System), **Instance Size** (CPU + RAM), **Storage** (EBS/Instance Store), associated **Security Groups**, and optional **EC2 User Data** script.
* **Security Groups**: The virtual firewall attached to the EC2 instance, controlling inbound and outbound traffic.
* **EC2 User Data**: A script executed only once, at the first launch of an instance, for initial configuration.
* **SSH**: The protocol (Port 22) used to securely access and manage Linux EC2 instances via a terminal.
* **EC2 Instance Role**: A mechanism to securely link EC2 instances to **IAM Roles**, allowing the instance to perform actions on other AWS services without embedding credentials.
* **Purchasing Options**: AWS offers various ways to purchase EC2 capacity to optimize costs:
    * **On-Demand**: Pay for compute capacity by the hour or second, with no long-term commitment.
    * **Spot Instances**: Bid for unused EC2 capacity, offering significant cost savings for fault-tolerant workloads.
    * **Reserved Instances (Standard + Convertible)**: Commit to a specific instance configuration for 1 or 3 years for substantial discounts.
    * **Dedicated Host**: A physical server dedicated for your use, providing visibility into the underlying sockets/cores. Useful for licensing requirements.
    * **Dedicated Instance**: EC2 instances that run on hardware dedicated to a single customer, but may share hardware with other instances from that same customer.

---

## Elastic Block Store (EBS)

### What's an EBS Volume?

An EBS (Elastic Block Store) Volume is a highly available, reliable, and scalable block storage service designed for use with Amazon EC2 instances. Think of it as a "network USB stick" for your virtual machines.

Key characteristics of EBS volumes:

* **Network Drive**: EBS volumes are network-attached drives, meaning they communicate with your EC2 instance over the network. This might introduce a small amount of latency compared to physical drives.
* **Data Persistence**: They allow your instances to persist data independently of the instance's lifecycle. Data remains even after the EC2 instance is terminated.
* **Single Instance Attachment (CCP Level)**: At the AWS Cloud Practitioner level, it's important to know that an EBS volume can generally be mounted to **only one EC2 instance at a time**.
    * *Note*: AWS does offer EBS Multi-Attach for specific volume types (io1/io2) which allows attaching a single volume to multiple instances in the same Availability Zone. You can learn more here: [https://docs.aws.amazon.com/ebs/latest/userguide/ebs-volumes-multi.html](https://docs.aws.com/ebs/latest/userguide/ebs-volumes-multi.html)
* **Availability Zone (AZ) Bound**: EBS volumes are tied to a specific Availability Zone. An EBS volume created in `us-east-1a` cannot be directly attached to an EC2 instance in `us-east-1b`. To "move" a volume across AZs, you first need to take a snapshot of it.
* **Detachable and Re-attachable**: An EBS volume can be quickly detached from one EC2 instance and attached to another within the same Availability Zone.

### EBS - Delete on Termination Attribute

This attribute controls the behavior of an EBS volume when the EC2 instance it's attached to is terminated. It can be changed in the advanced settings for EBS when launching an instance or modifying an existing volume.

* **By default, the root EBS volume** (the one containing the operating system) is **deleted** when the EC2 instance terminates (attribute is enabled).
* **By default, any other attached EBS volume** (non-root volumes) is **NOT deleted** when the EC2 instance terminates (attribute is disabled). This prevents accidental data loss.

### EBS Snapshots

EBS Snapshots are incremental backups of your EBS volumes at a specific point in time.

* **Backup Feature**: They allow you to make a backup (snapshot) of your EBS volume.
* **Online Operation**: It is **not strictly necessary to detach a volume to take a snapshot**, but it is generally **recommended** to temporarily stop I/O to the volume for application-consistent snapshots, especially for active databases.
* **Cross AZ/Region Copy**: You can copy snapshots across different Availability Zones within the same region or even across different AWS Regions. This is how you "move" EBS data across AZs or regions.

### EBS Snapshots Features

AWS provides advanced features for managing EBS Snapshots:

* **EBS Snapshot Archive**:
    * Allows you to move an EBS Snapshot to an "archive tier" for long-term retention.
    * This tier is significantly cheaper (up to 75% less) than standard snapshots.
    * Restoring a snapshot from the archive tier can take between 24 to 72 hours.
* **Recycle Bin for EBS Snapshots**:
    * Enables you to set up rules to retain deleted snapshots for a specified period.
    * This provides a safety net, allowing you to recover accidentally deleted snapshots.
    * You can specify the retention period, ranging from 1 day to 1 year.

---
## Amazon Machine Image (AMI)

An **Amazon Machine Image (AMI)** serves as a template for launching EC2 instances. It's essentially a pre-configured package containing the operating system, applications, and settings required to quickly and consistently launch an instance.

* **Customization of EC2 Instances**: AMIs are a customization of an EC2 instance. You can add your own software, configuration files, operating system settings, monitoring tools, and more.
* **Faster Boot/Configuration Time**: Using an AMI significantly reduces the time it takes to boot and configure a new instance because all your necessary software and configurations are pre-packaged.
* **Region-Specific (but Copyable)**: AMIs are built for a specific AWS region. However, you can copy an AMI across different regions to launch instances globally.
* **Sources for AMIs**: You can launch EC2 instances from:
    * **Public AMIs**: Provided and maintained by AWS (e.g., Amazon Linux AMI, Ubuntu Server AMI).
    * **Your Own AMIs**: You can create and maintain custom AMIs from your existing EC2 instances after configuring them to your needs.
    * **AWS Marketplace AMIs**: AMIs created and potentially sold by other vendors, often including specialized software or configurations.

---
## EC2 Instance Store

While EBS volumes are network drives, **EC2 Instance Store** provides temporary block-level storage for your EC2 instances. It's physically attached to the host computer where your instance is running.

* **High Performance Hardware Disk**: Designed for high I/O performance, offering better throughput and lower latency than EBS for specific use cases.
* **Ephemeral Storage**: The data on an EC2 Instance Store is **ephemeral**, meaning it **loses its storage if the instance is stopped, terminated, or if the underlying host fails**.
* **Use Cases**: Ideal for temporary data that can be re-generated, such as:
    * Buffers and caches
    * Scratch data
    * Temporary content
* **Responsibility for Data**: Due to its ephemeral nature, you are responsible for any backups and replication of data stored on instance stores if it's critical. There's a risk of data loss if the underlying hardware fails.

---
## Elastic File System (EFS)

**Amazon Elastic File System (EFS)** provides a simple, scalable, elastic, and highly available file storage service that can be accessed by multiple EC2 instances simultaneously.

* **Network File System**: EFS is a network file system (NFSv4 protocol) that can be mounted by hundreds or thousands of EC2 instances across multiple Availability Zones within a region.
* **Shared Access**: Multiple EC2 instances can access the same EFS file system at the same time, making it suitable for shared workloads, content management, web serving, and big data analytics.
* **Elastic Scaling**: EFS automatically grows and shrinks as you add or remove files, so you only pay for the storage you use.
* **EFS Infrequent Access (EFS-IA)**: A cost-optimized storage class for files that are accessed less frequently. It offers significant cost savings compared to standard EFS for data with infrequent access patterns. Data can transition automatically to EFS-IA based on lifecycle policies.

---
## Amazon FSx

**Amazon FSx** is a family of fully managed file storage services for popular third-party file systems. It aims to provide the full feature set and performance of these file systems without the operational overhead of managing them yourself.

* **Managed Third-Party File Systems**: Launches and manages high-performance file systems like:
    * **FSx for Windows File Server**: Provides fully managed, highly reliable, and scalable file storage that's accessible via the Server Message Block (SMB) protocol. It's built on Windows Server and integrates with existing Windows environments.
    * **FSx for Lustre**: A high-performance file system optimized for fast processing of workloads, such as machine learning, high performance computing (HPC), video processing, and financial modeling. It's a Linux file system.

## Storage and Image Services - Summary

This section covered various storage and instance image options in AWS:

* **EBS Volumes**: Network drives that can be attached to one EC2 instance at a time (at the CCP level). They are mapped to an Availability Zone and support EBS Snapshots for backups and cross-AZ/region transfers.
* **AMI (Amazon Machine Image)**: Custom images used to create ready-to-use EC2 instances with pre-configured software, OS, and settings. They improve boot and configuration times.
    * **EC2 Image Builder**: A service that automates the creation, testing, and distribution of secure and compliant AMIs.
* **EC2 Instance Store**: High-performance hardware disks directly attached to the EC2 instance. Data is lost if the instance is stopped or terminated (ephemeral). Best for temporary data like caches.
* **EFS (Elastic File System)**: A network file system that allows hundreds of instances to access shared storage concurrently across multiple Availability Zones in a region.
    * **EFS-IA (Infrequent Access)**: A cost-optimized storage class within EFS for less frequently accessed files.
* **FSx**: Fully managed third-party file systems:
    * **FSx for Windows File Server**: For Windows-based applications and workloads requiring SMB access.
    * **FSx for Lustre**: High-performance file system for Linux-based compute workloads like HPC.

---
## Scalability & High Availability

Understanding scalability and high availability is crucial for designing robust and performant applications in the cloud.

### Scalability

**Scalability** means that an application or system can handle greater loads by adapting its capacity.

There are two primary kinds of scalability:

* **Vertical Scalability (Scale Up)**:
    * Involves increasing the resources or size of a single instance (e.g., switching from a `t2.micro` to a `t2.large` EC2 instance).
    * Mostly used in non-distributed systems, such as traditional databases.
    * There's usually a hardware limit to how much one can vertically scale a single resource.
* **Horizontal Scalability (Scale Out)**:
    * Involves increasing the number of instances or systems running the application (e.g., adding more EC2 instances).
    * This approach is characteristic of distributed systems.
    * **Elasticity** is closely linked to horizontal scalability. Once a system is horizontally scalable, elasticity implies that there will be some "auto-scaling" mechanism. This allows the system to automatically scale based on the load, making it "cloud-friendly" by enabling pay-per-use, matching demand, and optimizing costs.

**Note**: Scalability is linked to, but distinct from, High Availability.

### High Availability

**High Availability** (HA) typically goes hand-in-hand with horizontal scaling.

* **Definition**: High availability means designing and running your application or system in a way that it can survive failures, often by distributing it across at least two **Availability Zones (AZs)**.
* **Goal**: The primary goal of high availability is to ensure application uptime and survive potential data center (Availability Zone) outages or disasters.

**Key Cloud Concepts Related to Scalability & HA:**

* **Scalability**: The ability to accommodate a larger load by making the hardware stronger (scale up), or by adding more computing nodes (scale out).
* **Elasticity**: The ability of a system to automatically scale in or out based on demand, ensuring optimal performance and cost efficiency. This is a core cloud benefit.
* **Agility**: (A distinct cloud benefit, not directly related to scalability) Refers to the speed and ease with which new IT resources can be provisioned (e.g., resources are only a click away, reducing provisioning time from weeks to minutes).

---
## Load Balancing

**Load balancers** are critical components that distribute incoming internet traffic across multiple backend servers (e.g., EC2 Instances), enhancing the availability and fault tolerance of your applications.

They provide several key benefits:

* **Distribute Load**: Spreads incoming requests evenly across multiple downstream instances.
* **Single Point of Access**: Exposes a single DNS name as an entry point to your application, simplifying access for users.
* **Seamless Failure Handling**: Automatically routes traffic away from unhealthy instances to healthy ones, providing seamless handling of instance failures.
* **Health Checks**: Performs regular health checks on downstream instances to ensure they are responsive and capable of serving requests.
* **SSL Termination**: Can handle SSL/TLS encryption and decryption (HTTPS), offloading this compute-intensive task from your backend instances.
* **High Availability Across Zones**: Can distribute traffic across instances in multiple Availability Zones, increasing application resilience.

### Why Use an Elastic Load Balancer (ELB)?

An **Elastic Load Balancer (ELB)** is a **managed load balancing service** provided by AWS.

* **AWS Managed Service**: AWS fully manages the ELB service, guaranteeing its operation and availability.
* **Reduced Operational Overhead**: AWS handles upgrades, maintenance, and ensures the high availability of the load balancer itself.
* **Simplified Configuration**: AWS provides a streamlined set of configuration options, making it easier to set up.
* **Cost vs. Effort**: While it might seem cheaper to set up your own load balancer on an EC2 instance, the operational effort for maintenance, high availability, and integrations would be significantly higher.

### Types of Load Balancers Offered by AWS

AWS offers different types of Elastic Load Balancers, each optimized for specific use cases and network layers (OSI model):

* **Application Load Balancer (ALB)**:
    * Operates at **Layer 7 (HTTP/HTTPS)**.
    * Ideal for HTTP and HTTPS traffic.
    * Offers advanced request routing based on URL path, host header, query string parameters, etc.
* **Network Load Balancer (NLB)**:
    * Operates at **Layer 4 (TCP/UDP)**.
    * Offers ultra-high performance and low latency.
    * Capable of handling millions of requests per second.
    * Suitable for extreme performance and static IP address needs.
* **Gateway Load Balancer (GWLB)**:
    * Operates at **Layer 3 (IP layer)**.
    * Used to deploy, manage, and scale third-party virtual appliances (e.g., firewalls, intrusion detection systems).
* **Classic Load Balancer (CLB)**:
    * Operates at both **Layer 4 and Layer 7**.
    * This is the legacy load balancer type and was **retired in 2023**. New applications should use ALB or NLB.

**Practical Tip**: When practicing with EC2 instances and load balancers, create 2 or more instances, add them to a **Target Group**, and then associate the Target Group with a Load Balancer. When you access the Load Balancer's DNS name, it will automatically distribute requests between your instances.

### Example EC2 User Data for Web Server (for Load Balancing Practice)

This script can be used as **User Data** when launching your EC2 instances. It will install Apache (httpd) and set up a basic "Hello World" page, allowing you to see which instance serves the request.

```bash
#!/bin/bash
# Use this for your user data (script from top to bottom)
# install httpd (Amazon Linux 2 version)
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html
```
---

## Auto Scaling Group (ASG)

In real-world applications, the load on your websites and applications can fluctuate significantly throughout the day, week, or even seasonally. The cloud's ability to provision and de-provision servers rapidly makes it ideal for handling these dynamic loads.

The primary goals of an **Auto Scaling Group (ASG)** are to:

* **Scale Out**: Automatically add EC2 instances to match an increased load or demand.
* **Scale In**: Automatically remove EC2 instances to match a decreased load, optimizing costs.
* **Maintain Instance Count**: Ensure that you always have a minimum number of machines running for availability and a maximum number to control costs and prevent over-provisioning.
* **Automatic Registration**: Seamlessly register new instances launched by the ASG to an associated Load Balancer.
* **Replace Unhealthy Instances**: Automatically detect and replace unhealthy or impaired instances, enhancing application fault tolerance.
* **Cost Savings**: By dynamically adjusting capacity to match demand, ASGs help ensure you only run at an optimal capacity, aligning with the "pay-per-use" principle of the cloud.

An ASG's definition involves three key capacity settings (minimum, desired, and maximum) and typically works in conjunction with a Load Balancer to distribute traffic across its instances.

### Auto Scaling Groups - Scaling Strategies

ASGs offer various strategies to determine when and how to scale your instances:

* **Manual Scaling**:
    * You manually update the size (number of instances) of the ASG.
    * This is the simplest method but lacks automation.

* **Dynamic Scaling**: Responds to changing demand by automatically adjusting the ASG size.

    * **Simple / Step Scaling**:
        * Scales based on a CloudWatch alarm threshold.
        * **Example**: "When CPU utilization is greater than 70%, add 2 instances."
        * **Example**: "When CPU utilization is less than 30%, remove 1 instance."
        * Can be less precise as it acts in steps and has cooldown periods.

    * **Target Tracking Scaling**:
        * Aims to keep a specific metric at a user-defined target value. AWS handles the scaling adjustments.
        * **Example**: "I want the average ASG CPU utilization to stay at around 40%." The ASG will automatically add or remove instances to try and maintain this target.

* **Scheduled Scaling**:
    * Anticipates scaling needs based on known usage patterns (e.g., daily peaks, weekly promotions).
    * **Example**: "Increase the minimum capacity to 10 instances every Friday at 5 PM."

* **Predictive Scaling**:
    * Uses Machine Learning (ML) to predict future traffic patterns based on historical data.
    * Automatically provisions the right number of instances in advance of predicted demand.
    * Especially useful when the load has predictable time-based patterns but might be complex for rule-based systems.
