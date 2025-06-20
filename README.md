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
* [Shared Responsibility Model for Amazon S3](#shared-responsibility-model-for-amazon-s3)
    * [AWS Responsibilities (Security *of* the Cloud)](#aws-responsibilities-security-of-the-cloud)
    * [Your Responsibilities (Security *in* the Cloud)](#your-responsibilities-security-in-the-cloud)
* [Amazon Simple Storage Service (S3)](#amazon-simple-storage-service-s3)
    * [Amazon S3 - Use Cases](#amazon-s3---use-cases)
    * [Amazon S3 - Buckets](#amazon-s3---buckets)
    * [Amazon S3 - Objects](#amazon-s3---objects)
    * [S3 Security](#s3-security)
    * [S3 Bucket Policies](#s3-bucket-policies)
    * [S3 Versioning](#s3-versioning)
    * [S3 Replication](#s3-replication)
    * [S3 Storage Classes](#s3-storage-classes)
    * [S3 Durability and Availability](#s3-durability-and-availability)
    * [S3 Encryption](#s3-encryption)
    * [IAM Access Analyzer for S3](#iam-access-analyzer-for-s3)
* [Data Migration & Hybrid Cloud Services](#data-migration--hybrid-cloud-services)
    * [AWS Snow Family (AWS Snowball)](#aws-snow-family-aws-snowball)
    * [AWS Storage Gateway](#aws-storage-gateway)

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

---
## Amazon Simple Storage Service (S3)

Amazon S3 is one of the foundational building blocks of AWS, famously advertised as "infinitely scaling" storage. It's a highly durable, available, and scalable object storage service.

### Amazon S3 - Use Cases

S3 is incredibly versatile and used for a wide range of purposes:

* **Backup and Storage**: A primary use case for reliable data backup.
* **Disaster Recovery**: Storing recovery points for quick restoration in case of an outage.
* **Archive**: Long-term, cost-effective storage for data that is rarely accessed.
* **Hybrid Cloud Storage**: Extending on-premises storage to the cloud.
* **Application Hosting**: Storing application assets, user-generated content.
* **Media Hosting**: Storing videos, images, and other media files for websites and applications.
* **Data Lakes & Big Data Analytics**: A common landing zone for large datasets used in analytics.
* **Software Delivery**: Distributing software updates and patches.
* **Static Websites**: Hosting static HTML, CSS, JavaScript, and image files for websites.

### Amazon S3 - Buckets

Amazon S3 allows you to store objects (files) within containers called "buckets." Think of buckets as top-level directories.

* **Globally Unique Name**: Each bucket must have a name that is globally unique across all AWS regions and all AWS accounts.
* **Region Defined**: Although S3 appears like a global service, buckets are defined at the **region level**. When you create a bucket, you specify the AWS region where it will reside.
* **Naming Convention**: Adhere to specific rules for bucket names:
    * No uppercase letters.
    * No underscores.
    * Between 3 and 63 characters long.
    * Cannot be formatted as an IP address.
    * Must start with a lowercase letter or a number.
    * Must NOT start with the prefix `xn--`.
    * Must NOT end with the suffix `-s3alias`.

### Amazon S3 - Objects

Objects are the fundamental entities stored in S3 buckets. An object consists of the data itself and its metadata.

* **Key (Full Path)**: Each object in a bucket has a unique `Key`. The key is the FULL path to the object within the bucket.
    * Examples:
        * `s3://my-bucket/my_file.txt`
        * `s3://my-bucket/my_folder1/another_folder/my_file.txt`
    * The key is composed of a **prefix** (e.g., `my_folder1/another_folder/`) and the **object name** (e.g., `my_file.txt`).
* **No "Directories" Concept**: Fundamentally, there is no concept of "directories" or "folders" within S3 buckets. The AWS console and other tools might visually represent them as such, but they are just part of the object's key using the slash (`/`) character as a delimiter.
* **Object Values (Body)**: The actual content of the file.
* **Max Object Size**: The maximum size for a single object is **5 TB (5000 GB)**.
* **Multi-Part Upload**: If you are uploading an object larger than 5 GB, you **must** use "multi-part upload," which breaks the object into smaller parts for more efficient and robust uploads.
* **Metadata**: A list of text key/value pairs associated with the object, including system-defined metadata (e.g., content-type) and user-defined metadata.
* **Tags**: Unicode key/value pairs (up to 10 per object). Useful for organizing objects, cost allocation, and security policies.
* **Version ID**: If versioning is enabled on the bucket, each object version will have a unique Version ID.

### S3 Security

Security in S3 is controlled through a combination of user-based and resource-based policies, along with encryption.

* **User-Based Security (IAM Policies)**:
    * IAM Policies are attached to IAM Users, Groups, or Roles.
    * They define which AWS API calls (e.g., `s3:GetObject`, `s3:PutObject`) a specific user or role is allowed to make on S3 resources.

* **Resource-Based Security**: These policies are attached directly to the S3 resources.

    * **Bucket Policies (Most Common)**:
        * JSON-based policies attached to the S3 bucket itself.
        * Define bucket-wide rules, including allowing public access with constraints, forcing objects to be encrypted upon upload, or granting cross-account access.
        * Commonly used to grant programmatic access to entire buckets or to specific prefixes.
    * **Object Access Control List (ACL)**:
        * Finer-grained permissions for individual objects.
        * Can be disabled at the bucket level (a recommended practice by AWS).
    * **Bucket Access Control List (ACL)**:
        * Legacy method for defining permissions at the bucket level.
        * Less common now, as Bucket Policies are more powerful and flexible. Can also be disabled.

* **Encryption**:
    * Encrypts objects in Amazon S3 to protect data at rest and in transit.
    * **Server-Side Encryption**: Default encryption where AWS encrypts the object as it's written to disk and decrypts it when accessed.
    * **Client-Side Encryption**: You encrypt the data before sending it to S3. The object is encrypted by you, then uploaded to S3, and potentially encrypted again by S3 depending on the server-side encryption settings.

### S3 Bucket Policies

S3 Bucket Policies are powerful JSON-based policies that attach directly to an S3 bucket to control permissions.

* **JSON-Based Policies**: Written in JSON format, similar to IAM policies.
* **Key Elements**:
    * `Resources`: Specifies the bucket and/or objects to which the policy applies.
    * `Effect`: `Allow` or `Deny` permission.
    * `Actions`: The set of S3 API calls (e.g., `s3:GetObject`, `s3:PutObject`, `s3:DeleteObject`) to allow or deny.
    * `Principal`: The AWS account, IAM user, or IAM role to which the policy applies.
* **Common Use Cases for S3 Bucket Policies**:
    * Granting public access to a bucket with specific constraints (e.g., read-only for certain IP ranges).
    * Forcing all objects uploaded to the bucket to be encrypted.
    * Granting cross-account access to your S3 bucket from another AWS account.
    * S3 can host static websites, and Bucket Policies are essential for making these sites publicly accessible over the internet.

### S3 Versioning

S3 Versioning allows you to keep multiple versions of an object in the same bucket.

* **Bucket-Level Setting**: Versioning must be enabled at the S3 bucket level.
* **New Version on Upload**: Whenever a user uploads an object with the same name/key to a version-enabled bucket, S3 creates a new version of that file instead of overwriting the existing one.
* **Version IDs**:
    * The first version of a file typically has a `null` version ID (unless created after versioning is enabled).
    * Subsequent uploads create new versions with unique version IDs.
* **Protection Against Deletion/Overwrites**: Versions are not deleted when you delete an object. A "delete marker" is created, and previous versions are retained. Any version can be retrieved, protecting against accidental deletions or overwrites.

### S3 Replication

S3 Replication allows you to automatically and asynchronously copy objects between buckets.

* **Versioning Required**: Versioning **must be enabled** on both the source and destination buckets for replication to work.
* **Types of Replication**:
    * **CRR (Cross-Region Replication)**: Replicates objects between buckets in different AWS Regions.
    * **SRR (Same-Region Replication)**: Replicates objects between buckets within the same AWS Region.
* **Cross-Account Replication**: Buckets can be in different AWS Accounts when replicating. Proper IAM Policies must be set on both source and destination buckets to handle permissions for the replication.
* **Asynchronous Copying**: The copying process happens asynchronously in the background.
* **Configuration**: Configured via a "replication rule" in the source bucket's management settings, where you specify the destination and an IAM role to grant permissions for the replication process.
* **Version ID Consistency**: The version IDs of replicated objects remain the same in both the source and destination buckets.

### S3 Storage Classes

S3 offers various storage classes, each optimized for specific data access patterns, performance needs, and cost requirements.

* **Amazon S3 Standard - General Purpose**:
    * **Use Cases**: Frequently accessed data, typically for daily access. Ideal for Big Data analytics, mobile & gaming applications, content distribution, dynamic websites.
    * **Characteristics**: Low latency and high throughput. Designed to sustain two concurrent facility failures.
* **Amazon S3 Standard-Infrequent Access (IA)**:
    * **Use Cases**: Data that is accessed less frequently (e.g., once a month) but requires rapid access when needed. Ideal for disaster recovery, backups, long-term storage of user-generated content.
    * **Characteristics**: Lower cost than S3 Standard. Incurs a retrieval fee.
* **Amazon S3 One Zone-Infrequent Access (One Zone-IA)**:
    * **Use Cases**: Storing secondary copies of on-premises data or data you can easily recreate. Suitable for infrequently accessed, non-critical data.
    * **Characteristics**: Even lower cost than Standard-IA. Data is stored in a single Availability Zone, making it less resilient to an AZ outage (not recommended for primary backups of critical data). Incurs a retrieval fee.
* **Amazon S3 Glacier Instant Retrieval**:
    * **Use Cases**: Low-cost object storage meant for archiving and backups where data needs to be retrieved within milliseconds. Great for data accessed once a quarter.
    * **Characteristics**: Millisecond retrieval time. Minimum storage duration of 90 days. Price for storage + object retrieval cost.
* **Amazon S3 Glacier Flexible Retrieval (formerly S3 Glacier)**:
    * **Use Cases**: Low-cost archiving data with flexible retrieval options ranging from minutes to hours. Suitable for data archives where retrieval time is less critical.
    * **Characteristics**: Retrieval options:
        * **Expedited**: 1 to 5 minutes (highest cost)
        * **Standard**: 3 to 5 hours
        * **Bulk**: 5 to 12 hours (often free or very low cost)
    * Minimum storage duration of 90 days.
* **Amazon S3 Glacier Deep Archive**:
    * **Use Cases**: The lowest-cost storage class for long-term archiving, suitable for data that is rarely accessed (e.g., once or twice a year).
    * **Characteristics**: Retrieval options:
        * **Standard**: 12 hours
        * **Bulk**: 48 hours
    * Minimum storage duration of 180 days.
* **Amazon S3 Intelligent-Tiering**:
    * **Use Cases**: When data access patterns are unknown or changing.
    * **Characteristics**: Includes a small monthly monitoring and auto-tiering fee. Automatically moves objects between access tiers (frequently accessed, infrequently accessed, archive access) based on usage patterns. **There are no retrieval charges** in S3 Intelligent-Tiering, only the monitoring fee.

### S3 Durability and Availability

* **Durability**:
    * S3 boasts incredibly high durability: **99.999999999% (eleven 9's)** of objects across multiple Availability Zones.
    * This means if you store 10,000,000 objects with Amazon S3, you can, on average, expect to incur a loss of only a single object once every 10,000 years.
    * This high durability is **consistent across all S3 storage classes**.
* **Availability**:
    * Measures how readily available a service is (uptime).
    * Varies depending on the specific storage class (e.g., S3 Standard has 99.99% availability, which equates to being unavailable for approximately 53 minutes per year).

### S3 Encryption

S3 provides robust encryption options to protect your data at rest and in transit.

* **Server-Side Encryption**:
    * Data is encrypted by AWS before being stored in the bucket and decrypted when retrieved.
    * **Default Encryption**: Can be configured at the bucket level to automatically encrypt all new uploads.
    * Options include:
        * **SSE-S3**: AWS-managed keys.
        * **SSE-KMS**: AWS Key Management Service (KMS) managed keys. Provides an audit trail.
        * **SSE-C**: Customer-provided encryption keys. AWS performs the encryption/decryption but doesn't store the key.
* **Client-Side Encryption**:
    * You encrypt the file before uploading it to S3.
    * The encrypted file is then uploaded to S3, and can optionally be encrypted again by the server-side encryption settings.

### IAM Access Analyzer for S3

IAM Access Analyzer helps you identify resources in your organization and accounts, such as S3 buckets, that are shared with an external entity.

* **Purpose**: Ensures that only intended people and accounts have access to your S3 buckets.
* **Evaluates Policies**: It evaluates S3 bucket policies, S3 ACLs, and S3 Access Point policies to flag public or cross-account access.

---

## Shared Responsibility Model for Amazon S3

Just like with other AWS services, the security of Amazon S3 operates under the Shared Responsibility Model, clearly defining what AWS is responsible for and what the user (you) is responsible for.

### AWS Responsibilities (Security *of* the Cloud)

AWS is responsible for the underlying infrastructure of Amazon S3, ensuring the service itself is secure, available, and performs as expected. This includes:

* **Infrastructure**: All aspects of the physical and virtual infrastructure that runs S3.
* **Durability and Availability**: Ensuring the inherent durability and availability of S3, including its ability to sustain concurrent losses of two facilities.
* **Internal Configuration & Vulnerability Analysis**: AWS's own internal security configurations, patching, and vulnerability analysis of the S3 service infrastructure.
* **Compliance Validation**: Internal compliance validation within their infrastructure to meet various security standards.

### Your Responsibilities (Security *in* the Cloud)

As a user of Amazon S3, you are responsible for how you configure and use the service to protect your data. This includes:

* **S3 Versioning**: Setting up S3 Versioning to protect against accidental deletions or overwrites of objects.
* **S3 Bucket Policies & ACLs**: Configuring appropriate S3 Bucket Policies and Access Control Lists (ACLs) to control who can access your data within the buckets, ensuring data protection.
* **Data Verification**: If you require data integrity verification (e.g., checksums), you are responsible for setting it up.
* **Logging and Monitoring**: Enabling and configuring logging (e.g., S3 access logs, CloudTrail logs) and monitoring (e.g., CloudWatch) to track access and activity on your S3 buckets. This is optional but highly recommended.
* **Cost-Optimal Storage Class**: Choosing the most cost-effective storage class for your data based on access patterns (e.g., S3 Standard, S3 Intelligent-Tiering, S3 Glacier, etc.).
* **Data Encryption**: Deciding whether and how to encrypt your data within your Amazon S3 buckets, both at rest and in transit. This includes choosing between S3-managed keys (SSE-S3), AWS Key Management Service (SSE-KMS), or customer-provided keys (SSE-C).

This clear delineation helps you understand where your security responsibilities lie when utilizing Amazon S3.

---

## Data Migration & Hybrid Cloud Services

AWS offers several services to help you migrate large datasets into and out of AWS, as well as to establish seamless connections between your on-premises environments and the AWS Cloud.

### AWS Snow Family (AWS Snowball)

The AWS Snow Family consists of highly secure, rugged, and portable physical devices designed for data migration and edge computing in environments with limited or no internet connectivity.

* **Primary Purpose**: To collect and process data at the edge and securely migrate petabytes of data into and out of AWS.
* **Data Migration**: Helps migrate up to petabytes of data quickly and securely, bypassing the need for high-bandwidth internet connections. Devices are shipped to your location, you load your data, and then ship them back to AWS for data transfer.
* **Edge Computing**:
    * **Concept**: Process data while it's being created at an "edge location." These locations often have limited internet connectivity and lack access to significant computing power.
    * **Snowball Edge for Edge Computing**: AWS Snowball Edge devices can be deployed to these edge locations to perform local compute and storage tasks.
    * **Functionality**: They can run EC2 instances or AWS Lambda functions directly on the device at the edge.
    * **Use Cases**: Preprocessing data, running Machine Learning (ML) inferences, data encoding, and then later sending the processed data to AWS for further storage and analysis.
* **Cost**: You typically pay for the device usage (per day) and data transfer out of AWS (data transfer *into* AWS is generally free for Snowball).

### AWS Storage Gateway

AWS Storage Gateway is a hybrid cloud storage service that bridges your on-premises environments with AWS cloud storage. It allows on-premises applications to seamlessly use AWS cloud storage as if it were local storage.

* **Primary Purpose**: Creates a seamless, low-latency connection between on-premises applications and cloud storage in Amazon S3, Amazon FSx, or Amazon EBS.
* **How it Works**: You deploy a virtual or hardware appliance in your data center, which connects to AWS storage services.
* **Key Use Cases**:
    * **Disaster Recovery**: Replicating on-premises data to AWS for disaster recovery purposes.
    * **Backup and Restore**: Centralizing backups from on-premises applications to cost-effective AWS storage.
    * **Tiered Storage**: Moving infrequently accessed data from expensive on-premises storage to more cost-effective cloud storage tiers in S3.
    * **Cloud Bursting**: Using cloud storage for peak workloads or seasonal demands.
