---
title: "Week 6 Worklog"
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:

- Understand the architecture and advantages of the NoSQL database **Amazon DynamoDB** (Key-Value/Document model).
- Become proficient in creating and managing DynamoDB tables and using different types of indexes (GSI/LSI).
- Master the role of **Amazon ElastiCache** in improving application response times.
- Practice creating and configuring an ElastiCache Cluster (Redis/Memcached).

### Tasks to be completed this week:

| Day | Task                                                                                                                                                                                                                                                                                       | Start Date | Completion Date | Reference Materials                       |
| :-- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------- | :-------------- | :---------------------------------------- |
| 2   | - Read and understand the architecture of **Amazon DynamoDB** (Primary Key, Partition Key, Sort Key). <br> - Learn concepts such as Read/Write Capacity Units (RCU/WCU) and On-Demand mode. <br> - **Practice:** Create your first DynamoDB table, insert data, and perform basic queries. | 13/10/2025 | 13/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Deep dive into Index types: **Local Secondary Index (LSI)** and **Global Secondary Index (GSI)**. <br> - **Practice:** Create a GSI for the DynamoDB table to support non-primary-key queries.                                                                                           | 14/10/2025 | 14/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Learn about the caching service **Amazon ElastiCache** (Redis and Memcached). <br> - Analyze the benefits of using a caching layer to offload traffic from RDS/DynamoDB. <br> - **Practice:** Launch an ElastiCache Cluster (e.g., Redis) in a Private Subnet.                           | 15/10/2025 | 15/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Learn how to **configure Security Groups** for ElastiCache to ensure secure connections from EC2. <br> - Understand how applications connect to and interact with ElastiCache. <br> - **Practice:** Configure the Security Group for the ElastiCache Cluster.                            | 16/10/2025 | 16/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Resource Cleanup & Review:** <br> - **Practice:** <br>&emsp; + Delete the created DynamoDB table. <br>&emsp; + Delete the ElastiCache Cluster. <br>&emsp; + Summarize the pros, cons, and use cases of RDS, DynamoDB, and ElastiCache.                                                 | 17/10/2025 | 17/10/2025      | <https://cloudjourney.awsstudygroup.com/> |

### Week 6 Achievements:

- **DynamoDB Fundamentals:** Gained a solid understanding of DynamoDB’s NoSQL model and became proficient in creating and managing tables and primary keys.
- **Query & Cost Optimization:** Understood the role of **GSI/LSI** and capacity modes (On-Demand/Provisioned) in optimizing query performance and cost.
- **ElastiCache Fundamentals:** Learned the differences and benefits of Redis and Memcached, and the importance of caching in modern architectures.
- **Caching Deployment:** Successfully launched an ElastiCache Cluster and configured secure network access through Security Groups.
- **Service Comparison:** Able to differentiate and choose among RDS, DynamoDB, and ElastiCache based on various data and performance requirements.
