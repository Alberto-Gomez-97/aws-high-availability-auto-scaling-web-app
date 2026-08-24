# aws-high-availability-auto-scaling-web-app

# High Availability and Auto-Scalable Web Application on AWS

## Overview

This project demonstrates the design and implementation of a highly available and auto-scalable web application infrastructure using Amazon Web Services (AWS).

The infrastructure was built using Amazon EC2, Application Load Balancer (ALB), Target Groups, Auto Scaling Groups (ASG), Security Groups and Amazon CloudWatch.

The project focused on distributing application traffic across multiple EC2 instances, detecting instance failures, maintaining a minimum number of healthy instances and dynamically scaling the infrastructure based on CPU utilization.

---

## Objectives

- Deploy a web application using multiple Amazon EC2 instances.
- Implement an Application Load Balancer to distribute incoming HTTP traffic.
- Configure a Target Group to manage the EC2 instances.
- Configure health checks to detect unhealthy instances.
- Restrict direct access to the EC2 instances and allow traffic through the ALB.
- Implement an Auto Scaling Group to maintain the desired number of instances.
- Configure dynamic scaling policies based on CPU utilization.
- Simulate an EC2 instance failure and validate automatic recovery.
- Generate CPU load to validate dynamic scaling behavior.
- Validate the infrastructure using the Application Load Balancer DNS endpoint.

---

## AWS Services

| AWS Service | Purpose |
|---|---|
| Amazon EC2 | Hosts the web application instances |
| Application Load Balancer | Distributes HTTP traffic between instances |
| Target Group | Groups and monitors the EC2 instances |
| Auto Scaling Group | Maintains and automatically adjusts the number of instances |
| Amazon CloudWatch | Monitors metrics and supports scaling policies |
| Security Groups | Controls inbound traffic to the ALB and EC2 instances |

---

## Architecture

The infrastructure consists of multiple EC2 instances behind an Application Load Balancer.

The ALB provides a single access point for the web application and distributes incoming HTTP traffic across the instances registered in the Target Group.

The Auto Scaling Group manages the EC2 instances and is responsible for maintaining the desired capacity and launching replacement instances when required.

### Architecture Diagram

![AWS High Availability Architecture](architecture/architecture-diagram.png)

---

## Infrastructure Components

### Amazon EC2

Two EC2 instances were initially launched to host the web application.

The instances were registered in the Target Group used by the Application Load Balancer.

### Application Load Balancer

The Application Load Balancer was configured as the public entry point for the application.

The ALB distributes HTTP traffic to the healthy instances registered in the Target Group.

The application was accessed through the DNS name assigned to the Load Balancer rather than directly through the public IP addresses of the EC2 instances.

### Target Group

A Target Group was created to group the EC2 instances.

Health checks were used to determine whether the instances were available to receive traffic.

When an instance was stopped, the Target Group detected the change in health status and the ALB stopped routing traffic to the unhealthy instance.

### Security Groups

Security Groups were configured to control traffic between the Application Load Balancer and the EC2 instances.

The EC2 security group was configured to allow HTTP traffic from the security group associated with the ALB.

This configuration prevents users from accessing the application directly through the EC2 public IP address.

### Auto Scaling Group

An Auto Scaling Group was configured to manage the EC2 instances.

The configuration included a minimum desired capacity of two instances.

When an instance was terminated, the Auto Scaling Group automatically launched a replacement instance to maintain the configured capacity.

### Amazon CloudWatch

CloudWatch metrics were used to monitor CPU utilization and support the dynamic scaling policy.

CPU load was generated on an EC2 instance to validate the scaling behavior.

---

## Implementation

The implementation was performed in the following stages:

1. Launch two EC2 instances.
2. Configure the instances for web application access.
3. Create a Target Group.
4. Register both EC2 instances in the Target Group.
5. Configure health checks.
6. Create an Application Load Balancer.
7. Configure the ALB listener for HTTP traffic.
8. Associate the Target Group with the Load Balancer.
9. Validate application access through the ALB DNS name.
10. Configure Security Groups to restrict direct EC2 access.
11. Simulate an EC2 instance failure.
12. Validate Target Group health detection.
13. Create an Auto Scaling Launch Template.
14. Configure an Auto Scaling Group.
15. Associate the Auto Scaling Group with the Load Balancer.
16. Configure the desired, minimum and maximum instance capacity.
17. Configure a dynamic scaling policy.
18. Generate CPU load on an EC2 instance.
19. Monitor the resulting activity through CloudWatch.
20. Validate the creation of additional instances.

---

## High Availability Test

To validate the behavior of the infrastructure, one of the EC2 instances was stopped.

The Target Group detected the instance as unhealthy and the Load Balancer stopped sending traffic to the failed instance.

The instance was subsequently started again to restore the original configuration.

This test demonstrated the health-check and traffic-management behavior of the Application Load Balancer.

---

## Auto Scaling Test

The Auto Scaling Group was configured to maintain a minimum capacity of two EC2 instances.

One instance was removed to simulate an infrastructure failure.

The Auto Scaling Group detected that the number of instances was below the configured capacity and automatically launched a replacement instance.

This validated the automatic recovery capability of the infrastructure.

---

## Dynamic Scaling Test

A dynamic scaling policy was configured based on CPU utilization.

CPU load was generated on an EC2 instance using the Linux `stress` utility.

```bash
sudo amazon-linux-extras install epel -y
sudo yum install stress -y
stress -C 4
