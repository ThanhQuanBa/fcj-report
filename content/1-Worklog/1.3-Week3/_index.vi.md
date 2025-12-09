---
title: "Worklog Tuần 3"
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu tuần 3:

- Hiểu rõ vai trò và các thành phần cốt lõi của dịch vụ mạng ảo **Amazon Virtual Private Cloud (VPC)**.
- Thành thạo việc tạo, cấu hình VPC, Subnet (Public/Private), Internet Gateway (IGW) và Route Table.
- Nắm vững cơ chế bảo mật hai lớp: **Security Group (SG)** và **Network Access Control List (NACL)**.
- Thực hành triển khai một EC2 Instance vào một VPC custom.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                                                                                                                                                                                                                                                       | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| :-- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------- | :-------------- | :---------------------------------------- |
| 2   | - Đọc và hiểu kiến trúc của **VPC**: Khái niệm về IP, CIDR Block, Subnet, Availability Zone (AZ). <br> - **Thực hành:** Tạo một VPC mới với dải CIDR tùy chỉnh (ví dụ: `10.0.0.0/16`). <br> - Tạo hai Subnet: một Public và một Private.                                                                                                        | 22/09/2025   | 22/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Tìm hiểu về **Internet Gateway (IGW)**, **Route Table** và vai trò của chúng trong việc cấp truy cập Internet. <br> - **Thực hành:** <br>&emsp; + Tạo và đính kèm IGW vào VPC. <br>&emsp; + Cấu hình **Route Table Public** để định tuyến traffic ra IGW. <br>&emsp; + Liên kết Route Table Public với Public Subnet.                         | 23/09/2025   | 23/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tìm hiểu về cơ chế bảo mật **Security Group (SG)** (Stateful) và nguyên tắc hoạt động. <br> - **Thực hành:** <br>&emsp; + Khởi tạo một EC2 Instance trong Public Subnet. <br>&emsp; + Cấu hình SG chỉ cho phép SSH/RDP (Port 22/3389) từ IP cá nhân. <br>&emsp; + Thử truy cập để kiểm tra tính năng bảo mật của SG.                          | 24/09/2025   | 24/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Tìm hiểu về cơ chế bảo mật **Network Access Control List (NACL)** (Stateless). <br> - **Thực hành:** <br>&emsp; + Cấu hình NACL cho Public Subnet (thử nghiệm tạo rule Deny). <br>&emsp; + Phân biệt chi tiết sự khác nhau khi xử lý inbound/outbound giữa SG và NACL. <br>&emsp; + Tìm hiểu khái niệm về NAT Gateway (trong Private Subnet). | 25/09/2025   | 25/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Dọn dẹp & Ôn tập tổng quát:** <br> - **Thực hành:** <br>&emsp; + Chấm dứt (Terminate) EC2 Instance. <br>&emsp; + **Xóa toàn bộ các thành phần VPC** đã tạo theo đúng thứ tự (Detach IGW -> Xóa Subnets -> Xóa Route Tables -> Xóa VPC) để dọn dẹp chi phí. <br>&emsp; + Tổng kết các thành phần VPC đã học.                                 | 26/09/2025   | 26/09/2025      | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 3:

- **VPC Cấu hình:** Đã hiểu và tự tay tạo được VPC, Subnet (Public/Private), Internet Gateway, và Route Table để định tuyến lưu lượng truy cập.
- **Bảo mật Lớp Mạng:** Nắm vững và phân biệt được cơ chế bảo mật hai lớp: **Security Group** (Stateful, hoạt động ở cấp Instance) và **NACL** (Stateless, hoạt động ở cấp Subnet).
- **Triển khai Cơ bản:** Đã triển khai thành công EC2 Instance trong VPC tự tạo và xác nhận khả năng truy cập Internet.
- **Kỹ năng Dọn dẹp:** Thực hiện thành công việc dọn dẹp các tài nguyên mạng theo đúng thứ tự (đảm bảo không để lại tài nguyên không cần thiết), góp phần quản lý chi phí hiệu quả.
