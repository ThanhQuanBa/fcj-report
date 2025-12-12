---
title: "Giới thiệu"
date: "2025-11-11"
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

# Xây dựng AWS VPC Cơ bản với Subnet Công khai & Riêng tư

Trong bài thực hành này, bạn sẽ xây dựng một nền tảng mạng đơn giản trên AWS sử dụng **Amazon VPC (Virtual Private Cloud)**.
Mục tiêu là giúp người mới bắt đầu hiểu cách hoạt động của mạng trong đám mây, và cách các thành phần khác nhau kết nối
với nhau để tạo thành một môi trường an toàn, được kiểm soát.

Bạn sẽ tạo một VPC với hai subnet (mạng con):

- Một **Public Subnet** (Subnet Công khai) – cho phép các tài nguyên (như EC2 instance) truy cập Internet trực tiếp.
- Một **Private Subnet** (Subnet Riêng tư) – được cách ly khỏi Internet công cộng theo mặc định để bảo mật tốt hơn.

Để cho phép private subnet tải xuống các gói phần mềm và truy cập dịch vụ trực tuyến mà không bị lộ ra ngoài công cộng,
bạn sẽ cấu hình:

- Một **Internet Gateway (IGW)** – cho phép lưu lượng truy cập của public subnet đi đến/từ Internet.
- Một **NAT Gateway** – cho phép các instance trong private subnet truy cập Internet _một cách gián tiếp_, mà không cần
  gán địa chỉ IP công cộng.

Đến cuối bài, bạn sẽ khởi chạy hai EC2 instance — mỗi cái nằm trong một subnet — và kiểm tra kết nối giữa chúng, đồng thời
quan sát vai trò của định tuyến (routing) và các gateway.

Bài thực hành này giảng dạy các kiến thức cơ bản thiết yếu về mạng AWS thông qua các bước hướng dẫn và điều hướng trên console.
Rất phù hợp cho người mới bắt đầu muốn xây dựng sự tự tin khi làm việc với VPC.
