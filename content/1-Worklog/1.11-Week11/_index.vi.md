---
title: "Worklog Tuần 11"
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu tuần 11:

- Hiểu rõ vai trò của **AWS WAF** (Web Application Firewall) và **AWS Shield** trong việc bảo vệ ứng dụng khỏi các cuộc tấn công Lớp 7 (WAF) và DDoS (Shield).
- Nắm vững cơ chế hoạt động và lợi ích của dịch vụ phát hiện mối đe dọa thông minh **Amazon GuardDuty**.
- Ôn tập và áp dụng các nguyên tắc bảo mật nâng cao như **IAM Policy Best Practices** và **Secrets Management**.
- Thực hành cấu hình các Rule bảo mật cơ bản.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                                                                                                                                                                                                         | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| :-- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :----------- | :-------------- | :---------------------------------------- |
| 2   | - Đọc và hiểu kiến trúc **AWS WAF** và **Web ACL** (Access Control List). <br> - Tìm hiểu về các loại tấn công Lớp 7 (SQL Injection, XSS) mà WAF bảo vệ. <br> - **Thực hành:** Tạo một Web ACL và tìm hiểu các Managed Rule Group.                                                                | 17/11/2025   | 17/11/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Tìm hiểu về **AWS Shield Standard/Advanced** (DDoS Protection) và cách nó tích hợp với WAF/Route 53. <br> - **Thực hành WAF:** <br>&emsp; + Tạo một Rule dựa trên Rate-based (giới hạn tần suất truy cập). <br>&emsp; + Thử nghiệm chặn các IP cụ thể (IP Set) qua WAF.                         | 18/11/2025   | 18/11/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tìm hiểu về dịch vụ phát hiện mối đe dọa **Amazon GuardDuty** (sử dụng Machine Learning). <br> - **Thực hành:** Kích hoạt GuardDuty và xem xét các mẫu Findings (mối đe dọa giả định) để hiểu cách dịch vụ cảnh báo.                                                                            | 19/11/2025   | 19/11/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Ôn tập và áp dụng **IAM Policy Best Practices** (Principle of Least Privilege). <br> - Tìm hiểu về **AWS Secrets Manager** (tự động xoay vòng credentials) và **AWS Systems Manager Parameter Store** (lưu trữ cấu hình). <br> - **Thực hành:** Xem xét lại các IAM Policy đã tạo trong tuần 2. | 20/11/2025   | 20/11/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Tổng hợp & Dọn dẹp Tài nguyên:** <br> - **Thực hành:** <br>&emsp; + Xóa Web ACL/WAF Rules. <br>&emsp; + **Vô hiệu hóa GuardDuty**. <br>&emsp; + Tổng kết các biện pháp bảo mật (Perimeter Security, Monitoring, Secrets Management) đã học.                                                   | 21/11/2025   | 21/11/2025      | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 11:

- **Bảo vệ Vành đai:** Hiểu rõ cách WAF và Shield hoạt động ở rìa mạng để bảo vệ các tài nguyên như CloudFront, ALB.
- **Phát hiện Mối đe dọa:** Thành thạo việc kích hoạt và theo dõi các cảnh báo từ **GuardDuty** để phát hiện các hành vi đáng ngờ trong tài khoản AWS.
- **Quản lý Danh tính:** Củng cố kiến thức về **IAM Policy** và hiểu tầm quan trọng của việc quản lý bí mật (Secrets Management) bằng các dịch vụ chuyên dụng.
- **Xây dựng Bảo mật:** Nắm được cách tích hợp các dịch vụ bảo mật vào kiến trúc ứng dụng (ví dụ: dùng WAF trước ALB).
