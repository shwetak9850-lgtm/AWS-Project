# Task 2 – Custom AWS VPC Network Architecture

## 📌 Objective

Create a custom Amazon VPC with public and private subnets across two Availability Zones, configure Internet Gateway and NAT Gateway connectivity, create route tables, and configure a Security Group.

## 🏗️ Architecture

```text
                         Internet
                            │
                            ▼
                  Internet Gateway (IGW)
                            │
                ┌───────────┴───────────┐
                │       VPC             │
                │    10.0.0.0/16        │
                │                       │
                │  ┌────────┐ ┌────────┐│
                │  │Public 1│ │Public 2││
                │  │10.0.1/24│ │10.0.2/24│
                │  └───┬────┘ └────────┘│
                │      │                 │
                │  NAT Gateway           │
                │      │                 │
                │  ┌─────────┐ ┌─────────┐
                │  │Private 1│ │Private 2│
                │  │10.0.11/24││10.0.12/24│
                │  └─────────┘ └─────────┘
                │
                └─────────────────────────
```

## 🛠️ AWS Services Used

- Amazon VPC
- Subnets
- Internet Gateway
- NAT Gateway
- Route Tables
- Elastic IP
- Security Group

## ⚙️ VPC Configuration

### VPC

| Setting | Value |
|---|---|
| Name | `AWS-Project-VPC-vpc` |
| CIDR | `10.0.0.0/16` |

### Availability Zones

- `us-east-1a`
- `us-east-1b`

### Subnets

| Subnet | Type | CIDR | Availability Zone |
|---|---|---|---|
| Public Subnet 1 | Public | `10.0.1.0/24` | `us-east-1a` |
| Public Subnet 2 | Public | `10.0.2.0/24` | `us-east-1b` |
| Private Subnet 1 | Private | `10.0.11.0/24` | `us-east-1a` |
| Private Subnet 2 | Private | `10.0.12.0/24` | `us-east-1b` |

## 🌐 Internet Gateway

Internet Gateway:

```text
AWS-Project-VPC-igw
```

The Internet Gateway provides internet connectivity for resources in public subnets through the public route table.

## 🌍 NAT Gateway

NAT Gateway:

```text
Project-NAT-Gateway
```

Configuration:

- Type: Public
- Availability mode: Zonal
- Subnet: Public Subnet 1
- Elastic IP: Allocated

The NAT Gateway allows resources in private subnets to access the internet for outbound traffic without making the private resources directly accessible from the internet.

## 🛣️ Route Tables

### Public Route Table

Name:

```text
Public-RT
```

Routes:

```text
10.0.0.0/16 → local
0.0.0.0/0   → Internet Gateway
```

Associated with:

- Public Subnet 1
- Public Subnet 2

### Private Route Table

Name:

```text
Private-RT
```

Routes:

```text
10.0.0.0/16 → local
0.0.0.0/0   → NAT Gateway
```

Associated with:

- Private Subnet 1
- Private Subnet 2

## 🔐 Security Group

Security Group:

```text
Project-SG
```

Inbound rules:

| Protocol | Port | Source | Purpose |
|---|---:|---|---|
| SSH | 22 | My IP | Secure administration |
| HTTP | 80 | `0.0.0.0/0` | Web access |

## 📸 Screenshots

Add the following screenshots:

```text
screenshots/task2/
├── 01-vpc.png
├── 02-subnets.png
├── 03-internet-gateway.png
├── 04-nat-gateway.png
├── 05-public-route-table.png
├── 06-private-route-table.png
├── 07-security-group.png
└── 08-resource-map.png
```

> Replace the filenames with the actual screenshot filenames used in the repository.

## ✅ Result

A custom VPC network was successfully created with:

- One VPC
- Four subnets
- Two Availability Zones
- Internet Gateway
- NAT Gateway
- Public and private route tables
- Security Group

This VPC was later used by the web application architecture in Task 3.

## 📚 Key Learnings

- Creating a custom VPC
- Designing public and private subnets
- Working with Availability Zones
- Configuring Internet Gateway and NAT Gateway
- Creating and associating route tables
- Configuring Security Groups
- Understanding public vs private network architecture
