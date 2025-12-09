---
title: "Các bài blogs đã dịch"

weight: 3
chapter: false
pre: " <b> 3. </b> "
---

### [Blog 1 - Xây dựng các ứng dụng đa cụm linh hoạt với Amazon EKS, Phần 1: Triển khai cân bằng tải liên cụm với NLB](3.1-Blog1/)

Bài viết trên blog này hướng dẫn cách xây dựng ứng dụng đa cụm linh hoạt với Amazon EKS bằng cách sử dụng tính năng mới trong AWS Load Balancer Controller (LBC). Tính năng này cho phép Network Load Balancer (NLB) phân phối lưu lượng truy cập đến các mục tiêu trong nhiều cụm EKS, giúp cải thiện khả năng phục hồi và tính sẵn sàng của ứng dụng. Bài viết cung cấp một hướng dẫn chi tiết về cách thiết lập và cấu hình cân bằng tải đa cụm cho cả khối lượng công việc hiện có và triển khai mới.

### [Blog 2 - Cách tăng tốc đánh giá tìm kiếm bảo mật bằng cách sử dụng xác thực ngữ cảnh kinh doanh tự động trong AWS Security Hub CSPM](3.2-Blog2/)

Bài viết hướng dẫn cách sử dụng tính năng Tự động hóa các quy tắc với các chức năng tùy chỉnh (Automate rules with custom functions) trong AWS Security Hub CSPM để tăng tốc độ đánh giá các phát hiện bảo mật.

1.  Bằng cách viết các hàm Lambda tùy chỉnh, bạn có thể tự động thu thập và thêm ngữ cảnh kinh doanh quan trọng (như môi trường, chủ sở hữu, chức năng) vào các phát hiện. Điều này giúp:

2.  Giảm thời gian phân loại (Triage): Loại bỏ công việc thủ công để tra cứu thông tin tài sản.

Ra quyết định nhanh hơn: Cung cấp ngay lập tức thông tin cần thiết để ưu tiên và khắc phục rủi ro.

3.  Tăng hiệu quả hoạt động: Tập trung nguồn lực vào các vấn đề quan trọng nhất.

Bài viết cung cấp ví dụ cụ thể về việc thêm ngữ cảnh từ AWS Systems Manager vào phát hiện Amazon Inspector để minh họa lợi ích của phương pháp này.

### [Blog 3 - Giải pháp tìm kiếm và cơ sở dữ liệu có khả năng mở rộng, đàn hồi cho hơn 1 tỷ vectơ được xây dựng trên LanceDB và Amazon S3](3.3-Blog3/)

Bài viết mô tả cách Metagenomi, hợp tác với AWS, đã xây dựng một giải pháp tìm kiếm và cơ sở dữ liệu vector có khả năng mở rộng cao, đàn hồi và tiết kiệm chi phí để quản lý hơn 1 tỷ vector protein, nhằm phục vụ việc khám phá enzyme mới cho chỉnh sửa gen.

Các điểm chính của giải pháp:

Thách thức: Sàng lọc hiệu quả hàng tỷ chuỗi protein để tìm các ứng viên enzyme giá trị cao.

Công nghệ lõi: Sử dụng LanceDB (cơ sở dữ liệu vector nguồn mở) hoạt động trực tiếp trên nền tảng lưu trữ Amazon S3. Điều này giúp thay thế các giải pháp lưu trữ đĩa đắt đỏ bằng lưu trữ đối tượng chi phí thấp.

Kiến trúc Serverless: Sử dụng AWS Lambda và AWS Step Functions để thực hiện các truy vấn tìm kiếm theo yêu cầu. Hệ thống không cần máy chủ chạy liên tục, giúp tối ưu chi phí.

Quy trình xử lý:

Chuyển đổi chuỗi protein thành vector (sử dụng mô hình AI).

Chia nhỏ (sharding) dữ liệu khổng lồ thành các phần nhỏ để xử lý song song.

Lập chỉ mục và lưu trữ các phần dữ liệu này trên S3.

Sử dụng Lambda để tìm kiếm song song trên các phần dữ liệu và tổng hợp kết quả.

Kết quả: Hệ thống cho phép tìm kiếm vector tốc độ cao (tính bằng giây), dễ dàng mở rộng theo chiều ngang khi dữ liệu tăng và giảm đáng kể chi phí vận hành.
