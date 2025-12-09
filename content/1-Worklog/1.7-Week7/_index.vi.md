---
title: "Worklog Tuần 7"
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu tuần 7:

- Hiểu rõ vai trò và cách cấu hình **EC2 Auto Scaling Group (ASG)** để tự động mở rộng và thu hẹp tài nguyên.
- Nắm vững kiến trúc và cách hoạt động của **Elastic Load Balancer (ELB)** (đặc biệt là ALB) để phân phối lưu lượng truy cập.
- Thành thạo việc sử dụng **Amazon CloudWatch** để thu thập metrics, logs, tạo Dashboard và thiết lập Alarms.
- Thực hành thiết lập **Scaling Policy** dựa trên các chỉ số giám sát.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                                                                                                                                                                                                               | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| :-- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :----------- | :-------------- | :---------------------------------------- |
| 2   | - Đọc và hiểu kiến trúc **Auto Scaling Group (ASG)**, Launch Template. <br> - Tìm hiểu các loại Load Balancer (Classic, Application, Network). <br> - **Thực hành:** Tạo Launch Template và cấu hình một ASG cơ bản.                                                                                    | 20/10/2025   | 20/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Tìm hiểu chuyên sâu về **Application Load Balancer (ALB)** và các thành phần (Listener, Target Group, Health Check). <br> - **Thực hành:** <br>&emsp; + Tạo ALB, Target Group. <br>&emsp; + Đăng ký các EC2 Instance của ASG vào Target Group.                                                        | 21/10/2025   | 21/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tìm hiểu về dịch vụ giám sát **Amazon CloudWatch** (Metrics, Logs, Events). <br> - **Thực hành:** <br>&emsp; + Xem các Metrics của EC2, ALB, ASG trên CloudWatch. <br>&emsp; + Tạo một CloudWatch Dashboard để theo dõi các chỉ số quan trọng (CPU Utilization, Request Count).                       | 22/10/2025   | 22/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Tìm hiểu về **CloudWatch Alarms** và các loại **Scaling Policy** (Target Tracking, Step Scaling). <br> - **Thực hành:** <br>&emsp; + Tạo CloudWatch Alarm dựa trên chỉ số CPU Utilization (ví dụ: > 80%). <br>&emsp; + Cấu hình Scaling Policy cho ASG sử dụng Alarm vừa tạo (scale out khi CPU cao). | 23/10/2025   | 23/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Kiểm tra & Dọn dẹp Tài nguyên:** <br> - **Thực hành:** <br>&emsp; + Kiểm tra quá trình Scale Out/In hoạt động. <br>&emsp; + **Dọn dẹp:** Xóa CloudWatch Alarms, xóa ASG (sẽ tự động chấm dứt EC2), xóa ALB, xóa Launch Template.                                                                    | 24/10/2025   | 24/10/2025      | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 7:

- **Khả năng mở rộng:** Triển khai thành công kiến trúc ứng dụng có khả năng tự động mở rộng (Scale Out/In) thông qua **ASG** và **Launch Template**.
- **Cân bằng tải:** Hiểu và cấu hình được **Application Load Balancer (ALB)** để phân phối lưu lượng truy cập và thực hiện Health Check hiệu quả.
- **Giám sát (Observability):** Nắm vững cách sử dụng **CloudWatch** để thu thập, hình dung (Dashboard) và theo dõi các chỉ số sức khỏe của tài nguyên.
- **Tự động hóa:** Thiết lập thành công cơ chế tự động mở rộng dựa trên hiệu suất (Scaling Policy và CloudWatch Alarms).
- **Quản lý Chi phí:** Nắm vững quy trình dọn dẹp các tài nguyên đắt tiền như ALB và ASG.
