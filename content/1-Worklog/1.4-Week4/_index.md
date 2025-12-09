---
title: "Week 4 Worklog"
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives:

- Understand the role, architecture, and durability of **Amazon S3**.
- Become proficient in creating and managing **Buckets**, including applying security measures such as **Bucket Policy**.
- Master S3 storage classes (**Standard, IA, Glacier**) and how to use **Lifecycle Rules** for cost optimization.
- Practice deploying a **Static Website Hosting** solution on S3.

### Tasks to be completed this week:

| Day | Task                                                                                                                                                                                                                                                                                        | Start Date | Completion Date | Reference Materials                       |
| :-- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :--------- | :-------------- | :---------------------------------------- |
| 2   | - Read and understand the basic architecture of **Amazon S3** (Object, Key, Bucket, Region). <br> - Learn about security features: Block Public Access, Access Control List (ACL). <br> - **Practice:** Create a new S3 Bucket and configure Block Public Access settings.                  | 29/09/2025 | 29/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Learn about **S3 Storage Classes** (Standard, Standard-IA, One Zone-IA, Glacier, Deep Archive). <br> - Learn how **Lifecycle Rules** automate transitions between classes. <br> - **Practice:** Configure a Lifecycle Rule to transition older objects to Standard-IA.                    | 30/09/2025 | 30/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Learn about **Versioning** and how to restore deleted objects. <br> - Learn about **Bucket Policies** to centrally control access. <br> - **Practice:** Enable Versioning and test deleting/restoring an object.                                                                          | 01/10/2025 | 01/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Learn about **Static Website Hosting** on S3. <br> - **Practice:** <br>&emsp; + Upload HTML/CSS/JS files (Index.html & Error.html). <br>&emsp; + Enable Static Website Hosting and configure a Bucket Policy for public read access. <br>&emsp; + Access the website via the S3 endpoint. | 02/10/2025 | 02/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Cleanup & S3 CLI Review:** <br> - **Practice:** <br>&emsp; + Use **AWS CLI** to upload/download/sync S3 data (`aws s3 cp`, `aws s3 sync`). <br>&emsp; + **Cleanup:** Disable Versioning, delete all objects (including older versions), then delete the Bucket.                         | 03/10/2025 | 03/10/2025      | <https://cloudjourney.awsstudygroup.com/> |

### Week 4 Achievements:

- **S3 Management:** Successfully created, configured, and managed S3 Buckets; understood the role of **Block Public Access** and **ACL**.
- **Cost Optimization:** Gained solid knowledge of S3 **Storage Classes** and practiced using **Lifecycle Rules** to automate data transitions and optimize storage costs.
- **Security & Control:** Successfully enabled/disabled **Versioning** and applied **Bucket Policies** to manage public access and IAM permissions.
- **Practical Application:** Successfully deployed a static website on S3 and accessed it through the public endpoint.
- **CLI Operations:** Became proficient with AWS CLI commands for S3 data management (upload, download, sync).
