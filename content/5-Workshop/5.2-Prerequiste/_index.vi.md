---
title: "Điều kiện tiên quyết"
date: "2025-11-11"
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

Trước khi bắt đầu bài thực hành, hãy đảm bảo bạn có quyền truy cập vào những mục sau:

### Tài khoản & Quyền truy cập

- Một **Tài khoản AWS**
- **IAM User** có quyền truy cập vào:
  - Amazon VPC
  - Amazon EC2
  - Elastic IP
  - NAT Gateway
  - Internet Gateway
- Chính sách khuyến nghị: **AdministratorAccess** (chỉ dành cho mục đích thực hành lab)

> Nếu bạn đang sử dụng IAM user, hãy đảm bảo bạn có access keys hoặc thông tin đăng nhập console.

### Lưu ý về Chi phí

Bài thực hành này bao gồm việc tạo một **NAT Gateway**, đây là tài nguyên có tính phí.
Để giảm thiểu chi phí, bạn nên xóa tất cả các tài nguyên đã tạo trong bước dọn dẹp.

_chi phí dự kiến: ~ vài USD nếu tài nguyên được dọn dẹp trong cùng ngày_

### Công cụ Yêu cầu

| Công cụ                       | Mục đích sử dụng                                               |
| ----------------------------- | -------------------------------------------------------------- |
| AWS Management Console        | Giao diện chính để xây dựng VPC, Subnet, Route Table           |
| SSH client (PuTTY / Terminal) | Dùng để kết nối vào EC2 instance sau này                       |
| Key Pair                      | Cần thiết để đăng nhập EC2 (bạn sẽ tạo nó trong bài thực hành) |

### Lựa chọn Khu vực (Region)

Chúng tôi khuyên bạn nên sử dụng khu vực (region) gần nhất để có độ trễ thấp hơn.  
Ví dụ: **ap-southeast-1 (Singapore)** cho người dùng tại Việt Nam.

> Bạn phải giữ nguyên một region cho tất cả các bước trong bài thực hành này.
