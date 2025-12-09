---
title: "Blog 2"
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# **How ​​to Accelerate Security Search Assessments Using Automated Business Context Validation in AWS Security Hub CSPM**

by Reetesh Surjani and Satish Kamat on SEPTEMBER 22, 2025 in **[Advanced (300)](https://aws.amazon.com/blogs/security/category/learning-levels/advanced-300/) , [AWS Security Hub](https://aws.amazon.com/blogs/security/category/security-identity-compliance/aws-security-hub/) , [Security, Identity, and Compliance](https://aws.amazon.com/blogs/security/category/security-identity-compliance/) , [Technical Guide](https://aws.amazon.com/blogs/security/category/security-identity-compliance/) , [Technical Guide](https://aws.amazon.com/blogs/security/category/security-identity-compliance/) technical](https://aws.amazon.com/blogs/security/category/post-types/technical-how-to/) [Permalink](https://aws.amazon.com/blogs/security/how-to-accelerate-security-finding-reviews-using-automated-business-context-validation-in-aws-security-hub/) [Commentary] review](https://aws.amazon.com/blogs/security/how-to-accelerate-security-finding-reviews-using-automated-business-context-validation-in-aws-security-hub/#Comments) [Share share](https://aws.amazon.com/vi/blogs/security/how-to-accelerate-security-finding-reviews-using-automated-business-context-validation-in-aws-security-hub/#)**

**October 1, 2025:** This post has been updated to reflect the new name of Security Hub, AWS Security Hub CSPM (Cloud Security Posture Management).

---

Security teams must effectively validate and document exceptions to [**AWS Security Hub (Cloud Security Posture Management, formerly known as Security Hub) findings**](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-control-manage-findings.html)**,** and maintain appropriate governance. Enterprise security teams need to ensure that exceptions to security best practices are properly validated and documented, while development teams need a streamlined process for implementing and verifying compensating controls.

In this blog post, we present an automated solution that is ideal for organizations using [**AWS Security Hub CSPM**](https://aws.amazon.com/security-hub/) that need to manage security exceptions at scale while maintaining administrative controls. This solution is especially useful for enterprises with complex compliance requirements and multiple development teams. By implementing this solution, you can accelerate the review of Security Hub CSPM findings while maintaining appropriate security governance and providing clear business context for security exceptions.

**Note:** The solution in this article is provided as a reference architecture and should not be deployed as-is in a production environment. Organizations should carefully review, customize, and enhance this solution to align with their specific security requirements, compliance frameworks, governance policies, and risk tolerances. Please work with your security, compliance, and legal teams before implementing this automated security validation solution.

## **Challenge**

Security Hub CSPM provides **[a comprehensive view of your AWS security posture across AWS accounts](https://docs.aws.amazon.com/securityhub/latest/userguide/dashboard.html) .** However, in real-world scenarios, you will encounter valid business reasons that lead to exceptions to security best practices. For example:

- Not enabling Amazon GuardDuty: Because of an alternative monitoring solution, an organization postponed implementing [Amazon GuardDuty](https://aws.amazon.com/guardduty/) but required compensating controls such as **[Amazon Virtual Private Cloud (VPC) flow logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html), [Amazon CloudWatch alerts](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)**, and an organization-specific incident response process.

- Do not enable public access to Amazon S3: Marketing teams may need a public **[Amazon Simple Storage Service (Amazon S3)](https://aws.amazon.com/s3/)** bucket for website assets, but should implement the following offsetting controls:

- [**Amazon CloudFront Distribution**](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-working-with.html) **before [Amazon S3](https://aws.amazon.com/s3/)**

- Server-side encryption with **[AWS KMS (SSE-KMS) keys enabled](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingKMSEncryption.html)** on the S3 bucket

- Enable **[Amazon bucket logging S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/enable-server-access-logging.html)**

- Enable [**Amazon S3 bucket versioning**](https://docs.aws.amazon.com/AmazonS3/latest/userguide/manage-versioning-examples.html)

- [**Amazon CloudWatch alarms**](https://aws.amazon.com/cloudwatch) for suspicious access patterns and comprehensive access logging

Managing exceptions to security best practices can be difficult and often involves multiple steps. Security teamsIt takes a lot of time to review exception requests, identify and validate compensating controls, and then developers must implement and validate those controls. Multiple teams must be mobilized to create and manage documentation for compliance and audit purposes. In general, if done manually, this process is time-consuming, error-prone (with the risk of missing implementation issues), and has the risk of poor visibility due to limited or missing documentation of the business context of security findings.

## **Solution Prerequisites**

To resolve this issue, you must have the following:

- **AWS account with appropriate service quotas for [Amazon DynamoDB](https://aws.amazon.com/dynamodb), [AWS Lambda](https://aws.amazon.com/lambda), and [Amazon Simple Queue Service (Amazon SQS)](https://aws.amazon.com/sqs)**

- **[AWS Identity and Access Management (IAM)](https://aws.amazon.com/iam) permissions required to deploy various AWS resources including:**

- **IAM create-role and IAM put-role-policy permissions (to create security group roles and developer roles)**

- [**AWS Stack Management CloudFormation**](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/aws-resource-cloudformation-stack.html)

- **Create and manage [DynamoDB tables](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithTables.html)**

- [**Amazon SQS**](https://aws.amazon.com/sqs/)

- [**Mapping AWS Lambda event sources with Amazon SQS**](https://docs.aws.amazon.com/lambda/latest/dg/with-sqs.html)

- [**Amazon Policy SQS**](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-using-identity-based-policies.html)

- **Deploy and configure [Lambda functions](https://aws.amazon.com/lambda/)**

- [**Lambda execution roles**](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html)

- **Configure [Amazon EventBridge rules](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rules.html)**

- [**Amazon S3 bucket operations**](https://aws.amazon.com/s3/) **for deployment artifacts**

- [**AWS Command Line Interface (AWS CLI)**](https://aws.amazon.com/cli/) **version 2.17.44 or later**

- [**Python**](https://www.python.org/downloads/release/python-3120/) **version 3.12 or later**

- [**JSON jq Processing Utility**](https://jqlang.org/download/) **for scripting operations**

- [**Security Hub CSPM**](https://aws.amazon.com/security-hub/) **enabled in your target AWS Region**

**aws securityhub enable-security-hub**

- [**AWS Config**](https://aws.amazon.com/config/) **recommended for enhanced authentication**

**aws configservice put\-configuration\-recorder \\ \--configuration\-recorder name\=default,roleARN\=arn:aws:iam::ACCOUNT_ID:role/aws\-service\-role/config.amazonaws.com/AWSServiceRoleForConfig**

### **Automatic Validation**

The solution includes a pre-deployment validation script **( [validate-environment.sh](https://github.com/aws-samples/sample-automated-securityhub-validator/blob/main/scripts/validate-environment.sh) )** that automatically verifies the following:

- Tool version and installation

- AWS service activation status

- Resource conflicts

This validation runs automatically during deployment (Integrated in the [deploy.sh](https://github.com/aws-samples/sample-automated-securityhub-validator/blob/main/scripts/deploy.sh) ) to help ensure that required prerequisites are met before starting to create the infrastructure.

### **Additional Resources**

See the **[Cost Estimation Guide](https://github.com/aws-samples/sample-automated-securityhub-validator/blob/main/docs/guides/cost-estimation.md)** for a detailed pricing table for prerequisites and the [**Troubleshooting Guide**](https://github.com/aws-samples/sample-automated-securityhub-validator/blob/main/docs/guides/troubleshooting.md) for common setup issues and solutions.

## **Solution Overview**

This solution provides sample code and CloudFormation templates that organizations can deploy to automatically validate compensating controls for masked Security Hub CSPM findings while maintaining appropriate segregation of duties between security and development teams.

### **Architecture**

**![Figure 1: Solution Architecture Diagram]![alt text](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-1-23.png)**

**_Figure 1: Solution Architecture Diagram_**

Figure 1 illustrates the solution process initiated when a developer changes the process status of a Security Hub CSPM result to **SUPPRESSED**, requesting a business-appropriate security exception. The process ends with the solution adding the validation result as a note to the corresponding Security Hub CSPM result, and maintaining a complete audit log of the exception request and validation result.

**Note _:_** Before initiating this workflow, developers must consult with the security team within the organization to explain the business rationale for this exception. DuringDuring this initial consultation, the security team determines the required compensating controls for the detection type. The security team uses the **add-controls-role-based.sh** script to add controls to DynamoDB. The developer activates the required compensating controls before changing the workflow state.

The workflow shown in Figure 1 consists of the following steps:

1. The developer changes the Security Hub CSPM search state to **SUPPRESSED.**

2. EventBridge detects the change to **SUPPRESSED.**

3. The EventBridge rule sends the event to an Amazon SQS queue.

4. The Lambda function retrieves messages from the Amazon SQS queue.

5. The Lambda function retrieves the compensating controls from the DynamoDB compensating dashboard.

6. The Lambda function validates each control using the appropriate AWS service API.

7. Evidence is collected for each validation and stored in DynamoDB.

8. The detection validation result and timestamp are stored in the DynamoDB table **Findings.**

9. The version history of validation attempts is stored in the **History** table DynamoDB.

10. If the controls provided by the security team pass validation, the search result remains **SUPPRESSED,** and a note is added to the corresponding Security Hub search result with the adjusted severity information (the original severity assigned by Security Hub is not changed by this solution). If one of these controls fails **validation, the search status will be changed to NOTIFIED, and a note will be added to the Security Hub search results for the failed controls (the initial severity level assigned by the Security Hub CSPM is not changed by this solution).**

11. OPTIONAL: Extend the solution with [**Amazon OpenSearch**](https://aws.amazon.com/opensearch-service/) for SOC teams to perform advanced searching, correlation, and visualization of validation evidence on findings, as well as historical trend analysis of compensating control effectiveness. Use **[Amazon QuickSight](https://aws.amazon.com/quicksight/)** to visualize compliance metrics and **[AWS Security Lake](http://aws.amazon.com/security-lake/)** to centralize authentication data across multiple accounts and regions, normalize data in the OCSF format for comprehensive cross-account analysis, and long-term compliance reporting.

**Note:** This solution must be deployed in accordance with your organization's security policy and the [**AWS Shared Responsibility Model**](https://aws.amazon.com/compliance/shared-responsibility-model/)**.** Please review and test security controls before deploying in production.

### **How ​​it works**

This solution is designed specifically for deployment and management by an organization's security teams. Only security groups have permission to deploy **[AWS CloudFormation](https://aws.amazon.com/cloudformation) stacks,** modify Lambda authentication code, add/modify offset controls, or access the four DynamoDB tables (Controls, Detections, History, Evidence).

Developers are limited to two specific actions: suppress Security Hub CSPM detections and read offset control requests. This strict separation of roles facilitates proper governance and helps prevent bypassing security authentication logic. Organizations must implement appropriate IAM policies to enforce these access restrictions in production environments.

The solution works as follows:

1. Security groups define controls: Security groups set up offset controls for specific Security Hub detection types and store them in a DynamoDB table. This helps ensure approved exceptions adhere to approved security guidelines and maintain compliance standards.

- Important files for security teams:

| Documentation                         | Purpose                                     |
| :------------------------------------ | :------------------------------------------ |
| add-controls-role-based.sh            | Utility script to add compensating controls |
| /templates/findings/\*.json           | Compensating control example for reference  |
| /docs/guides/compensating-controls.md | Guide to defining controls                  |

- Supported authentication types: The solution supports 13 authentication methods to meet diverse security requirements:

| Authentication type      | Description                                       | Usage example                                                                                                                                   |
| ------------------------ | ------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| **CONFIG_RULE**          | Authenticate using **AWS Config Rules**           | For **GuardDuty not enabled**, find: `vpc-flow-logs-enabled` — rule to ensure network traffic is monitored                                      |
| **API_CALL**             | Authenticate using direct **AWS API calls**       | Check public access of **Amazon S3**: API call verifies **CloudFront distribution** exists before **S3 bucket**                                 |
| **SECURITY_HUB_CONTROL** | Authenticate using **Security Hub Control** state | For **GuardDuty not enabled**, find: `CloudTrail.1` — ensure comprehensive API logging                                                          |
| **CLOUDWATCH**           | Authenticate using **CloudWatch alarms**          | For **GuardDuty not enabled**, create **alarm** to monitor suspicious API calls and unusual network traffic                                     |
| **CLOUDTRAIL**           | Validate **AWS CloudTrail** configuration         | With **GuardDuty not enabled**, ensure **multi-region CloudTrail** has log validation and **CloudWatch** integration                            |
| **SYSTEMS_MANAGER**      | Validate with **AWS Systems Manager Parameters**  | With **GuardDuty not enabled**, check the **custom threat detection solution** assertion parameter is enabled                                   |
| **PROCESS_CONTROL**      | Validate **processes**-based controls             | With **GuardDuty not enabled**, verify the **incident response process** is logged for network security events                                  |
| **INSPECTOR**            | Validate **Amazon Inspector** configuration       | Vulnerability check: **Inspector EC2 scans** are enabled and no critical issues are detected                                                    |
| **ACCESS_ANALYZER**      | Validate **AWS IAM Access Analyzer**              | Ensure **Access Analyzer** is enabled and no **active findings** are allowed                                                                    |
| **MACIE**                | Validate **Amazon Macie** configuration           | Ensure **Macie** is enabled, detects sensitive data, and no sensitive data groups are left out                                                  |
| **AUDIT_MANAGER**        | Validate **AWS Audit Manager frameworks**         | Ensure **custom security framework** is active and includes all **mandatory controls**                                                          |
| **EVENTBRIDGE**          | Validate **Amazon EventBridge** rules             | With **GuardDuty not enabled**, validate **EventBridge rule** monitors **AWS CloudTrail** events with **Lambda targets** for automated response |
| **TRUSTED_ADVISOR**      | Validate **AWS Trusted Advisor** checks           | Check **S3 bucket permissions** — must pass without **warnings** or **errors**                                                                  |

Note: Only security group members have permission to add or modify offset controls. The solution enforces this permission through IAM permissions and runtime checks to maintain proper governance.
Approved security exceptions must have an expiration date to facilitate periodic review. The solution automatically applies these time limits based on the expiration date defined by the security team.
In this article, we provide a utility script ( [add-controls-role-based.sh](https://github.com/aws-samples/sample-automated-securityhub-validator/blob/main/scripts/add-controls-role-based.sh) ) to demonstrate adding offset controls. However, in production enterprise environments, organizations should integrate DynamoDB with existing governance systems (such as Jira, ServiceNow, etc.) to automatically populate controls from sources authorized by the security team. This solution focuses on validating controls, not prescribing how they are received.

2\. Developers deploy controls: When a Security Hub CSPM is detected as suppressed, the developer must deploy the required compensating controls defined by the security team.

How developers interact with the solution:

1. View required controls: The solution provides clear requirements for each search type.

2. Deploy compensating controls: Developers should deploy the compensating controls provided by the security team in their AWS environment, referencing the compensating controls defined by the security team. The specific compensating controls depend on the type of detection and the security team's requirements.

3. Look for status changes: The developer changes the Security Hub CSPM search status to **SUPPRESSED** in Security Hub.

4. Automated Validation: Solution to validate compensation controls when CSPM Security workflow statusHub changes.

5. Status update: The result remains **SUPPRESSED** if the control passes validation; the result changes to **NOTIFIED** with error details if validation fails.

Note: This solution does not modify the original severity of the findings in Security Hub CSPM. It adds business context with security-approved severity to the findings based on security-approved compensating control validation, helping security teams make informed decisions.

With this solution, we simulate the developer workflow of handling CSPM findings on Security Hub by deploying and validating compensating controls. In production, developers will receive notifications of findings that require attention, deploy the necessary controls as directed by the security team, and use this validation system to verify the deployment. The solution focuses on the validation aspect but assumes that organizations will integrate it with existing developer workflows, ticket systems, and continuous integration and delivery (CI/CD) processes to create a seamless process from bug discovery to remediation verification.
**Evidence Collection and Audit Tracking**
The solution automatically collects comprehensive evidence for each validation activity. Key features of the solution include:

1. Four-table design: Separate tables for Controls, Findings, History, and Evidence (shown in Figure 2\) provide security through separation while maintaining a full audit trail

**![Figure 2: Four-table design to store offsetting controls, evidence, findings, and history]![alt text](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-2-20.png)**
**_Figure 2: Four-table design to store offsetting controls, evidence, findings, and history_**

1. Detailed evidence: Each validation stores specific evidence based on its type—from AWS Config rule compliance details to API responses and process document verification

2. Immutable records: Each evidence includes a timestamp, validation context, and results that cannot be modified after collection (shown in Figure 3)![]![alt text](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-3-17.png)
   **\*Figure 3: Sample collected evidence to validate CONFIG_RULE shows PASSED status\*\*\***History Tracking: The solution maintains a full history of each validation, allowing organizations to demonstrate ongoing compliance over time\*\*

3. History Tracking: The solution maintains a full history of each validation, allowing organizations to demonstrate ongoing compliance over time

**Deployment and Configuration**
You can deploy the solution using the provided scripts.

1. Use the following command to clone the repository:

**git clone [https://github.com/aws-samples/sample-automated-securityhub-validator.git](https://github.com/aws-samples/sample-automated-securityhub-validator.git)**

**cd automated-securityhub-validator**

2. Use the following command to check service quotas and create security groups and developer roles:

**cd scripts**

**./create-roles-quotas-check.sh**

3. Use the following command to assume the security group roles:

**aws sts assume\-role \--role\-arn arn:aws:iam:: ACCOUNT_ID:role/securityhub\-validator\-SecurityTeamRole \--role\-session\-name SecurityTeamSession**

In the output of the previous command, Note the AccessKeyId, SecretAccessKey, and SessionToken markers. The timestamp in the Expires field is in UTC and shows when the IAM role's temporary credentials expire. After the temporary credentials expire, the user must resume assuming the role.

Note: For temporary credentials, you can use the DurationSeconds parameter to increase the maximum session duration for IAM roles.

4. Create environment variables to assume the security group role and verify the user has assumed the IAM role:

- Run the following commands to set the environment variables to assume the IAM role:

**export AWS_ACCESS_KEY_ID\=RoleAccessKeyID**

**export AWS_SECRET_ACCESS_KEY\=RoleSecretKey**

**export AWS_SESSION_TOKEN\=RoleSessionToken**

Note: Replace the example values ​​with the values ​​you noted when assuming the IAM role. For Windows (OS), replace export with set.

- Run the get-caller-identitycommand to verify that the user has assumed the IAM role:

**aws sts get-caller-identity**
**Note:** In the output of the previous command, confirm that the ARN is **arn:aws:sts::ACCOUNT_ID:assumed-role/securityhub-validator-SecurityTeamRole/SecurityTeamSession** instead of **arn:aws:iam::ACCOUNT_ID:user/username.**

5. Use the following command to deploy the solution:

**cd scripts**

**./deploy.sh**

6. You can verify that the stack has been created by going to the AWS Management Console for CloudFormation and following these steps:

7. In the CloudFormation console, select Stacks and then select Stack details in the navigation pane.

8. Locate and select the securityhub-validator stack to open its details page.tack that.

9. On the stack details page, select the Resources tab.

10. Under Resources, you'll see a list of resources that are part of the stack.

**![Image 4: Resources created using a CloudFormation stack]![alt text](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-4-14.png)**
**_Image 4: Resources created using a CloudFormation stack_**
**The deployment script creates a CloudFormation stack with the required resources:**

- DynamoDB table for controls, detections, history, and evidence

- A Lambda function to authenticate and update Security Hub

- EventBridge rule to record search state changes

- Amazon SQS queue and dead letter queue (DLQ) to process messages

- Least privilege IAM role

7\. Add compensating controls (security group):

**cd scripts**

**./add-controls-role-based.sh**

8\. Implement controls (developer).

The developer will now assume the developer role and deploy the required controls based on the security group specifications. The solution will automatically validate these deployments when the **SUPPRESSED** developer changes the Security Hub CSPM workflow status.

For an example of how to implement common controls, see **[example of compensating controls for GuardDuty.1 detection](https://github.com/aws-samples/sample-automated-securityhub-validator/blob/main/templates/findings/example-guardduty-1.json).**

**Test the solution**

To test the solution, you can validate the compensating controls for GuardDuty detection using the following example scenario:
A developer wants a security exception for Security Hub CSPM when GuardDuty.1 is found: [**GuardDuty must be enabled**](https://docs.aws.amazon.com/securityhub/latest/userguide/guardduty-controls.html#guardduty-1) and due to cost constraints, the developer's organization has not deployed GuardDuty and has asked their organization's security team to issue a security exception.
The compensating controls provided by the security group include:

- [**Amazon Virtual Private Cloud (Amazon VPC) Flow Logs**](https://aws.amazon.com/vpc) must be enabled for network monitoring

- CloudWatch alarms triggered to monitor for suspicious activity

**Note:** To simulate this finding, do not enable GuardDuty so that the _GuardDuty enabled_ finding appears in the Security Hub console.

Approximately 20–30 minutes after enabling AWS Config and Security Hub CSPM, you can locate this finding in the console by performing the following steps, then adding the compensating controls provided by the security group.

For this use case, we are using _GuardDuty to enable_ Security Hub CSPM:

1. Navigate to the AWS Security Hub CSPM console and select Findings in the navigation pane.

2. In the Add Filter search bar at the top, select the Severity label and set the value to HIGH .

3. After applying the filter, select GuardDuty to enable in the Search column to view its details in the right pane.

4. Select Actions in the upper right corner and select View JSON .![]![alt text](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-5-13.png)

**_Figure 5: Security Hub CSPM Detection_**

5. In the JSON details window, find the SecurityControlId field and note the value. You will be prompted to enter this value **add-controls-role-based.sh utility in the next step.**

**Note: SecurityControlIdThis value is required for the add-controls-role-based.sh utility to correctly associate your offset control with the Security Hub CSPM detection.**

**![Image 6: SecurityControlId from GuardDuty detection]![alt text](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-6-9.png)**

**_Image 6: SecurityControlId from GuardDuty detection_**

1. Use the following command to clone the repository:

**git clone [https://github.com/aws-samples/sample-automated-securityhub-validator.git](https://github.com/aws-samples/sample-automated-securityhub-validator.git)**

**cd sample-automated-securityhub-validator**

7. For this demo, you will be a member of the security group by assuming the security group role and using the add-controls-role-based.sh utility to create offset controls and push them to the DynamoDB offset controls table.

**cd sample-automated-securityhub-validator/scripts**

**./add-controls-role-based.sh**

8. Use the following prompt values ​​add-controls-role-based.sh to create compensating control table entries using the four compensating controls provided by the security team to GuardDuty.1search type:

**./add\-controls\-role\-based.sh**

**Security Team \- Compensating Controls Management Utility**

**\-----------------------------------------------------------**

**SECURITY NOTICE: This utility is restricted to security team members only**

**Validating security team role...**

**✓ Security team role validated: arn:aws:sts::xxxxxxxxxxx:assumed\-role/securityhub\-validator\-SecurityTeamRole/SecurityTeamSession**

**Using AWS Region: us\-east\-1**

**Using stack: securityhub\-validator**

**Using controls table: securityhub\-validator\-ControlsTable\-ARDQCU67CBCN**

**Enter finding type (e.g., GuardDuty.1): GuardDuty.1**

**Security approved adjusted risk level \[CRITICAL/HIGH/MEDIUM/LOW/INFORMATIONAL\]: MEDIUM**

**Expiration date (YYYY\-MM\-DD): 2026\-12\-31**

**Ticket reference: JIRA\-SEC\-1234**

**Business justification: Alternative monitoring solution provides equivalent detection capabilities**

**Adding Control \#1**

**Control ID: VPC\-FLOW\-LOGS**

**Control description: VPC Flow logs must be enabled for network monitoring**

**Validation type \[CONFIG_RULE/API_CALL/SECURITY_HUB_CONTROL/INSPECTOR/ACCESS_ANALYZER/CLOUDTRAIL/MACIE/AUDIT_MANAGER/CLOUDWATCH/SYSTEMS_MANAGER/EVENTBRIDGE/TRUSTED_ADVISOR/PROCESS_CONTROL\]: CONFIG_RULE**

**Config rule name (exact name): vpc\-flow\-logs\-enabled**

**Description of how this rule mitigates the finding: Provides comprehensive network traffic visibility similar to GuardDuty's network monitoring capabilities**

**Add another control? \[y/n\]: y**

**Adding Control \#2**

**Control ID: SECURITY\-ALARMS**

**Control description: CloudWatch alarms for suspicious activity**

**Validation type \[CONFIG_RULE/API_CALL/SECURITY_HUB_CONTROL/INSPECTOR/ACCESS_ANALYZER/CLOUDTRAIL/MACIE/AUDIT_MANAGER/CLOUDWATCH/SYSTEMS_MANAGER/EVENTBRIDGE/TRUSTED_ADVISOR/PROCESS_CONTROL\]: CLOUDWATCH**

**Alarm name pattern: SecurityMonitoring\-**

**Required metrics (comma\-separated): UnauthorizedAPICalls,NetworkPortProbing**

**Required alarm state \[ALARM/OK/INSUFFICIENT_DATA/ANY\]: ANY**

**Minimum number of matching alarms required: 2**

**Description of how these alarms mitigate the finding: Alarms detect suspicious API calls and network activity similar to GuardDuty's threat detection**

**Add another control? \[y/n\]: n**

**Generated controls:**

**{**

**"findingType": {**

**"S": "GuardDuty.1"**

**},**

**"securityApprovedAdjustedRiskLevel": {**

**"S": "MEDIUM"**

**},**

**"expirationDate": {**

**"S": "2026-12-31T00:00:00Z"**

**},**

**"ticketReference": {**

**"S": "JIRA-SEC-1234"**

**},**

**"businessJustification": {**

**"S": "Alternative monitoring solution provides equivalent detection capabilities"**

**},**

**"auditInfo": {**

**"S": "{\\"createdBy\":\"arn:aws:sts::xxxxxxxxxxx:assumed-role/securityhub-validator-SecurityTeamRole/SecurityTeamSession\",\"createdAt\":\"2025-08-05T08:49:51Z\",\"la stModifiedBy\":\"arn:aws:sts::xxxxxxxxxxx:assumed-role/securityhub-validator-SecurityTeamRole/SecurityTeamSession\",\"lastModifiedAt\":\\"2025-08-05T08:49:51Z\\"}"**

**},**

**"securityControlHash": {**

**"S": "a0b33a0a96a6b282bad1c093586d89cef832d40bb379abd4a004d00afdf603d1"**

**},**

**"requiredControls": {**

**"S": "\[{\\"controlId\":\"VPC-FLOW-LOGS\",\"description\":\"VPC Flow logs must be enabled for network monitoring\",\"validationType\":\"CONFIG_RULE\",\"validationParams\":{\\"ruleName\":\"vpc-flow-logs-enabled\",\"justification\":\"Provides comprehensive network traffic visibility similar to GuardDuty's network monitoring capabilities\"}},{\\"controlId\":\"SECURITY-ALARMS\",\"description\":\"CloudWatch alarms for suspicious activity\",\"validationType\":\"CLOUDWATCH\",\"validationParams\":{\\"alarmNamePattern\":\"SecurityMonitoring-\",\"requiredMetrics \\":\[\"UnauthorizedAPICalls\",\"NetworkPortProbing\"\],\"requiredState\":\"ANY\",\"minimumAlarms\":2,\"justification\":\"Alarms detect suspicious API calls and network activity similar to GuardDuty's threat detection\"}}\]"**

**}**

**}**

**Save to DynamoDB? \[y/n\]: y**

**Compensating controls saved to DynamoDB\!**

**This action has been logged for audit purposes.**

9. When prompted to save to DynamoDB, enter Y. The compensating controls will be added to the DynamoDB compensation dashboard.

**![Image 7: Compensating Control for GuardDuty Detection.1]![alt text](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-7-8.png)**
**_Image 7: Compensating Control for GuardDuty Detection.1_**

1.  For this proof-of-concept demo, deploying compensating controls requires additional AWS permissions beyond what the developer role provides. In production environments, these controls are typically deployed by infrastructure teams or through automated deployment processes.

- Move to admin info.

To illustrate, let's temporarily switch back to your AWS administrative credentials (the ones used to create the role):

Uninstall security group role credentials
**unset AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY AWS_SESSION_TOKEN**

- Implement necessary controls

Control 1: Enable VPC Flow Logs, starting by getting your VPC ID **VPC_ID=$(aws ec2 describe-vpcs \--query 'Vpcs\[0\].VpcId' \--output text)**
Create flow logs:

**aws ec2 create\-flow\-logs \\**

**\--resource\-type VPC \\**

**\--resource\-ids $VPC_ID \\**

**\--traffic\-type ALL \\**

**\--log\-destination\-type cloud\-watch\-logs \\**

**\--log\-group\-name VPCFlowLogs**Create an AWS Config Rule:

**aws configservice put\-config\-rule \\**

**\--config\-rule '{**

**"ConfigRuleName": "vpc-flow-logs-enabled",**

**"Source": {**

**"Owner": "AWS",**

**"SourceIdentifier": "VPC_FLOW_LOGS_ENABLED"**

**}**

**}'**

Control 2: Creating security monitoring alerts starts with creating a metric filter for CloudTrail Logs; Start by creating a log group for CloudTrail (if none exists): **aws logs create-log-group \--log-group-name CloudTrail/SecurityEvents** Create a metric filter for unauthorized API calls:

**aws logs put\-metric\-filter \\**

**\--log\-group\-name CloudTrail/SecurityEvents \\**

**\--filter\-name UnauthorizedAPICallsFilter \\**

**\--filter\-pattern '{ ($.errorCode \= "\*UnauthorizedOperation") || ($.errorCode \= "AccessDenied\*") }' \\**

**\--metric\-transformations metricName\=UnauthorizedAPICalls,metricNamespace\=SecurityMetrics,metricValue\=1**

Create a filter to probe network ports:

**aws logs put-metric-filter \\**

**\--log-group-name CloudTrail/SecurityEvents \\**

**\--filter-name NetworkPortProbingFilter \\**

**\--filter-pattern '\[version, account, eni, source, destination, srcport, destport="22" || destport="3389" || destport="1433", protocol, packets, bytes, windowstart, windowend, action="REJECT", flowlogstatus\]' \\**

**\--metric-transformations metricName=NetworkPortProbing,metricNamespace\=SecurityMetrics,metricValue\=1**

Create required CloudWatch alerts, starting with Alert 1 for unauthorized API calls:

**aws cloudwatch put\-metric\-alarm \\**

**\--alarm\-name "SecurityMonitoring-UnauthorizedAPICalls" \\**

**\--alarm\-description "Detects unauthorized API calls" \\**

**\--metric\-name "UnauthorizedAPICalls" \\**

**\--namespace "SecurityMetrics" \\**

**\--statistic Sum \\**

**\--period 300 \\**

**\--threshold 1 \\**

**\--comparison\-operator GreaterThanOrEqualToThreshold \\**

**\--evaluation\-periods 1**

Alarm 2: Network port probe:

**aws cloudwatch put\-metric\-alarm \\**

**\--alarm\-name "SecurityMonitoring-NetworkPortProbing" \\**

**\--alarm\-description "Detects network port probing activity" \\**

**\--metric\-name "NetworkPortProbing" \\**

**\--namespace "SecurityMetrics" \\**

**\--statistic Sum \\**

**\--period 300 \\**

**\--threshold 5 \\**

**\--comparison\-operator GreaterThanOrEqualToThreshold \\**

**\--evaluation\-periods 1**

11. Now assume a DeveloperRole to prevent detection:

**aws sts assume\-role \\**

**\--role\-arn arn:aws:iam::ACCOUNT_ID:role/securityhub\-validator\-DeveloperRole \\**

**\--role\-session\-name DeveloperSession**

**Configure the credentials returned:**

**export AWS_ACCESS_KEY_ID\=\<from assume\-role output\>**

**export AWS_SECRET_ACCESS_KEY\=\<from assume\-role output\>**

**export AWS_SESSION_TOKEN\=\<from assume\-role output\>**

12. Change the GuardDuty-related Security Hub workflow status from NEW to SUPPRESSED.

To change workflow status using AWS CLI (developer):

**\# Get the finding ARN first (command shown for reference)**

**aws securityhub get-findings \\**

**\--filters '{"GeneratorId":\[{"Value":"security-control/GuardDuty.1","Comparison":"EQUALS"}\]}' \\**

**\--query 'Findings\[0\].Id'**

**\# Get the product ARN (command shown for reference)**

**aws securityhub get-findings \\**

**\--filters '{"GeneratorId":\[{"Value":"security-control/GuardDuty.1","Comparison":"EQUALS"}\]}' \\**

**\--query 'Findings\[0\].ProductArn' \\**

**\--output text**

**\# Then suppress the finding**

**aws securityhub batch-update-findings \\**

**\--finding-identifiers '\[{"Id":"finding-arn-from-above","ProductArn":"product-arn-from-above"}\]' \\**

**\--workflow '{"Status":"SUPPRESSED"}' \\**

**\--note '{"Text":"Implemented compensating controls as per security team requirements","UpdatedBy":"developer@example.com"}'**

To change the workflow status using the (developer) console:

1. Access the Security Hub CSPM console.

2. In the navigation pane, select Detections.

3. In the search bar, select the Compliance Security Control ID filter and enter the Is value as **GuardDuty.1.**

4. Select the GuardDuty item to be enabled and in Workflow Status, select DISABLED.

5. In the Notes field, enter **Implemented compensating controls as per security team requirements.**

6. Select Set Status to save the note.

**![Image 8: GuardDuty.1 search workflow status changed from NEW to PREVENTED]![alt text](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-8-8.png)**

**_Image 8: GuardDuty.1 search workflow status changed from NEW to PREVENTED_**
**Note:** Only delete findings after implementing the required compensating controls provided by the security team.

13\. After the workflow status of the discoveryn is **SUPPRESSED,** the automatic validation process will start and you can view Lambda function logs in the CloudWatch console related to the different validations performed.

To view Lambda function logs in the CloudWatch console:

1. Go to the Amazon CloudWatch console.

2. In the navigation pane, under Logs, select Log Groups.

3. Select the log group with the Lambda function name.

4. Select the most recent log stream to view the logs.

**![Image 9: Lambda Function CloudWatch Logs]![alt text](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-9-7.png)**
**_Image 9: Lambda Function CloudWatch Logs_**

The solution updates the findings notes in Security Hub CSPM with the validation results:

If all controls pass:

- Still looking for **SUPPRESSED.**

- A note is added with the validation results and adjusted risk level.

- Business context is added to the search results.

If one of the controls fails:

- Search for a status change for **NOTIFIED.**

- A note is added with details about the failed controls.

- The security team will review these changes as part of their standard process.

**To view the workflow status of the discovery and updated notes using the (developer) console:**

1. Go to the Security Hub CSPM console.

2. In the navigation pane, select Findings.

3. In the search bar, select the Compliance Security Control ID filter and enter the value Is as **GuardDuty.1.**

4. Select the GuardDuty item that needs to be enabled and check the Workflow status.

5. For Action, select Add Note.

6. Check the Last Note Added.

**![Image 10: Security Hub CSPM Updated Search Notes]![alt text](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-10-4.png)**
**_Image 10: Security Hub CSPM Updated Search Notes_**

The discovery notes show that automated validation performed the test and recorded the results, and note that the original severity level **HIGH** assigned by Security Hub CSPM is maintained, and the adjusted severity level **MEDIUM** provided by the security team is added to the Notes section and Evidence panel, providing transparency and accountability while maintaining the original severity level assigned by Security Hub CSPM**.**
**Cleanup**
To avoid ongoing costs, use the following command to clean up resources created for this post.
**./cleanup.sh**
This deployment process is designed to be simple and maintains security best practices such as encryption, least privilege, and segregation of duties.

**Conclusion**
In this article, we showed you how to implement a solution that security teams can use to define compensating controls for AWS Security Hub CSPM findings and automatically validate their implementation. We analyzed the challenges of managing security exceptions and demonstrated how the solution helps bridge the gap between security requirements and actual implementation.
The solution provides a structured workflow where security teams define appropriate compensating controls, developers implement them, and an automated system validates their effectiveness. With support for 13 different types of validation, from AWS Config rules to process documentation, the solution provides comprehensive security coverage for a variety of security scenarios.
We also demonstrated a comprehensive process for adding compensating controls to GuardDuty detections and showed that the solution maintains the severity of the original detection assigned by the Security Hub CSPM, while recording an adjusted risk level approved by the security team. This approach helps maintain transparency and auditability, while allowing for necessary exceptions.
Try it out and share your feedback in the comments.
Security Disclaimer: The Amazon S3 configurations presented in this article involve publicly accessible setups that expose data to the internet and should only be used for demonstration purposes or non-sensitive content. Public S3 buckets pose significant risks, including data leaks, unexpected costs from unauthorized use, regulatory violations, and potential security vulnerabilities. For production environments, use IAM roles, implement a least privilege access policy, enable S3 Block Public Access settings, and consider using CloudFront with Origin Access Control to deliver public content. Please consult your security team and ensure compliance with organizational policies before deploying public S3 configurations in production.

---

<div style="display: flex; gap: 20px; padding: 20px; border: 1px solid #e5e7eb; border-radius: 6px; margin-bottom: 24px;">
    <img src="https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/Reetesh-Surjani-author.jpg" 
         width="130" 
         style="border-radius: 4px;">
    <div>
        <h3>Reetesh Surjani</h3>
        <p>
            Reetesh is a Delivery Consultant in Security Risk & Compliance at AWS Professional Services,
            based in Pune, India. He works closely with customers across diverse verticals to help 
            strengthen their security infrastructure and achieve their security goals.
        </p>
    </div>
</div>

<div style="display: flex; gap: 20px; padding: 20px; border: 1px solid #e5e7eb; border-radius: 6px; margin-bottom: 24px;">
    <img src="https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/Satish-Kamat-author.jpg" 
         width="130" 
         style="border-radius: 4px;">
    <div>
        <h3>Satish Kamat</h3>
        <p>
            Satish is a Senior Delivery Consultant in Application Development at AWS Professional Services,
            based in Pune, India. He works closely with customers in their cloud transformation and migration 
            journeys across various verticals like BFSI, automotive, and telecom.
        </p>
    </div>
</div>
