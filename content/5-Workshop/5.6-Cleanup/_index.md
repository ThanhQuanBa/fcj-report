---
title: "Clean Up"
date: "2025-11-11"
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

To avoid unnecessary charges — especially from the **NAT Gateway** — you should remove all created resources after finishing the workshop.

Follow the steps below in order:

---

### 9.1 Terminate EC2 Instances

1. Open **EC2 Console**
2. Select both `EC2-Public` and `EC2-Private`
3. Click **Instance state → Terminate** (This process may take some time)
4. Confirm termination

![EC2 instances termination](/images/5-Workshop/5.6-Cleanup/ec2-instance-termination.png)

---

### 9.2 Delete NAT Gateway

1. Go to **VPC Console → NAT Gateways**
2. Select `Workshop-NAT`
3. Click **Actions → Delete NAT Gateway** (This process may take some time)
4. Confirm the action

> Wait until NAT Gateway shows as **Deleted** before moving on.

![NAT Gateway deletion](/images/5-Workshop/5.6-Cleanup/NAT-termination.png)

---

### 9.3 Release Elastic IP

After NAT Gateway is deleted:

1. Go to **Elastic IPs**
2. Select the allocated address
3. Click **Actions** → **Release Elastic IP address**
4. Confirm release (empty Elastic IP addresses list)

---

### 9.4 Detach and Delete Internet Gateway

1. In **Internet Gateways**
2. Select `Workshop-IGW`
3. Click **Actions → Detach from VPC**
4. Then **Actions → Delete Internet gateway**
5. Confirm deletion (empty Internet gateways list)
