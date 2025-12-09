---
title: "Week 2 Worklog"
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:

- Understand the role and operation of the virtual server service **EC2** (Elastic Compute Cloud).
- Become proficient in launching, managing, and terminating an EC2 instance.
- Master the concept and usage of **IAM Roles** to grant secure access to EC2 resources.
- Use **AWS CLI** to perform basic EC2 management operations.

### Tasks to be completed this week:

| Day | Task                                                                                                                                                                                                                                                                         | Start Date | Completion Date | Reference Materials                       |
| :-- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------- | :-------------- | :---------------------------------------- |
| 2   | - Read and understand core components of **Amazon EC2** (AMI, Instance Type, Key Pair, Security Group). <br> - **Practice:** Launch the first EC2 Instance (Free Tier type).                                                                                                 | 15/09/2025 | 15/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Learn about secure access control using **IAM Roles for EC2** (Instance Profile). <br> - **Practice:** <br>&emsp; + Create an IAM Policy allowing `s3:ListBucket`. <br>&emsp; + Create an IAM Role and attach the policy. <br>&emsp; + Assign the Role to an EC2 Instance. | 16/09/2025 | 16/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Learn EC2 management commands using **AWS CLI**. <br> - **Practice:** <br>&emsp; + Use CLI to view EC2 instance info (`describe-instances`). <br>&emsp; + Use CLI to Stop/Start an Instance. <br>&emsp; + Manage Security Groups via CLI.                                  | 17/09/2025 | 17/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - **Integration Practice:** Verify IAM Role permissions. <br> - Connect to the EC2 instance via SSH/Session Manager. <br> - **Practice:** Run `aws s3 ls` from inside EC2 to verify it can list S3 Buckets (confirming IAM Role works).                                      | 18/09/2025 | 18/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Lifecycle & Cost Management:** <br> - Review EC2 lifecycle states (Pending, Running, Stopping, Terminated). <br> - **Practice:** <br>&emsp; + **Terminate** the EC2 Instance. <br>&emsp; + Delete created IAM Roles and Policies for resource cleanup.                   | 19/09/2025 | 19/09/2025      | <https://cloudjourney.awsstudygroup.com/> |

### Week 2 Achievements:

- **EC2:** Gained a solid understanding of how to create, configure, and manage the lifecycle of EC2 Instances via both Console and CLI. Clearly understood the components that make up an EC2 instance.
- **IAM Roles:** Learned the benefits and usage of **IAM Roles** (Instance Profile) to securely grant AWS service permissions to EC2 without the need for storing Access Keys on servers.
- **Advanced AWS CLI:** Able to use CLI commands to manage EC2 instance states (Start, Stop, Describe) and perform remote operations.
- **Integration Practice:** Successfully validated that the EC2 Instance can perform S3 actions (e.g., `aws s3 ls`) thanks to permissions granted through IAM Role—confirming the security model works effectively.
- **Resource Cleanup:** Understood the process of terminating EC2 instances and removing related IAM resources to ensure no unwanted cost is incurred.
