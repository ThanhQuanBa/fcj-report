---
title: "Blog 1"
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Building Resilient Multi-Cluster Applications with Amazon EKS, Part 1: Implementing Cross-Cluster Load Balancing with NLB

by Krishna Sarabu, Anuj Butail, and Pushkar Patil on 22 SEPTEMBER 2025 in [Amazon Elastic Kubernetes Service](https://aws.amazon.com/blogs/networking-and-content-delivery/category/compute/amazon-kubernetes-service/) , [Compute](https://aws.amazon.com/blogs/networking-and-content-delivery/category/compute/) , [Elastic Load Balancing](https://aws.amazon.com/blogs/networking-and-content-delivery/category/networking-content-delivery/elastic-load-balancing/) , [Networking and Content Delivery](https://aws.amazon.com/blogs/networking-and-content-delivery/elastic-load-balancing/) , [Networking and Content Delivery](https://aws.amazon.com/blogs/networking-and-content-delivery/elastic-load-balancing/) link](https://aws.amazon.com/blogs/networking-and-content-delivery/category/networking-content-delivery/) [permanent](https://aws.amazon.com/blogs/networking-and-content-delivery/building-resilient-multi-cluster-applications-with-amazon-eks/) [Share](https://aws.amazon.com/vi/blogs/networking-and-content-delivery/building-resilient-multi-cluster-applications-with-amazon-eks/#)

This three-part series explores design patterns and strategies for enhancing application resiliency through multi-cluster deployments on [Amazon Elastic Kubernetes Service (EKS)](https://aws.amazon.com/eks/) . In this first part, we address a common challenge when using a [Network Load Balancer (NLB)](https://aws.amazon.com/elasticloadbalancing/network-load-balancer/) in a multi-cluster environment.

Organizations increasingly rely on Kubernetes—whether through [Amazon Elastic Kubernetes Service (EKS)](https://aws.amazon.com/eks/) or self-managed clusters on [Amazon Web Services (AWS)](https://aws.amazon.com/)—to run and scale mission-critical applications. While running workloads on a single EKS cluster offers convenience, it also poses challenges in maintaining high availability during critical operations. Activities such as cluster upgrades, add-on updates, and workflow changes can potentially impact workload resiliency and application availability, making it necessary to proactively address these issues.

To mitigate these challenges, users often deploy applications across multiple EKS clusters. This multi-cluster approach offers several key benefits:

- **Blue-Green Upgrades** : Zero-downtime upgrades via blue-green deployments, allowing traffic to move gradually between clusters.

- **Cluster Upgrades and Add-on Updates** : Clusters and add-ons are updated cluster by cluster, minimizing disruption across the entire system.

- **Workload Resiliency** : Improved workload resiliency when clusters are accidentally deleted.

- **Failover and Disaster Recovery** : Improve disaster recovery with cross-cluster failover capabilities.

While these benefits are compelling, implementing efficient load balancing across multiple clusters has historically been a challenge. However, [AWS Load Balancer Controller (LBC)](https://docs.aws.amazon.com/eks/latest/userguide/aws-load-balancer-controller.html) v2.10+ now addresses this issue by supporting cross-cluster traffic distribution via multi-cluster TargetGroupBinding, a powerful feature we will detail in this article. This is particularly useful for client-server models and scenarios where organizations self-manage their Layer 7 proxy configuration, as Network Load Balancer (NLB) provides the flexibility and performance needed for these use cases.

## **Enhanced NLB Target Group Binding: Support for Multi-Cluster EKS Deployments**

This new [feature](https://kubernetes-sigs.github.io/aws-load-balancer-controller/latest/guide/targetgroupbinding/targetgroupbinding/#multicluster-target-group) allows NLB to register targets (TargetGroupBinding) from different EKS clusters to the same NLB target group, ensuring traffic is distributed seamlessly.

### **How ​​it works**

With LBC version 2.10 and above, the new feature enables efficient target management across multiple EKS clusters. Using a ConfigMap for each cluster TargetGroupBinding allows the controller to support multi-cluster deployments seamlessly.

The new parameter, multiClusterTargetGroup, allows NLB to handle targets across multiple clusters. When enabled, this flag ensures that each cluster manages its targets independently, improving reliability and streamlining load balancing across clusters.

The following figure shows the reference architecture:

![alt text](https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2025/09/21/Arch1-1024x888.png)

Figure 1 – Architecture

The process goes as follows:

1. Target Management: For each EKS cluster, the LBC maintains a separate ConfigMap to track targets for that cluster's service endpoints. This ensures that only cluster-specific targets are registered or unregistered from the NLB, avoiding any inadvertent changes to other clusters.

2. Pod Registration: When a new pod is initialized, the LBC updates the ConfigMap in its reconciliation loop. Thec new target is registered in NLB, ensuring that traffic can be routed correctly to the new pod.

3. Pod deletion: Similarly, when a pod is deleted, LBC updates the ConfigMap to reflect the change and deregisters the deleted target from NLB, keeping the system consistent and error-free.

4. Reconciliation process: LBC regularly reconciles service endpoints with NLB targets, adding new endpoints and removing obsolete endpoints while using ConfigMap to maintain cluster separation. When there is a change, LBC updates the entire ConfigMap object as a single operation. The controller does not support partial update or patching functionality.

Now, let's implement this multi-cluster configuration.

## **Prerequisites**

For this tutorial, you need an AWS account with the appropriate [AWS Identity and Access Management (IAM)](https://aws.amazon.com/iam/) permissions to create an EKS cluster and IAM role, and to be able to launch the provided [AWS CloudFormation](https://aws.amazon.com/cloudformation/) template. For detailed pricing information, please refer to the official [AWS Pricing](https://aws.amazon.com/pricing) page.

## **Deploy the solution with CloudFormation**

We use the CloudFormation stack to deploy this solution. This stack creates all the necessary resources, such as the following:

- Network components such as [Amazon Virtual Private Cloud (Amazon VPC),](https://aws.amazon.com/vpc/) subnets and [NAT Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) .

- Two EKS clusters: Each EKS cluster has two working nodes ( [Amazon EC2)](https://aws.amazon.com/ec2/) deployed in the same VPC.

To get started, complete the following steps:

1. Log in to the [AWS Management Console](https://aws.amazon.com/console/) .

2. Select Launch Stack in any [AWS Region](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/) and open it in a new tab: ![alt text](https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2025/09/22/launchstack.png)

3. On the Create Stack page, continue with all defaults.

4. Select the checkbox to confirm IAM resource creation.

5. Select Create Stack.

6. Wait for the stack creation to complete. This step can take up to 15 minutes to complete.

At this point, you have a Primary and Secondary EKS cluster. Configure the LBC add-on on both clusters and deploy a sample application to demonstrate multi-cluster load balancing.

From the Console, launch [AWS CloudShell](https://aws.amazon.com/cloudshell/) :

![alt text](https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2025/09/21/cs.png)

Figure 2 – Launch CloudShell from the AWS Console

Using CloudShell, run the following command:

git clone https://github.com/aws-samples/sample-eksapps-ha-design-patterns.git

cd sample-eksapps-ha-design-patterns/nlb_config_aws_lbc/src

bash ./install_prereqs.sh

The script successfully deploys three nginxgroups with the NLB LoadBalancer service on the primary EKS cluster (pri-eks-clu1), while the secondary EKS cluster (sec-eks-clu1) is initialized and ready for further configuration.

We will cover two scenarios in the following sections:

1. Scenario 1 (existing workload): Use existing NLB to route traffic to nginx service running on both primary and secondary EKS clusters.

2. Scenario 2 (new workload): Create a new NLB to route traffic to nginx service running on both primary and secondary EKS clusters.

Each scenario demonstrates how to distribute traffic seamlessly across multiple clusters using a single NLB setup.

We start with Scenario 1 for existing workloads.

### **Scenario 1: Extend existing NLB configuration to support multiple clusters**

In this scenario, you work with an existing NLB and the Target Group and associated TargetGroupBinding configured on the Primary EKS cluster. You update the TargetGroupBinding on the Primary EKS cluster to enable multi-cluster support. Next, you create a new TargetGroupBinding on the Secondary EKS cluster for the nginx service. This configuration allows the existing NLB to distribute traffic between nginx services running on both the Primary and Secondary EKS clusters using the same Target Group. This approach ensures seamless traffic distribution across both clusters while maintaining the existing load balancing infrastructure.

Step 1: Verify Multi-Cluster Support in LBC

Using CloudShell, verify that both the primary and secondary EKS clusters have aws-load-balancer-controller installed with multi-cluster enabled by running the following commands:

kubectl \--context pri-eks-clu1 explain targetGroupBinding.spec.multiClusterTargetGroup

kubectl \--context sec-eks-clu1 explain targetGroupBinding.spec.multiClusterTargetGroup

Dam

A successful output will show the multiClusterTargetGroup defines field in the TargetGroupBinding spec for both clusters, confirming that the LBC's multi-cluster capabilities are properly enabled. If this field is missing, ensure you have the latest version of LBC installed.

Step 2: Enable multi-cluster support and deletion protection on NLB

Using CloudShell, run the following command to add deletion protection and enable multi-cluster target group with annotations on the primary EKS cluster:

kubectl \--context pri-eks-clu1 patch service nginx \\

\--patch '{"metadata": {"annotations": {"service.beta.kubernetes.io/aws-load-balancer-multi-cluster-target-group": "true", "service.beta.kubernetes.io/aws-load-balancer-attributes": "deletion_protection.enabled=true"}}}' \\

\--type=merge

Successful output will show service/nginx patched.

Step 3 : Verify multi-cluster configuration in TargetGroupBinding

Using CloudShell, run the following command to verify that the targetGroupBinding flag has been updated to multiClusterTargetGroup:

kubectl \--context pri-eks-clu1 get targetgroupbinding \-l service.k8s.aws/stack-name\=nginx \\

\-o jsonpath='{range .items\[\*\]}{.metadata.name}{"\\t"}{.spec.multiClusterTargetGroup}{"\\n"}{end}'

A successful output will display true.

Step 4 : Verify target registration in ConfigMap

When the TargetGroupBinding flag multiClusterTargetGroup is updated, LBC will create a ConfigMap object containing a list of targets and match the target Pods to the NLB Target.

Using CloudShell, run the following commands to verify that ConfigMap creates and registers targets:

kubectl \--context pri-eks-clu1 get cm aws-lbc-targets-$(kubectl get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.metadata.name}{end}')

kubectl \--context pri-eks-clu1 describe cm aws-lbc-targets-$(kubectl get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.metadata.name}{end}')

The successful output shows that ConfigMap contains registered targets, confirming LBC target matching between nginxpods and NLB.

Step 5: Deploy nginxapp on secondary EKS cluster

Using CloudShell, run the following commands to create a deployment object and a service object of type ClusterIP on the secondary EKS cluster:

kubectl \--context sec-eks-clu1 create \-f nginxapp.yaml

kubectl \--context sec-eks-clu1 create \-f nginx_sec-eks-clu1.yaml

kubectl get all

The successful output shows the nginxdeployment with 2 pods running and the ClusterIP service created on the Secondary cluster.

Step 6: Create Multiple ClusterTargetGroupBindings on the Secondary EKS Cluster

Using CloudShell, run the following command to create the TargetGroupBinding with the multiClusterTargetGroup flag on the Secondary EKS Cluster:

\# Get targetGroup ARN from Primary EKS Cluster

targetgrouparn=$(kubectl \--context pri-eks-clu1 get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.spec.targetGroupARN}{end}')

lb=$(kubectl \--context pri-eks-clu1 get svc nginx \-o jsonpath='{.status.loadBalancer.ingress\[0\].hostname}' | sed \-n 's/.\*\\(k8s-default-nginx-\[a-z0-9\]\*\\).\*/\\1/p')

\# Get Security GroupId

secgrpid=$(aws ec2 describe-security-groups \--group-ids $(aws elbv2 describe-load-balancers \--names ${lb} \--query 'LoadBalancers\[\*\].SecurityGroups\[\]' \--output text) \--query "SecurityGroups\[?Description=='\[k8s\] Shared Backend SecurityGroup for LoadBalancer'\].GroupId" \--output text)

\# update tgb.yaml file

sed "s|\<securitygroupid\>|${secgrpid}|g; s|\<targetgrouparn\>|${targetgrouparn}|g" tgb.yaml \> tgb_sec.yaml

kubectl \--context sec-eks-clu1 create \-f tgb_sec.yaml

Step 7: Verify ConfigMap creation on secondary EKS cluster

Using CloudShell, run the following commands to verify the ConfigMap object and its contents were created by the controller along with the targetGroupBinding:

kubectl \--context sec-eks-clu1 get cm aws-lbc-targets-nginx

kubectl \--context sec-eks-clu1 describe cm aws-lbc-targets-nginx

Successful output will show the ConfigMap aws-lbc-targets-nginx information containing the IP address and port of the nginx target.

Step 8 : Verify Target Registration on Both EKS Clusters

Using CloudShell, run the following command to verify that NLB has registered targets from both the primary and secondary EKS clusters:

aws elbv2 describe-target-health \--target-group-arn $( kubectl get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.spec.targetGroupARN}{end}') \--query 'TargetHealthDescriptions\[\*\].\[Target.Id,Target.AvailabilityZone,TargetHealth.State\]' \--output table

The successful output shows that healthy targets are distributed across the primary and secondary EKS clusters, confirming a successful multi-cluster configuration.

Step 9 : Verify Traffic Distribution on EKS Clusters

Using CloudShell, run the following commands to perform a synthetic workload test and verify traffic distribution to Pods on both the primary and secondary EKS clusters:

\# Get NLB hostname

lb=$(kubectl \--context pri-eks-clu1 get svc nginx \-o jsonpath='{.status.loadBalancer.ingress\[0\].hostname}')

\# Run ApacheBench test

ab \-n 1000 \-c 40 http://$lb/

\# Check logs on Primary cluster

echo "Primary EKS Cluster:"

kubectl \--context pri-eks-clu1 logs deployment/nginx \--tail=2

\# Check logs on Secondary cluster

echo "Secondary EKS Cluster:"

kubectl \--context sec-eks-clu1 logs deployment/nginx \--tail=2

The successful output shows HTTP requests being logged in both clusters, confirming that NLB is properly distributing traffic across all nginx pods on the primary and secondary EKS clusters.

Cleanup: Using CloudShell, run the commandsafter:

kubectl \--context sec-eks-clu1 delete targetgroupbinding nginx

kubectl \--context sec-eks-clu1 delete svc nginx

kubectl \--context pri-eks-clu1 patch service nginx \--patch '{"metadata": {"annotations": {"service.beta.kubernetes.io/aws-load-balancer-attributes": "deletion_protection.enabled=false"}}}' \--type=merge

kubectl \--context pri-eks-clu1 delete svc nginx

kubectl \--context pri-eks-clu1 delete deployment nginx

You have successfully tested migrating existing workloads in Scenario 1\. In Scenario 2, you can explore how to deploy multi-cluster load balancing for new deployments.

### **Scenario 2: Deploy multi-cluster load balancing for new deployments**

In this scenario, you create a new NLB that is designed to support multi-cluster traffic distribution from the start. This deployment requires LBC 2.10 or later to be installed on both EKS clusters.

Step 1 : Deploy nginx with LoadBalancer Service on the primary EKS cluster

Using CloudShell, run the following commands:

kubectl \--context pri-eks-clu1 create \-f nginxapp.yaml

kubectl \--context pri-eks-clu1 create \-f nginxsvc.yaml

Step 2 : Verify ConfigMap creation using LBC

Using CloudShell, run the following commands to verify that the ConfigMap object and its contents are created with targetGroupBinding:

kubectl \--context pri-eks-clu1 get cm aws-lbc-targets-$(kubectl get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.metadata.name}{end}')

kubectl \--context pri-eks-clu1 describe cm aws-lbc-targets-$(kubectl get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.metadata.name}{end}')

The successful output shows the ConfigMap IP address and port of the nginxtarget, confirming the correct configuration of LBC.

Step 3 : Deploy nginx service on secondary EKS cluster

Using CloudShell, run the following commands to create nginx service deployment and ClusterIP on secondary EKS cluster:

kubectl \--context sec-eks-clu1 create \-f nginxapp.yaml

kubectl \--context sec-eks-clu1 create \-f nginx_sec-eks-clu1.yaml

Step 4 : Configure TargetGroupBinding on secondary EKS cluster

Using CloudShell, run the following commands to create a TargetGroupBinding cluster that supports multi-clusters:

\# Get targetGroup ARN from Primary EKS Cluster

targetgrouparn=$(kubectl \--context pri-eks-clu1 get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.spec.targetGroupARN}{end}')

lb=$(kubectl \--context pri-eks-clu1 get svc nginx \-o jsonpath='{.status.loadBalancer.ingress\[0\].hostname}' | sed \-n 's/.\*\\(k8s-default-nginx-\[a-z0-9\]\*\\).\*/\\1/p')

\# Get Security GroupId

secgrpid=$(aws ec2 describe-security-groups \--group-ids $(aws elbv2 describe-load-balancers \--names ${lb} \--query 'LoadBalancers\[\*\].SecurityGroups\[\]' \--output text) \--query "SecurityGroups\[?Description=='\[k8s\] Shared Backend SecurityGroup for LoadBalancer'\].GroupId" \--output text)

\# update tgb.yaml file

sed "s|\<securitygroupid\>|${secgrpid}|g; s|\<targetgrouparn\>|${targetgrouparn}|g" tgb.yaml \> tgb_p2.yaml

kubectl \--context sec-eks-clu1 create \-f tgb_p2.yaml

Step 5: Verify the ConfigMap on the secondary EKS cluster

Using CloudShell, run the following commands to verify the ConfigMap object and its contents were created by the controller:

kubectl \--context sec-eks-clu1 get cm aws-lbc-targets-$(kubectl \--context sec-eks-clu1 get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.metadata.name}{end}')

kubectl \--context sec-eks-clu1 describe cm aws-lbc-targets-$(kubectl \--context sec-eks-clu1 get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.metadata.name}{end}')

The successful output shows the ConfigMap containing the target IPs and ports of the nginx secondary EKS cluster.

Step 6 : Verify Target Registration on Both EKS Clusters

Using CloudShell, run the following command to verify that NLB has successfully registered targets from both the primary and secondary EKS clusters:

aws elbv2 describe-target-health \--target-group-arn $( kubectl get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.spec.targetGroupARN}{end}') \--query 'TargetHealthDescriptions\[\*\].\[Target.Id,Target.AvailabilityZone,TargetHealth.State\]' \--output table

Expected output:

\----------------------------------------------

| DescribeTargetHealth |

\+--------------+--------------+-----------+

| xx.xx.xx.xxx| us-east-1b | healthy |

| xx.xx.xx.xxx| us-east-1c | healthy |

| xx.xx.xx.xxx| us-east-1c | healthy |

| xx.xx.xx.xxx| us-east-1b | healthy |

\+--------------+--------------+-----------+

The output shows healthy targets distributed across the primary and secondary EKS clusters, confirming a successful multi-cluster configuration.

Cleanup: Using AWS CloudShell, run the following commands to clean up all resources:

kubectl \--context sec-eks-clu1 delete targetgroupbinding nginx

kubectl \--context sec-eks-clu1 delete svc nginx

kubectl \--context pri-eks-clu1 patch service nginx \--patch '{"metadata": {"annotations": {"service.beta.kubernetes.io/aws-load-balancer-attributes": "deletion_protection.enabled=false"}}}' \--type=merge

kubectl \--context pri-eks-clu1 delete svc nginx

kubectl \--context pri-eks-clu1 delete deployment nginx

aws cloudformation delete-stack \--stack-name eks-nlb

This demo shows how the multi-cluster NLB feature enhances service resiliency by enabling traffic distribution across multiple EKS clusters for both existing and new load balancer deployments.

## **Considerations**

- This feature currently only supports even, active-active distribution between targets in both EKS clusters. Weighted target load balancing is not currently supported support.

- Existing VPC account limits, API limits, and NLB limits remain in effect with this feature.

- It is best practice to enable deletion protection on NLB to avoid accidental deletion.

- Each service has a one-to-one mapping to the ConfigMap object, ensuring proper management of TargetGroupBindings for each service.

- LBC writes all targets to the ConfigMap (limited to 1MB) in a single update rather than incrementally. Monitor EKS control plane health accordingly.

## **Conclusion**

In the first part of this series, we demonstrated how to achieve resiliency across multiple Amazon EKS clusters using the new NLB feature in a declarative manner. As organizations increasingly migrate and modernize applications to Kubernetes environments, deploying robust and scalable solutions becomes critical to maintaining high availability. Distributing workloads across Clusters not only improve fault tolerance, but also streamline upgrades and enhance disaster recovery. Visit our [EKS Best Practices Center](https://github.com/aws/aws-eks-best-practices) for architectural patterns, security guidelines, and cost optimization strategies for production workloads.

Stay tuned for future posts in this series, where we explore additional design patterns to further improve the resiliency of workloads running on Amazon EKS.

## **About the Authors**

| <img src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2025/09/21/krishna.png" width="600"> | Krishna Sarabu is a Senior Database Engineer at AWS. He focuses on containers, application modernization, infrastructure, and open-source database tools Amazon RDS for PostgreSQL and Amazon Aurora PostgreSQL. He enjoys working with users to help design, deploy, and optimize relational database workloads on AWS.                                                                                       |
| :---------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ![alt text](https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2025/09/21/anuj.png)               | Anuj Butail is a Principal Solutions Architect at AWS. Based in San Francisco, he helps users in San Francisco and Silicon Valley design and build large-scale applications on AWS. He specializes in AWS, edge services, and containers. He enjoys playing tennis, reading, and spending time with his family.                                                                                                |
| ![alt text](https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2025/09/21/pushkar.png)            | Pushkar Patil is a Product Owner for the AWS networking team based in California. He has over a decade of experience driving product innovation and strategy in cloud computing and infrastructure. Pushkar has successfully launched multiple new products by understanding user needs and delivering innovative solutions. When not working, you can find this cricket enthusiast traveling with his family. |
