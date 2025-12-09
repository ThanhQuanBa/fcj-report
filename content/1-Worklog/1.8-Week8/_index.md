---
title: "Week 8 Worklog"
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Week 8 Objectives:

- Understand the architecture, advantages, and core components of the serverless compute service **AWS Lambda**.
- Become proficient in creating, deploying, and managing Lambda functions.
- Master the role and configuration of **Amazon API Gateway** for building RESTful APIs.
- Practice building a simple serverless application by integrating Lambda with API Gateway.

### Tasks to Be Completed This Week:

| Day | Tasks                                                                                                                                                                                                                                                                                                         | Start Date | End Date   | Reference Material                        |
| :-- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :--------- | :--------- | :---------------------------------------- |
| 2   | - Read and understand **AWS Lambda** architecture (Function, Runtime, Handler, Execution Role). <br> - Compare Serverless (Lambda) vs Compute (EC2). <br> - **Hands-on:** Create and deploy a simple Lambda function (e.g., print "Hello World").                                                             | 27/10/2025 | 27/10/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Learn the **Event-Driven** model in Lambda. <br> - **Hands-on:** <br>&emsp; + Configure a Trigger so Lambda is invoked by S3 events (e.g., file upload). <br>&emsp; + Configure Lambda to read/write data to DynamoDB (using IAM Role permissions).                                                         | 28/10/2025 | 28/10/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Study **Amazon API Gateway** (REST API, Stage, Resource, Method). <br> - **Hands-on:** Create a new REST API in API Gateway. <br> - Define basic Resources and Methods (e.g., GET /items, POST /items).                                                                                                     | 29/10/2025 | 29/10/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Learn about **Integration Types** (Lambda Proxy Integration) between API Gateway and Lambda. <br> - **Hands-on:** <br>&emsp; + Connect the POST /items method to the Lambda function created on Day 2. <br>&emsp; + Deploy the API to a Stage (e.g., `prod`) and test through the public URL.               | 30/10/2025 | 30/10/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Security, Monitoring & Cleanup:** <br> - Learn about CORS (Cross-Origin Resource Sharing) and how to enable it on API Gateway. <br> - **Hands-on:** <br>&emsp; + Enable CORS for the API. <br>&emsp; + Check Lambda Logs in CloudWatch. <br>&emsp; + Delete the API Gateway and then the Lambda function. | 31/10/2025 | 31/10/2025 | <https://cloudjourney.awsstudygroup.com/> |

### Week 8 Achievements:

- **Lambda Fundamentals:** Fully understood the serverless model and became proficient in creating, configuring, and managing Lambda functions (Function, Role, Runtime).
- **Event-Driven Architecture:** Learned how Lambda works with event triggers and practiced integration with services like S3 and DynamoDB.
- **API Gateway Mastery:** Gained solid understanding of API Gateway architecture (Resource, Method, Stage) and how to build RESTful APIs.
- **Serverless Application:** Successfully implemented a basic serverless architecture (**API Gateway -> Lambda -> DynamoDB**), a common design pattern on AWS.
- **Security & Cleanup:** Learned how to manage Lambda logs via CloudWatch and properly clean up serverless resources.
