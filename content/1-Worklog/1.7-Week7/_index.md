---
title: "Week 7 Worklog"
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives:

- Clearly understand the role and configuration of **EC2 Auto Scaling Group (ASG)** for automatic scaling in and out.
- Understand the architecture and operation of the **Elastic Load Balancer (ELB)** (especially ALB) for traffic distribution.
- Become proficient in using **Amazon CloudWatch** to collect metrics, logs, build Dashboards, and configure Alarms.
- Practice setting up **Scaling Policies** based on monitoring metrics.

### Tasks to Be Completed This Week:

| Day | Tasks                                                                                                                                                                                                                                                                                                            | Start Date | End Date   | Reference Material                        |
| :-- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------- | :--------- | :---------------------------------------- |
| 2   | - Read and understand the architecture of **Auto Scaling Group (ASG)** and Launch Template. <br> - Study types of Load Balancers (Classic, Application, Network). <br> - **Hands-on:** Create a Launch Template and configure a basic ASG.                                                                       | 20/10/2025 | 20/10/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Deep dive into **Application Load Balancer (ALB)** and its components (Listener, Target Group, Health Check). <br> - **Hands-on:** <br>&emsp; + Create an ALB and Target Group. <br>&emsp; + Register ASG EC2 Instances into the Target Group.                                                                 | 21/10/2025 | 21/10/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Study **Amazon CloudWatch** (Metrics, Logs, Events). <br> - **Hands-on:** <br>&emsp; + View EC2, ALB, and ASG metrics on CloudWatch. <br>&emsp; + Create a CloudWatch Dashboard to monitor key metrics (CPU Utilization, Request Count).                                                                       | 22/10/2025 | 22/10/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Learn about **CloudWatch Alarms** and types of **Scaling Policies** (Target Tracking, Step Scaling). <br> - **Hands-on:** <br>&emsp; + Create a CloudWatch Alarm based on CPU Utilization (e.g., > 80%). <br>&emsp; + Configure a Scaling Policy for ASG using the created Alarm (scale out when CPU is high). | 23/10/2025 | 23/10/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Resource Review & Cleanup:** <br> - **Hands-on:** <br>&emsp; + Validate the Scale Out/In process. <br>&emsp; + **Cleanup:** Delete CloudWatch Alarms, delete ASG (which will terminate EC2 instances), delete ALB, and delete the Launch Template.                                                           | 24/10/2025 | 24/10/2025 | <https://cloudjourney.awsstudygroup.com/> |

### Week 7 Achievements:

- **Scalability:** Successfully deployed an application architecture capable of automatic scaling (Scale Out/In) using **ASG** and **Launch Template**.
- **Load Balancing:** Understood and configured **Application Load Balancer (ALB)** to distribute traffic and perform effective health checks.
- **Observability:** Gained solid understanding of **CloudWatch** for collecting, visualizing (Dashboard), and monitoring resource health metrics.
- **Automation:** Successfully set up performance-based automatic scaling using **Scaling Policies** and **CloudWatch Alarms**.
- **Cost Management:** Learned how to properly clean up expensive resources such as ALB and ASG.
