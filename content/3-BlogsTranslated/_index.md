---
title: "Translated Blogs"
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

### [Blog 1 - Building resilient multi-cluster applications with Amazon EKS, Part 1: Deploying cross-cluster load balancing with NLB](3.1-Blog1/)

This blog post provides guidance on how to build resilient multi-cluster applications with Amazon EKS using new features in the AWS Load Balancer Controller (LBC). These features allow a Network Load Balancer (NLB) to distribute traffic across targets located in multiple EKS clusters, improving application resilience and availability. The article includes step-by-step instructions for configuring multi-cluster load balancing for both existing workloads and new deployments.

### [Blog 2 - Accelerating security findings triage using automated business context enrichment in AWS Security Hub CSPM](3.2-Blog2/)

This blog explains how to use the “Automate rules with custom functions” feature in AWS Security Hub CSPM to speed up the evaluation of security findings.

1. By writing custom Lambda functions, you can automatically collect and enrich findings with important business context (such as environment, owner, or function). This helps to:

2. Reduce triage time: Eliminates manual work to look up asset information.

Accelerate decision-making: Immediately provides necessary context to prioritize and remediate risks.

3. Improve operational efficiency: Allows teams to focus on the most critical issues.

The blog also provides an example of enriching Amazon Inspector findings with context from AWS Systems Manager to demonstrate the benefits of this approach.

### [Blog 3 - A scalable, elastic vector search and database solution for over 1 billion vectors built on LanceDB and Amazon S3](3.3-Blog3/)

This blog describes how Metagenomi, in collaboration with AWS, built a highly scalable, elastic, and cost-efficient vector search and database solution to manage more than 1 billion protein vectors for enzyme discovery used in gene editing.

Key highlights of the solution:

Challenges: Efficiently screening billions of protein sequences to identify high-value enzyme candidates.

Core technology: Using LanceDB (an open-source vector database) operating directly on Amazon S3 storage. This replaces expensive disk-based storage with low-cost object storage.

Serverless architecture: Using AWS Lambda and AWS Step Functions to perform on-demand vector search queries. No continuous server provisioning is required, significantly reducing operational costs.

Processing workflow:

- Convert protein sequences into vectors using AI models.
- Shard large-scale datasets into smaller partitions for parallel processing.
- Index and store these partitions on S3.
- Use Lambda functions to perform parallel vector searches across partitions and aggregate results.

Outcome: The system enables high-speed vector search (within seconds), scales horizontally as data grows, and significantly reduces operational expenses.
