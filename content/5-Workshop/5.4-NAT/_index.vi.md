---
title: "Tạo NAT Gateway"
date: "2025-11-11"
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

NAT Gateway cho phép các instance trong **Private Subnet** truy cập Internet
_mà không bị lộ ra ngoài công cộng_. Đây là cách mà EC2 riêng tư (private EC2) sẽ thực hiện lệnh `yum install`, tải xuống các bản cập nhật, v.v.

### 5.1 Cấp phát Elastic IP

1. Trong menu VPC bên trái, chọn **Elastic IPs**
2. Nhấn **Allocate Elastic IP address**
3. Chọn các cài đặt mặc định
4. Nhấn **Allocate**

![Allocate Elastic IP window](/images/5-Workshop/5.4-NAT/elastic-IP-allocate.png)

---

### 5.2 Tạo NAT Gateway

1. Trong bảng điều khiển VPC bên trái, đi đến **NAT gateways**
2. Nhấn **Create NAT gateway**
3. Cấu hình như sau:

| Trường (Field)        | Giá trị (Value)                 |
| --------------------- | ------------------------------- |
| Name                  | `Workshop-NAT`                  |
| Availability mode     | Zonal                           |
| Subnet                | `Public-Subnet`                 |
| Connectivity type     | Public                          |
| Elastic IP allocation | Chọn IP bạn vừa tạo ở bước trên |

4. Nhấn **Create NAT gateway**

![Create NAT Gateway screen with selected subnet + EIP](/images/5-Workshop/5.4-NAT/NAT-creation.png)

---

### 5.3 Chờ NAT Gateway chuyển sang trạng thái "Available"

- Trạng thái ban đầu sẽ là **Pending**
- Hãy đợi cho đến khi nó chuyển sang **Available**

![NAT Gateway state](/images/5-Workshop/5.4-NAT/NAT-state.png)

---

### Kiểm tra Kết quả

| Tài nguyên                   | Trạng thái (Status) |
| ---------------------------- | ------------------- |
| NAT Gateway (`Workshop-NAT`) | Available           |
| Elastic IP                   | Associated          |

Bây giờ bạn đã sẵn sàng để định tuyến lưu lượng truy cập subnet riêng tư qua NAT.
