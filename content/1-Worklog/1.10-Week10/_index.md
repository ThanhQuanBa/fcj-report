---
title: "Week 10 Worklog"
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Week 10 Objectives:

- Understand the role and operation of **Amazon Simple Queue Service (SQS)** (Standard & FIFO).
- Master the **Publish/Subscribe** architecture using **Amazon Simple Notification Service (SNS)**.
- Become proficient in creating and configuring **AWS Step Functions** to orchestrate AWS services.
- Practice building an asynchronous workflow using SQS, SNS, and Lambda.

### Tasks to Be Completed This Week:

| Day | Tasks                                                                                                                                                                                                                                                                                                       | Start Date | End Date   | Reference Material                        |
| :-- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------- | :--------- | :---------------------------------------- |
| 2   | - Read and understand the architecture of **Amazon SQS** (message queue) and the differences between Standard Queue and FIFO Queue. <br> - **Hands-on:** Create an SQS Standard Queue and send/receive messages manually via Console.                                                                       | 10/11/2025 | 10/11/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Learn about **Amazon SNS** (Topic, Subscriber) and the Pub/Sub model. <br> - **Hands-on:** <br>&emsp; + Create an SNS Topic. <br>&emsp; + Create an SQS Queue and a Lambda function (from week 8) as subscribers to the Topic. <br>&emsp; + Send messages to the Topic and verify delivery to SQS/Lambda. | 11/11/2025 | 11/11/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Learn about **AWS Step Functions** (State Machine, Task State, Choice State). <br> - **Hands-on:** <br>&emsp; + Create a new Lambda function (e.g., `ProcessStep1`). <br>&emsp; + Create a simple State Machine (single Task step) to invoke this Lambda function.                                        | 12/11/2025 | 12/11/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Study how **Step Functions** orchestrate workflow logic (Sequence, Choice, Parallel). <br> - **Hands-on:** Extend the previously created State Machine: <br>&emsp; + Add a `Choice` step based on input results. <br>&emsp; + Integrate SQS (e.g., send a message to the queue if a branch is taken).     | 13/11/2025 | 13/11/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Resource Cleanup & Review:** <br> - **Hands-on:** <br>&emsp; + Delete the State Machine, SQS Queue, SNS Topic. <br>&emsp; + Review how these services (SQS, SNS, Step Functions) enable asynchronous communication and decoupling in applications.                                                      | 14/11/2025 | 14/11/2025 | <https://cloudjourney.awsstudygroup.com/> |

### Week 10 Achievements:

- **Message Queues:** Clearly understood SQS’s role in **decoupling** and **buffering** application workloads. Proficient in sending/receiving messages.
- **Pub/Sub Model:** Learned how **SNS** efficiently distributes messages to multiple subscribers.
- **Workflow Orchestration:** Understood how **Step Functions** orchestrate AWS services into organized, monitorable workflows.
- **Asynchronous Systems:** Capable of designing application architectures that integrate these services to improve flexibility and scalability.
