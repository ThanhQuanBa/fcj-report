---
title: "Khởi chạy EC2 Instances"
date: "2025-11-11"
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

Bây giờ bạn sẽ triển khai hai EC2 instance:

| Instance    | Subnet         | Quyền truy cập                                           |
| ----------- | -------------- | -------------------------------------------------------- |
| EC2-Public  | Public-Subnet  | SSH trực tiếp từ Internet                                |
| EC2-Private | Private-Subnet | Không có IP công khai — chỉ truy cập được qua Public EC2 |

Cả hai sẽ sử dụng cùng một AMI và loại instance để đơn giản hóa.

---

### 7.1 Tạo Key Pair

1. Đi đến **EC2 Console**
2. Menu bên trái → **Key Pairs**
3. Nhấn **Create key pair**
4. Thiết lập:
   - **Name:** `Workshop-Key`
   - **Type:** RSA
   - **Format:** `.pem` (Linux/Mac) hoặc `.ppk` (Windows PuTTY)
5. Tải xuống và lưu trữ an toàn

![Key Pair Creation Menu](/images/5-Workshop/5.5-EC2/ec2-keypair-creation.png)

> _Key này sẽ được sử dụng sau để SSH._

---

### 7.2 Khởi chạy Public EC2 Instance

1. Trong EC2 Console → Nhấn **Instances** → Nhấn **Launch instances**
2. Name: `EC2-Public`
3. Chọn AMI:
   - **Amazon Linux**
4. Instance type: **t3.micro** (Đủ điều kiện bậc miễn phí)
5. Chọn key pair hiện có: `Workshop-Key`
6. Cài đặt mạng (Nhấn **Edit** để hiện menu):
   - **VPC:** Workshop-VPC
   - **Subnet:** Public-Subnet
   - **Auto-assign Public IP:** Enabled (Đã bật)
7. Cấu hình Security Group:
   - Tên SG mới: `Public-EC2-SG`
   - Quy tắc Inbound (Inbound rule):
     - Type: **SSH**
     - Source Type: `Custom`
     - Source: `0.0.0.0/0`
8. Để mọi thứ khác ở mặc định.
9. Nhấn **Launch instance**

![EC2 Instance Launch Menu](/images/5-Workshop/5.5-EC2/ec2-instance-creation.png)

---

### 7.3 Khởi chạy Private EC2 Instance

1. Trong EC2 Console → Nhấn **Instances** → Nhấn **Launch instances** lần nữa
2. Name: `EC2-Private`
3. AMI + instance type = giống như public EC2
4. Sử dụng key pair: `Workshop-Key`
5. Cài đặt mạng:
   - **VPC:** Workshop-VPC
   - **Subnet:** Private-Subnet
   - **Auto-assign Public IP:** Disabled (Đã tắt)
6. Security Group:
   - Name: `Private-EC2-SG`
   - Type: **SSH**
   - Source Type: `Custom`
   - Source: `Public-EC2-SG` **(không phải Internet)**

## ![EC2 Private Instance Launch Menu](/images/5-Workshop/5.5-EC2/ec2-instance-private-creation.png)

### 7.4 Xác minh Instances

| Instance    | IP              | Kết quả mong đợi                 |
| ----------- | --------------- | -------------------------------- |
| EC2-Public  | Có IPv4 Public  | Có thể SSH từ máy của bạn        |
| EC2-Private | Không Public IP | Chỉ tiếp cận được qua Public EC2 |

![EC2 Instances List](/images/5-Workshop/5.5-EC2/ec2-instance-list.png)
