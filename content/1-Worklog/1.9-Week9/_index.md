---
title: "Week 9 Worklog"
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Week 9 Objectives:

- Understand the architecture and role of **Amazon Kinesis** data streaming services (Data Streams, Firehose).
- Master key concepts of **AWS Glue** (Data Catalog, Crawler, ETL Jobs) within the ETL (Extract, Transform, Load) process.
- Become proficient in creating and managing the **Glue Data Catalog** to store metadata.
- Practice integrating Kinesis Firehose to deliver streaming data into S3.

### Tasks to Be Completed This Week:

| Day | Tasks                                                                                                                                                                                                                                                                                        | Start Date | End Date   | Reference Material                        |
| :-- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------- | :--------- | :---------------------------------------- |
| 2   | - Read and understand the architecture of **Amazon Kinesis Data Streams (KDS)** (Shard, Producer, Consumer). <br> - Study **Amazon Kinesis Data Firehose** for simplified data ingestion. <br> - **Hands-on:** Create a Kinesis Data Firehose Delivery Stream.                               | 03/11/2025 | 03/11/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Learn about the **AWS Glue Data Catalog** (Metadata storage) and **Glue Crawler**. <br> - **Hands-on:** <br>&emsp; + Create an IAM Role for the Glue Crawler. <br>&emsp; + Create a Glue Crawler to scan sample data in an S3 Bucket (from week 4) and update the Data Catalog.            | 04/11/2025 | 04/11/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Study **AWS Glue ETL Jobs** (Extract, Transform, Load) and execution environments (Spark, Python Shell). <br> - **Hands-on:** <br>&emsp; + Create a basic Glue ETL Job (using Python Shell). <br>&emsp; + Configure the Job to read data from the Data Catalog and write the output to S3. | 05/11/2025 | 05/11/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - **Kinesis Integration Practice:** <br> - **Hands-on:** <br>&emsp; + Configure the previously created Kinesis Firehose Delivery Stream to deliver data to S3. <br>&emsp; + Simulate data ingestion into Firehose and verify that it is stored in S3.                                        | 06/11/2025 | 06/11/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Resource Cleanup & Review:** <br> - **Hands-on:** <br>&emsp; + Delete Glue Jobs, Glue Crawlers, and tables in the Data Catalog. <br>&emsp; + Delete the Kinesis Data Firehose Delivery Stream. <br>&emsp; + Summarize the roles of Kinesis (speed) and Glue (batch/ETL).                 | 07/11/2025 | 07/11/2025 | <https://cloudjourney.awsstudygroup.com/> |

### Week 9 Achievements:

- **Streaming Processing:** Understood real-time data streaming with Kinesis and how Firehose simplifies data ingestion.
- **Metadata Management:** Became proficient in using **Glue Data Catalog** and **Glue Crawler** to discover and manage metadata for data stored in S3.
- **Basic ETL:** Learned the ETL process and practiced creating a **Glue ETL Job** for data processing.
- **Integration:** Successfully delivered data from the source (Firehose) to the destination (S3) and processed raw data.
- **Big Data Concepts:** Distinguished between AWS services suitable for Batch processing (Glue) and Streaming processing (Kinesis).
