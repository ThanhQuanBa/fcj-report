---
title: "Week 5 Worklog"
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:

- Understand the role and benefits of **Amazon RDS** compared to managing databases manually on EC2.
- Become proficient in creating and connecting to a DB Instance (e.g., MySQL or PostgreSQL).
- Master key RDS features: **Multi-AZ** (High Availability), **Read Replicas** (Read Scalability), and **Snapshot/Backup**.
- Practice managing network security for DB Instances using Security Groups.

### Tasks to be completed this week:

| Day | Task                                                                                                                                                                                                                                                                                                     | Start Date | Completion Date | Reference Materials                       |
| :-- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------- | :-------------- | :---------------------------------------- |
| 2   | - Read and understand the architecture of **Amazon RDS** (DB Instance, Engine, Master Username/Password, DB Security Group). <br> - Compare RDS with self-managed databases on EC2. <br> - **Practice:** Launch a DB Instance (Free Tier) in the VPC created in Week 3 (choose a Private Subnet).        | 06/10/2025 | 06/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Learn how to connect to and secure an RDS instance. <br> - **Practice:** <br>&emsp; + Configure the **Security Group** to allow access only from the **EC2 Security Group** created in Week 2. <br>&emsp; + Use a database client or EC2 instance to connect and create a basic table.                 | 07/10/2025 | 07/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Learn about **Multi-AZ Deployment** to ensure High Availability (HA). <br> - Learn about **Automated Backups** and **DB Snapshots**. <br> - **Practice:** Enable Multi-AZ for the instance and create a manual DB Snapshot.                                                                            | 08/10/2025 | 08/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Learn about **Read Replicas** to offload read traffic. <br> - **Practice:** <br>&emsp; + Create a Read Replica for the primary DB Instance. <br>&emsp; + Simulate connecting an application to the Read Replica for read scaling. <br>&emsp; + Learn how to promote a Read Replica to a standalone DB. | 09/10/2025 | 09/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Cleanup & Optimization:** <br> - **Practice:** <br>&emsp; + Delete the Read Replica. <br>&emsp; + Delete the primary DB Instance (Note: Uncheck “Final Snapshot” if not needed). <br>&emsp; + Clean up all related Snapshots and Security Groups.                                                    | 10/10/2025 | 10/10/2025      | <https://cloudjourney.awsstudygroup.com/> |

### Week 5 Achievements:

- **RDS Management:** Gained a solid understanding of RDS benefits and became proficient in creating, configuring, and connecting to DB Instances.
- **Network Security:** Learned how to use Security Groups to restrict database access to specific resources (e.g., EC2), ensuring network-level protection.
- **High Availability:** Successfully practiced enabling **Multi-AZ** to protect databases from AZ failures.
- **Scalability:** Understood and practiced creating **Read Replicas** to improve read performance and reduce load on the primary DB.
- **Backup & Recovery:** Learned how to create **DB Snapshots** and how RDS handles **Automated Backups**.
