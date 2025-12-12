---
title: "Tạo VPC & Subnets"
date: "2025-11-11"
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

# Tạo VPC & Subnets

Trong phần này, bạn sẽ tạo Virtual Private Cloud (VPC) của riêng mình và xác định hai subnet:

- 1 Public Subnet (Có thể truy cập Internet)
- 1 Private Subnet (Không có quyền truy cập Internet trực tiếp)

### 3.1 Tạo VPC

1. Mở **AWS Management Console**
2. Điều hướng đến **Services → VPC**
3. Nhấn **Create VPC**
4. Chọn **VPC Only**
5. Nhập các thông tin chi tiết:
   - **Name tag:** `Workshop-VPC`
   - **IPv4 CIDR:** `10.0.0.0/16`
6. Giữ nguyên các cài đặt khác ở mặc định
7. Nhấn **Create VPC**

![VPC Creation Menu](/images/5-Workshop/5.3-VPC-Subnets/vpc-creation.png)

---

### 3.2 Tạo Public Subnet

1. Ở menu bên trái, nhấn **Subnets**
2. Nhấn **Create subnet**
3. Cấu hình như sau:
   - **VPC:** `Workshop-VPC`
   - **Subnet name:** `Public-Subnet`
   - **Availability Zone:** (bất kỳ, ví dụ: `ap-southeast-1a`)
   - **IPv4 CIDR block:** `10.0.1.0/24`
4. Nhấn **Create subnet**

![Subnet Creation Menu](/images/5-Workshop/5.3-VPC-Subnets/public-subnet-creation.png)

---

### 3.3 Tạo Private Subnet

1. Nhấn **Create subnet** một lần nữa
2. Điền các giá trị:
   - **VPC:** `Workshop-VPC`
   - **Subnet name:** `Private-Subnet`
   - **Availability Zone:** (có thể giống hoặc khác)
   - **IPv4 CIDR block:** `10.0.2.0/24`
3. Nhấn **Create subnet**

> Quá trình này tương tự như mục 3.2 với chỉ một vài khác biệt nhỏ. Vui lòng xem mục 3.2 để tham khảo.

---

### 3.4 Bật Tự động gán IP công khai (Chỉ dành cho Public Subnet)

1. Đi đến **Subnets**
2. Chọn **Public-Subnet**
3. Nhấn **Actions → Edit subnet settings**
4. Bật tùy chọn:
   - ☑ **Auto-assign public IPv4**
5. Nhấn **Save**

![Subnet Setting Menu](/images/5-Workshop/5.3-VPC-Subnets/public-subnet-IP-assign.png)

---

**Kết quả mong đợi:**
Bạn hiện đã có một VPC với hai subnet sẵn sàng để sử dụng.

Các thành phần đã tạo cho đến nay:

| Tài nguyên         | Tên            | CIDR        |
| ------------------ | -------------- | ----------- |
| VPC                | Workshop-VPC   | 10.0.0.0/16 |
| Subnet 1 (Public)  | Public-Subnet  | 10.0.1.0/24 |
| Subnet 2 (Private) | Private-Subnet | 10.0.2.0/24 |
