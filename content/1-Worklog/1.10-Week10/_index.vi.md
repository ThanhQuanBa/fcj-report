---
title: "Worklog Tuần 10"
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Mục tiêu tuần 10:

- Hiểu rõ vai trò và cách thức hoạt động của dịch vụ hàng đợi tin nhắn **Amazon Simple Queue Service (SQS)** (Standard & FIFO).
- Nắm vững kiến trúc **Publish/Subscribe** với dịch vụ thông báo **Amazon Simple Notification Service (SNS)**.
- Thành thạo việc tạo và cấu hình các máy trạng thái **AWS Step Functions** để điều phối các dịch vụ AWS.
- Thực hành xây dựng một quy trình làm việc phi đồng bộ sử dụng SQS, SNS và Lambda.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                                                                                                                                                                                                                          | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| :-- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------- | :-------------- | :---------------------------------------- |
| 2   | - Đọc và hiểu kiến trúc **Amazon SQS** (Hàng đợi tin nhắn) và sự khác biệt giữa Standard Queue và FIFO Queue. <br> - **Thực hành:** Tạo một SQS Standard Queue, gửi và nhận tin nhắn thủ công qua Console.                                                                                                         | 10/11/2025   | 10/11/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Tìm hiểu về dịch vụ thông báo **Amazon SNS** (Topic, Subscriber) và mô hình Pub/Sub. <br> - **Thực hành:** <br>&emsp; + Tạo một SNS Topic. <br>&emsp; + Tạo SQS Queue và hàm Lambda (từ tuần 8) làm Subscriber cho Topic này. <br>&emsp; + Gửi tin nhắn đến Topic và kiểm tra xem SQS/Lambda có nhận được không. | 11/11/2025   | 11/11/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tìm hiểu về **AWS Step Functions** (State Machine, Task State, Choice State). <br> - **Thực hành:** <br>&emsp; + Tạo một hàm Lambda mới (ví dụ: `ProcessStep1`). <br>&emsp; + Tạo một State Machine đơn giản (chỉ có một bước Task) để gọi hàm Lambda này.                                                       | 12/11/2025   | 12/11/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Tìm hiểu về cách **Step Functions** điều phối luồng logic (Sequence, Choice, Parallel). <br> - **Thực hành:** Mở rộng State Machine đã tạo: <br>&emsp; + Thêm một bước `Choice` dựa trên kết quả đầu vào. <br>&emsp; + Tích hợp SQS (ví dụ: gửi tin nhắn vào hàng đợi nếu luồng đi theo một nhánh nhất định).    | 13/11/2025   | 13/11/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Dọn dẹp Tài nguyên & Tích hợp Tổng quan:** <br> - **Thực hành:** <br>&emsp; + Xóa State Machine, SQS Queue, SNS Topic. <br>&emsp; + Ôn tập về cách các dịch vụ này (SQS, SNS, Step Functions) giải quyết vấn đề giao tiếp phi đồng bộ và decoupling (tách rời) trong ứng dụng.                                 | 14/11/2025   | 14/11/2025      | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 10:

- **Hàng đợi tin nhắn:** Hiểu rõ vai trò của SQS trong việc **decoupling** và **làm mềm tải** (buffering) ứng dụng. Thành thạo việc gửi/nhận tin nhắn.
- **Mô hình Pub/Sub:** Nắm được cơ chế **SNS** để phân phối tin nhắn tới nhiều người đăng ký (Subscriber) một cách hiệu quả.
- **Điều phối luồng công việc:** Hiểu rõ cách **Step Functions** giúp điều phối các dịch vụ AWS khác thành một quy trình làm việc có tổ chức và dễ giám sát.
- **Xây dựng Hệ thống Phi đồng bộ:** Có khả năng thiết kế kiến trúc ứng dụng sử dụng các dịch vụ tích hợp ứng dụng này để tăng tính linh hoạt và khả năng mở rộng.
