---
title: "Worklog Tuần 8"
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu tuần 8:

- Hiểu rõ kiến trúc, ưu điểm và các thành phần cốt lõi của dịch vụ điện toán phi máy chủ **AWS Lambda**.
- Thành thạo việc tạo, triển khai và quản lý các hàm Lambda.
- Nắm vững vai trò và cách cấu hình **Amazon API Gateway** để tạo các API RESTful.
- Thực hành xây dựng một ứng dụng Serverless đơn giản bằng cách tích hợp Lambda và API Gateway.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                                                                                                                                                                                                             | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| :-- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------- | :-------------- | :---------------------------------------- |
| 2   | - Đọc và hiểu kiến trúc **AWS Lambda** (Function, Runtime, Handler, Execution Role). <br> - So sánh Serverless (Lambda) với Compute (EC2). <br> - **Thực hành:** Tạo và triển khai một hàm Lambda đơn giản (ví dụ: in ra "Hello World").                                                              | 27/10/2025   | 27/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Tìm hiểu về cơ chế **Event-Driven** trong Lambda. <br> - **Thực hành:** <br>&emsp; + Cấu hình Trigger để Lambda được kích hoạt bởi sự kiện từ S3 (ví dụ: upload file). <br>&emsp; + Cấu hình Lambda để đọc/ghi dữ liệu từ DynamoDB (sử dụng IAM Role).                                              | 28/10/2025   | 28/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tìm hiểu về dịch vụ **Amazon API Gateway** (REST API, Stage, Resource, Method). <br> - **Thực hành:** Tạo một REST API mới trên API Gateway. <br> - Định nghĩa các Resource và Method cơ bản (ví dụ: GET /items, POST /items).                                                                      | 29/10/2025   | 29/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Tìm hiểu về **Integration Type** (Lambda Proxy Integration) giữa API Gateway và Lambda. <br> - **Thực hành:** <br>&emsp; + Kết nối Method POST /items của API Gateway với hàm Lambda đã tạo ở ngày 2. <br>&emsp; + Deploy API lên một Stage (ví dụ: `prod`) và kiểm tra qua URL công cộng.          | 30/10/2025   | 30/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Bảo mật, Giám sát & Dọn dẹp:** <br> - Tìm hiểu về CORS (Cross-Origin Resource Sharing) và cách kích hoạt trên API Gateway. <br> - **Thực hành:** <br>&emsp; + Kích hoạt CORS cho API. <br>&emsp; + Kiểm tra Logs của Lambda trên CloudWatch. <br>&emsp; + Xóa API Gateway, sau đó xóa hàm Lambda. | 31/10/2025   | 31/10/2025      | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 8:

- **Lambda Nền tảng:** Hiểu rõ mô hình Serverless, thành thạo việc tạo, cấu hình và quản lý hàm Lambda (Function, Role, Runtime).
- **Event-Driven:** Hiểu cách thức Lambda hoạt động theo sự kiện và đã thực hành tích hợp với các dịch vụ khác như S3 và DynamoDB.
- **API Gateway:** Nắm vững kiến trúc API Gateway (Resource, Method, Stage) và khả năng tạo các API RESTful.
- **Xây dựng Serverless:** Triển khai thành công kiến trúc Serverless cơ bản (**API Gateway -> Lambda -> DynamoDB**), một mẫu kiến trúc phổ biến trên AWS.
- **Bảo mật & Dọn dẹp:** Nắm được cách quản lý Logs Lambda qua CloudWatch và thực hiện dọn dẹp tài nguyên Serverless.
