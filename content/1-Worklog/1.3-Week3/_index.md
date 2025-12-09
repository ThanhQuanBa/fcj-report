---
title: "Week 3 Worklog"
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:

- Understand the role and core components of **Amazon Virtual Private Cloud (VPC)**.
- Become proficient in creating and configuring VPC, Subnets (Public/Private), Internet Gateway (IGW), and Route Tables.
- Master the two-layer security mechanisms: **Security Group (SG)** and **Network Access Control List (NACL)**.
- Practice deploying an EC2 Instance into a custom VPC.

### Tasks to be completed this week:

| Day | Task                                                                                                                                                                                                                                                                                                                                  | Start Date | Completion Date | Reference Materials                       |
| :-- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :--------- | :-------------- | :---------------------------------------- |
| 2   | - Read and understand **VPC architecture**: IP concepts, CIDR Block, Subnets, Availability Zones (AZ). <br> - **Practice:** Create a new VPC with a custom CIDR block (e.g., `10.0.0.0/16`). <br> - Create two Subnets: one Public and one Private.                                                                                   | 22/09/2025 | 22/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Learn about **Internet Gateway (IGW)**, **Route Table**, and their role in providing Internet access. <br> - **Practice:** <br>&emsp; + Create and attach an IGW to the VPC. <br>&emsp; + Configure the **Public Route Table** to route traffic to the IGW. <br>&emsp; + Associate the Public Route Table with the Public Subnet.   | 23/09/2025 | 23/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Learn about **Security Groups (SG)** (Stateful) and their operation principles. <br> - **Practice:** <br>&emsp; + Launch an EC2 Instance in the Public Subnet. <br>&emsp; + Configure SG to allow only SSH/RDP (Port 22/3389) from your personal IP. <br>&emsp; + Try accessing the instance to validate SG security.               | 24/09/2025 | 24/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Learn about **Network Access Control List (NACL)** (Stateless). <br> - **Practice:** <br>&emsp; + Configure NACL for the Public Subnet (experiment with Deny rules). <br>&emsp; + Clearly distinguish inbound/outbound behavior differences between SG and NACL. <br>&emsp; + Learn the concept of NAT Gateway (in Private Subnet). | 25/09/2025 | 25/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Cleanup & Review:** <br> - **Practice:** <br>&emsp; + Terminate the EC2 Instance. <br>&emsp; + **Delete all VPC components** created earlier in the correct order (Detach IGW → Delete Subnets → Delete Route Tables → Delete VPC) to avoid unnecessary costs. <br>&emsp; + Summarize all VPC components learned.                 | 26/09/2025 | 26/09/2025      | <https://cloudjourney.awsstudygroup.com/> |

### Week 3 Achievements:

- **VPC Configuration:** Successfully understood and created VPC, Public/Private Subnets, Internet Gateway, and Route Tables for traffic routing.
- **Network Security:** Mastered and differentiated the two-layer security mechanisms: **Security Group** (Stateful, instance-level) and **NACL** (Stateless, subnet-level).
- **Basic Deployment:** Successfully deployed an EC2 Instance within a custom VPC and verified Internet connectivity.
- **Cleanup Skills:** Properly cleaned up all networking resources in the correct order (ensuring no unnecessary leftover components), contributing to efficient cost management.
