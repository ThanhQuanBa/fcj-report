---
title: "Worklog Tuần 2"
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu tuần 2:

- Hiểu rõ vai trò và cách hoạt động của dịch vụ máy chủ ảo **EC2** (Elastic Compute Cloud).
- Thành thạo quy trình khởi tạo, quản lý và chấm dứt (Terminate) một EC2 Instance.
- Nắm vững khái niệm và cách sử dụng **IAM Roles** để cấp quyền truy cập an toàn cho tài nguyên EC2.
- Sử dụng **AWS CLI** để thực hiện các thao tác quản lý EC2 cơ bản.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                                                                                                                                                                                                | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| :-- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------- | :-------------- | :---------------------------------------- |
| 2   | - Đọc và hiểu các thành phần cốt lõi của **Amazon EC2** (AMI, Instance Type, Key Pair, Security Group). <br> - **Thực hành:** Khởi tạo Instance EC2 đầu tiên (chọn loại Free Tier).                                                                                                      | 15/09/2025   | 15/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Tìm hiểu về cơ chế cấp quyền an toàn **IAM Roles for EC2** (Instance Profiling). <br> - **Thực hành:** <br>&emsp; + Tạo IAM Policy cho phép `s3:ListBucket`. <br>&emsp; + Tạo IAM Role và gán Policy này. <br>&emsp; + Gán Role vừa tạo cho Instance EC2.                              | 16/09/2025   | 16/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tìm hiểu các lệnh quản lý dịch vụ **EC2 với AWS CLI**. <br> - **Thực hành:** <br>&emsp; + Dùng CLI để xem thông tin EC2 Instance (`describe-instances`). <br>&emsp; + Dùng CLI để Stop/Start Instance. <br>&emsp; + Quản lý Security Group thông qua CLI.                              | 17/09/2025   | 17/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - **Thực hành Tích hợp:** Kiểm tra quyền hạn của IAM Role. <br> - Đăng nhập SSH/Session Manager vào Instance EC2. <br> - **Thực hành:** Dùng lệnh `aws s3 ls` (CLI) từ bên trong EC2 để kiểm tra xem Instance có thể đọc (list) các S3 Bucket hay không (chứng minh IAM Role hoạt động). | 18/09/2025   | 18/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Quản lý Vòng đời & Chi phí:** <br> - Ôn tập các trạng thái vòng đời của EC2 (Pending, Running, Stopping, Terminated). <br> - **Thực hành:** <br>&emsp; + **Terminate** (Chấm dứt) Instance EC2. <br>&emsp; + Xóa IAM Role, Policy đã tạo để dọn dẹp tài nguyên.                      | 19/09/2025   | 19/09/2025      | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 2:

- **EC2:** Đã nắm được cách tạo, cấu hình và quản lý vòng đời của máy ảo EC2 thông qua Console và CLI. Hiểu rõ các thành phần cấu tạo nên một EC2 instance.
- **IAM Roles:** Hiểu rõ ưu điểm và cách dùng **IAM Role** (Instance Profile) để cấp quyền truy cập dịch vụ AWS một cách bảo mật cho các tài nguyên EC2, loại bỏ việc lưu trữ Access Key trên máy chủ.
- **AWS CLI Nâng Cao:** Đã biết cách sử dụng các lệnh CLI để quản lý trạng thái của EC2 Instance (Start, Stop, Describe) và thực hiện các thao tác từ xa.
- **Thực hành Tích Hợp:** Đã chứng minh được việc EC2 Instance có thể thực thi các hành động trên S3 (ví dụ: `aws s3 ls`) nhờ vào quyền được cấp qua IAM Role, xác nhận mô hình bảo mật hoạt động hiệu quả.
- **Dọn dẹp Tài nguyên:** Nắm được quy trình chấm dứt EC2 Instance và dọn dẹp các tài nguyên IAM liên quan để đảm bảo không phát sinh chi phí.
