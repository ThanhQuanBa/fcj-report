---
title: "Dọn dẹp tài nguyên"
date: "2025-11-11"
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

Để tránh phát sinh các chi phí không cần thiết — đặc biệt là từ **NAT Gateway** — bạn nên xóa tất cả các tài nguyên đã tạo sau khi hoàn thành bài thực hành.

Hãy thực hiện các bước dưới đây theo đúng thứ tự:

---

### 9.1 Hủy (Terminate) các EC2 Instance

1. Mở **EC2 Console**
2. Chọn cả 2 máy ảo `EC2-Public` và `EC2-Private`
3. Nhấn **Instance state → Terminate** (Quá trình này có thể mất một chút thời gian)
4. Xác nhận hủy (Terminate)

![EC2 instances termination](/images/5-Workshop/5.6-Cleanup/ec2-instance-termination.png)

---

### 9.2 Xóa NAT Gateway

1. Truy cập **VPC Console → NAT Gateways**
2. Chọn `Workshop-NAT`
3. Nhấn **Actions → Delete NAT Gateway** (Quá trình này có thể mất thời gian)
4. Xác nhận hành động xóa

> Hãy đợi cho đến khi trạng thái NAT Gateway chuyển sang **Deleted** hoàn toàn trước khi chuyển sang bước tiếp theo.

![EC2 instances termination](/images/5-Workshop/5.6-Cleanup/NAT-termination.png)

---

### 9.3 Giải phóng Elastic IP

Sau khi NAT Gateway đã bị xóa thành công:

1. Truy cập **Elastic IPs**
2. Chọn địa chỉ IP đã được cấp phát
3. Nhấn **Actions** → **Release Elastic IP address**
4. Xác nhận giải phóng (danh sách Elastic IP trống)

---

### 9.4 Tách và Xóa Internet Gateway

1. Tại **Internet Gateways**
2. Chọn `Workshop-IGW`
3. Nhấn **Actions → Detach from VPC**
4. Sau đó nhấn **Actions → Delete Internet gateway**
5. Xác nhận xóa (danh sách Internet gateways trống)

---

### 9.5 Xóa Subnets

1. Truy cập **Subnets**
2. Xóa `Public-Subnet`
3. Xóa `Private-Subnet`
4. Xác nhận xóa (danh sách Subnets trống)

---

### 9.6 Xóa VPC

1. Tại **VPC Console**
2. Chọn `Workshop-VPC`
3. Nhấn **Actions → Delete VPC**
4. Xác nhận xóa (danh sách VPCs trống)

---

### 9.7 Xóa Route Tables

1. Mở **Route Tables**
2. Chọn `Public-RT` → **Delete**
3. Chọn `Private-RT` → **Delete**
4. Xác nhận xóa (các route table đã tạo không còn tồn tại)

---

### Hoàn tất dọn dẹp
