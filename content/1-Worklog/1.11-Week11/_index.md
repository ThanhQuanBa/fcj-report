---
title: "Week 11 Worklog"
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objectives:

- Understand the role of **AWS WAF** (Web Application Firewall) and **AWS Shield** in protecting applications from Layer 7 attacks (WAF) and DDoS attacks (Shield).
- Gain proficiency in the operation and benefits of **Amazon GuardDuty** for intelligent threat detection.
- Review and apply advanced security principles such as **IAM Policy Best Practices** and **Secrets Management**.
- Practice configuring basic security rules.

### Tasks to Be Completed This Week:

| Day | Tasks                                                                                                                                                                                                                                                                                        | Start Date | End Date   | Reference Material                        |
| :-- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------- | :--------- | :---------------------------------------- |
| 2   | - Read and understand **AWS WAF** architecture and **Web ACL** (Access Control List). <br> - Learn about common Layer 7 attacks (SQL Injection, XSS) that WAF protects against. <br> - **Hands-on:** Create a Web ACL and explore Managed Rule Groups.                                       | 17/11/2025 | 17/11/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Learn about **AWS Shield Standard/Advanced** (DDoS Protection) and how it integrates with WAF/Route 53. <br> - **Hands-on WAF:** <br>&emsp; + Create a Rate-based Rule (limit request rate). <br>&emsp; + Test blocking specific IPs using an IP Set.                                      | 18/11/2025 | 18/11/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Learn about **Amazon GuardDuty** (ML-based threat detection). <br> - **Hands-on:** Enable GuardDuty and review sample Findings (simulated threats) to understand how alerts are generated.                                                                                                 | 19/11/2025 | 19/11/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Review and apply **IAM Policy Best Practices** (Principle of Least Privilege). <br> - Learn about **AWS Secrets Manager** (automatic credential rotation) and **AWS Systems Manager Parameter Store** (configuration storage). <br> - **Hands-on:** Review IAM Policies created in Week 2. | 20/11/2025 | 20/11/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Summary & Resource Cleanup:** <br> - **Hands-on:** <br>&emsp; + Delete Web ACL/WAF Rules. <br>&emsp; + **Disable GuardDuty**. <br>&emsp; + Summarize security measures learned (Perimeter Security, Monitoring, Secrets Management).                                                     | 21/11/2025 | 21/11/2025 | <https://cloudjourney.awsstudygroup.com/> |

### Week 11 Achievements:

- **Perimeter Protection:** Understood how WAF and Shield operate at the network edge to protect resources such as CloudFront and ALB.
- **Threat Detection:** Proficient in enabling and monitoring **GuardDuty** alerts to detect suspicious activity within the AWS account.
- **Identity Management:** Reinforced knowledge of **IAM Policies** and the importance of managing secrets using dedicated services.
- **Building Security:** Learned how to integrate security services into application architecture (e.g., deploying WAF in front of an ALB).
