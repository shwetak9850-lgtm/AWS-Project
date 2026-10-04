# Task 3 – Highly Available Web Application using ALB and Auto Scaling

## 📌 Objective

Deploy a web application using Amazon EC2, an Application Load Balancer, a Target Group, and an Auto Scaling Group. The EC2 instances run in private subnets across two Availability Zones, while the Application Load Balancer is placed in public subnets.

## 🏗️ Architecture

```text
                         Internet
                            │
                            ▼
                 Application Load Balancer
                       (Public Subnets)
                    /                    \
                   /                      \
                  ▼                        ▼
          Private Subnet 1          Private Subnet 2
             EC2 Instance              EC2 Instance
                  \                        /
                   \                      /
                    └── Auto Scaling ────┘
                            │
                            ▼
                       NAT Gateway
                            │
                            ▼
                         Internet
```

## 🛠️ AWS Services Used

- Amazon EC2
- EC2 Launch Template
- Application Load Balancer
- Target Group
- Auto Scaling Group
- Amazon VPC
- NAT Gateway
- Security Group

## ⚙️ Implementation

### 1. Create Launch Template

Launch Template:

```text
AWS-WebApp-LaunchTemplate
```

Configuration:

- AMI: Amazon Linux 2023
- Instance type: `t3.micro`
- Key pair: Existing key pair
- Security Group: `Project-SG`
- Subnet: Not selected in the Launch Template

User data:

```bash
#!/bin/bash

dnf update -y
dnf install -y httpd

systemctl enable httpd
systemctl start httpd

echo "<html>
<head>
<title>AWS Web Application</title>
</head>
<body>
<h1>AWS Web Application</h1>
<p>Hello from EC2 Auto Scaling</p>
<p>Hostname: $(hostname)</p>
</body>
</html>" > /var/www/html/index.html
```

The user data automatically installs and starts Apache HTTP Server and creates a simple web page when an EC2 instance starts.

### 2. Create Target Group

Target Group:

```text
AWS-Web-TG
```

Configuration:

- Target type: Instances
- Protocol: HTTP
- Port: `80`
- VPC: `AWS-Project-VPC-vpc`
- Health check protocol: HTTP
- Health check path: `/`

The target group receives traffic from the Application Load Balancer and checks the health of EC2 instances.

### 3. Create Application Load Balancer

Load Balancer:

```text
AWS-Web-ALB
```

Configuration:

- Type: Application Load Balancer
- Scheme: Internet-facing
- IP address type: IPv4
- VPC: `AWS-Project-VPC-vpc`
- Availability Zones:
  - `us-east-1a` → Public Subnet 1
  - `us-east-1b` → Public Subnet 2
- Security Group: `Project-SG`
- Listener: HTTP port `80`
- Default action: Forward to `AWS-Web-TG`

The ALB distributes incoming HTTP traffic across healthy EC2 instances.

### 4. Create Auto Scaling Group

Auto Scaling Group:

```text
AWS-Web-ASG
```

Launch Template:

```text
AWS-WebApp-LaunchTemplate
```

Network configuration:

- VPC: `AWS-Project-VPC-vpc`
- Private Subnet 1 → `us-east-1a`
- Private Subnet 2 → `us-east-1b`

Load balancing:

```text
AWS-Web-TG
```

Capacity:

| Setting | Value |
|---|---:|
| Desired capacity | 2 |
| Minimum capacity | 2 |
| Maximum capacity | 4 |

Health check:

- EC2 health checks
- Elastic Load Balancing health checks enabled

Scaling policy:

- Target tracking
- Average CPU utilization
- Target: 50%

## 🔍 Verification

### EC2 Instances

The Auto Scaling Group automatically launched two EC2 instances.

Expected:

```text
Instance 1 → us-east-1a → Private Subnet 1
Instance 2 → us-east-1b → Private Subnet 2
```

Both instances should be in the:

```text
Running
```

state.

### Target Group Health

Open:

**EC2 → Target Groups → AWS-Web-TG → Targets**

Both instances should show:

```text
Healthy
```

### Web Application Test

Copy the DNS name of:

```text
AWS-Web-ALB
```

Open it in a web browser using HTTP.

Expected output:

```text
AWS Web Application

Hello from EC2 Auto Scaling

Hostname: <instance-hostname>
```

The hostname confirms that the request is being served by an EC2 instance behind the load balancer.

## 📸 Screenshots

Add the following screenshots:

```text
screenshots/task3/
├── 01-launch-template.png
├── 02-target-group.png
├── 03-load-balancer.png
├── 04-auto-scaling-group.png
├── 05-running-ec2-instances.png
├── 06-healthy-targets.png
├── 07-working-web-application.png
└── 08-asg-final-configuration.png
```

> Replace the filenames with the actual screenshot filenames used in the repository.

## ✅ Result

A highly available web application architecture was successfully deployed.

The final flow is:

```text
Internet
   ↓
Application Load Balancer
   ↓
Target Group
   ↓
EC2 Instances in Private Subnets
   ↓
Auto Scaling Group
```

The application was successfully accessed through the ALB DNS name, and the target EC2 instances were verified as healthy.

## 📚 Key Learnings

- Creating EC2 Launch Templates
- Installing Apache using EC2 User Data
- Creating Target Groups
- Configuring Application Load Balancers
- Deploying EC2 instances in private subnets
- Configuring Auto Scaling Groups
- Performing load balancer health checks
- Testing a web application through an ALB
- Understanding highly available AWS architecture
