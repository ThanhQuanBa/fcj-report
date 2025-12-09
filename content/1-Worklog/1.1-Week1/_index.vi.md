---
title: "Worklog Tuần 1"
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Mục tiêu tuần 1:

- Kết nối, làm quen với các thành viên trong First Cloud Journey.
- Hiểu dịch vụ AWS cơ bản, cách tạo và quản lý chi phi với tài khoản AWS.
- Cách dùng console & CLI để tương tác và quản lý các dịch vụ.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                                                                                                                                                                                                                                                                                               | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | - Làm quen với các thành viên FCJ <br> - Đọc và lưu ý các nội quy, quy định tại đơn vị thực tập                                                                                                                                                                                                                                                                                         | 08/09/2025   | 08/09/2025      |
| 3   | - Tìm hiểu AWS và các loại dịch vụ cơ bản <br>&emsp; + Compute (EC2) <br>&emsp; + Storage (S3) <br>&emsp; + Networking (VPC) <br>&emsp; + Database (RDS) <br>                                                                                                                                                                                                                           | 09/09/2025   | 09/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tạo AWS Free Tier account <br> - Tìm hiểu AWS Console & AWS CLI <br> - **Thực hành:** <br>&emsp; + Tạo AWS account <br>&emsp; + Quản lý danh tính và quyền <br>&emsp;&nbsp;&nbsp;&nbsp; truy cập <br>&emsp; + Cài AWS CLI & cấu hình <br> &emsp; + Sử dụng AWS CLI với các thao tác cơ bản                                                                                            | 10/09/2025   | 10/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Tìm hiểu cách quản lý chi phí hiệu quả với AWS budget <br>&emsp; + Budget <br>&emsp; + Cost Budget, <br>&emsp; + Usage Budget <br>&emsp; + Reservation (RI) Budget <br>&emsp; + Saving plans Budget <br> - **Thực hành:** <br>&emsp; + Tạo Cost Budget <br>&emsp; + Tạo Usage Budget <br>&emsp; + Tạo RI Budget <br>&emsp; + Tạo Savings Plans Budget <br>&emsp; + Dọn Dẹp Tài Nguyên | 11/09/2025   | 11/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Tìm hiểu về dịch vụ AWS Support <br> - Các gói hỗ trợ của AWS <br>&emsp; + Gói Basic, Developer, Business và Enterprise <br> - Các loại yêu cầu hỗ trợ <br>&emsp; + Hỗ trợ Tài khoản và Thanh toán <br>&emsp; + Hỗ trợ nâng hạn mức dịch vụ <br>&emsp; + Hỗ trợ Kỹ thuật <br> - **Thực hành:** <br>&emsp; + Chọn gói hỗ trợ Basic <br>&emsp; + Tạo yêu cầu hỗ trợ                     | 12/09/2025   | 12/09/2025      | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 1:

- Hiểu AWS là gì và nắm được các nhóm dịch vụ cơ bản:

  - Compute: Cung cấp tài nguyên xử lý cho ứng dụng như máy ảo, container,...
  - Storage: Dùng để lưu trữ dữ liệu, sao lưu và phục hồi
  - Networking: Quản lý hạ tầng mạng, bảo mật, và kết nối giữa các tài nguyên AWS.
  - Database: Cung cấp dịch vụ quản lý cơ sở dữ liệu quan hệ và phi quan hệ.

- Đã tạo cấu hình và định danh AWS Free Tier account thành công.
- Đã biết tạo và quản lý Group user, User.
- Biết cách đăng nhập bằng IAM và các user trong cùng một group sẽ được dùng chung quyền được cấp.

- Làm quen với AWS Management Console và biết cách tìm, truy cập, sử dụng dịch vụ từ giao diện web.

- Cài đặt và cấu hình AWS CLI trên máy tính bao gồm:

  - Access Key
  - Secret Key
  - Region mặc định

- Sử dụng AWS CLI để thực hiện các thao tác cơ bản như:

  - Kiểm tra thông tin tài khoản & cấu hình
  - Lấy danh sách region
  - Tạo và xóa S3 Bucket
  - Sử dụng SNS amazon
  - Tạo IAM group, user và thêm user
  - Tạo và xóa acess key
  - Tạo và cấu hình cơ bản VPS
  - Chạy và chấm dứt EC2

- Nắm được cách quản lý và giám sát chi phí trên AWS thông qua các công cụ:
  - Tạo và cấu hình các gói Budget (Cost, Usage, RI, Savings Plan).
  - Biết cách dọn dẹp tài nguyên để quản lý chi phí hiệu quả.
- Hiểu về các gói hỗ trợ của AWS và biết cách tạo yêu cầu hỗ trợ từ trung tâm hỗ trợ.
  - Basic: Miễn phí, hỗ trợ các vấn đề liên quan đến tài khoản và thanh toán từ trung tâm trợ giúp
  - Developer: 29 USD/tháng, tư vấn kiến trúc cơ bản, và hỗ trợ kỹ thuật không giới hạn được tạo từ tài khoản gốc (root user)
  - Business:100 USD/tháng, lựa chọn phổ biến cho các doanh nghiệp vừa và nhỏ với các hỗ trợ như: Chỉ dẫn theo Use-case cụ thể, Hỗ trợ sử dụng AWS Support API, không giới hạn các yêu cầu hỗ trợ được tạo bởi tất cả các IAM User,...
- Enterprise: 15.000 USD/tháng, cho doanh nghiệp quy mô lớn được đảm bảo các tiêu chí bảo mẩ tiêu chuẩn và nghiêm ngặt nhất với các dịch vụ bảo mật như: về kiến trúc phần mềm, hạ tầng, hỗ trợ toàn diện về chiến lược và tối ưu chi phí, được ưu tiên chăm sóc đặc biệt các yêu cấu hỗ trợ,...
- Làm quen với giao diện AWS Console và sử dụng tốt các thao tác cơ bản qua cả Console và CLI.
