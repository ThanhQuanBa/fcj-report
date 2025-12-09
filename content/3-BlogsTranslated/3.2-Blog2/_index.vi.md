---
title: "Blog 2"
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# **Cách tăng tốc đánh giá tìm kiếm bảo mật bằng cách sử dụng xác thực ngữ cảnh nghiệp vụ tự động trong AWS Security Hub CSPM**

bởi Reetesh Surjani và Satish Kamat vào ngày 22 tháng 9 năm 2025 trong **[Nâng cao (300)](https://aws.amazon.com/blogs/security/category/learning-levels/advanced-300/), [AWS Security Hub](https://aws.amazon.com/blogs/security/category/security-identity-compliance/aws-security-hub/), [Bảo mật, Nhận dạng và Tuân thủ](https://aws.amazon.com/blogs/security/category/security-identity-compliance/), [Kỹ thuật [Hướng dẫn](https://aws.amazon.com/blogs/security/category/security-identity-compliance/) , [Hướng dẫn kỹ thuật](https://aws.amazon.com/blogs/security/category/security-identity-compliance/) kỹ thuật](https://aws.amazon.com/blogs/security/category/post-types/technical-how-to/) [Liên kết cố định](https://aws.amazon.com/blogs/security/how-to-accelerate-security-finding-reviews-using-automated-business-context-validation-in-aws-security-hub/) [Bình luận] đánh giá](https://aws.amazon.com/blogs/security/how-to-accelerate-security-finding-reviews-using-automated-business-context-validation-in-aws-security-hub/#Comments) [Chia sẻ share](https://aws.amazon.com/vi/blogs/security/how-to-accelerate-security-finding-reviews-using-automated-business-context-validation-in-aws-security-hub/#)**

**Ngày 1 tháng 10 năm 2025:** Bài viết này đã được cập nhật để phản ánh tên mới của Security Hub, AWS Security Hub CSPM (Quản lý Tư thế Bảo mật Đám mây).

---

Các nhóm bảo mật phải xác thực và ghi lại hiệu quả các ngoại lệ đối với các phát hiện của [**AWS Security Hub (Quản lý Tư thế Bảo mật Đám mây, trước đây gọi là Security Hub)**](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-control-manage-findings.html)**,** và duy trì quản trị phù hợp. Các nhóm bảo mật doanh nghiệp cần đảm bảo rằng các ngoại lệ đối với các biện pháp bảo mật tốt nhất được xác thực và ghi lại đúng cách, trong khi các nhóm phát triển cần một quy trình hợp lý để triển khai và xác minh các biện pháp kiểm soát bù trừ.

Trong bài đăng trên blog này, chúng tôi giới thiệu một giải pháp tự động lý tưởng cho các tổ chức sử dụng [**AWS Security Hub CSPM**](https://aws.amazon.com/security-hub/) cần quản lý các ngoại lệ bảo mật ở quy mô lớn trong khi vẫn duy trì các biện pháp kiểm soát hành chính. Giải pháp này đặc biệt hữu ích cho các doanh nghiệp có yêu cầu tuân thủ phức tạp và nhiều nhóm phát triển. Bằng cách triển khai giải pháp này, bạn có thể đẩy nhanh việc xem xét các phát hiện CSPM của Security Hub trong khi vẫn duy trì quản trị bảo mật phù hợp và cung cấp bối cảnh kinh doanh rõ ràng cho các ngoại lệ bảo mật.

**Lưu ý:** Giải pháp trong bài viết này được cung cấp dưới dạng kiến ​​trúc tham chiếu và không nên được triển khai nguyên trạng trong môi trường sản xuất. Các tổ chức nên xem xét, tùy chỉnh và cải tiến giải pháp này một cách cẩn thận để phù hợp với các yêu cầu bảo mật, khuôn khổ tuân thủ, chính sách quản trị và khả năng chịu rủi ro cụ thể của mình. Vui lòng làm việc với các nhóm bảo mật, tuân thủ và pháp lý của bạn trước khi triển khai giải pháp xác thực bảo mật tự động này.

## **Thách thức**

Security Hub CSPM cung cấp **[cái nhìn toàn diện về tình trạng bảo mật AWS của bạn trên các tài khoản AWS](https://docs.aws.amazon.com/securityhub/latest/userguide/dashboard.html) .** Tuy nhiên, trong các tình huống thực tế, bạn sẽ gặp phải những lý do kinh doanh hợp lệ dẫn đến các ngoại lệ đối với các biện pháp bảo mật tốt nhất. Ví dụ:

- Không bật Amazon GuardDuty: Do có giải pháp giám sát thay thế, một tổ chức đã hoãn triển khai [Amazon GuardDuty](https://aws.amazon.com/guardduty/) nhưng yêu cầu các biện pháp kiểm soát bù trừ như **[Nhật ký luồng Amazon Virtual Private Cloud (VPC)](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html), [Cảnh báo Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)** và quy trình ứng phó sự cố dành riêng cho tổ chức.

- Không cho phép truy cập công khai vào Amazon S3: Các nhóm tiếp thị có thể cần một thùng **[Amazon Simple Storage Service (Amazon S3)](https://aws.amazon.com/s3/)** công khai cho các tài sản trang web, nhưng nên triển khai các biện pháp kiểm soát bù trừ sau:

- [**Amazon CloudFront Distribution**](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-working-with.html) **trước [Amazon S3](https://aws.amazon.com/s3/)**

- Mã hóa phía máy chủ với **[khóa AWS KMS (SSE-KMS) được bật](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingKMSEncryption.html)** trên thùng S3

- Bật **[ghi nhật ký thùng Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/enable-server-access-logging.html)**

- Bật [**quản lý phiên bản thùng Amazon S3**](https://docs.aws.amazon.com/AmazonS3/latest/userguide/manage-versioning-examples.html)

- [**cảnh báo Amazon CloudWatch**](https://aws.amazon.com/cloudwatch) để phát hiện các mẫu truy cập đáng ngờ và ghi nhật ký truy cập toàn diện

Việc quản lý các ngoại lệ đối với các biện pháp bảo mật tốt nhất có thể khó khăn vàThường bao gồm nhiều bước. Các nhóm bảo mật cần rất nhiều thời gian để xem xét các yêu cầu ngoại lệ, xác định và xác thực các biện pháp kiểm soát bù trừ, sau đó các nhà phát triển phải triển khai và xác thực các biện pháp kiểm soát đó. Nhiều nhóm phải được huy động để tạo và quản lý tài liệu cho mục đích tuân thủ và kiểm toán. Nhìn chung, nếu được thực hiện thủ công, quy trình này tốn thời gian, dễ xảy ra lỗi (với nguy cơ bỏ sót các vấn đề triển khai) và có nguy cơ kém khả năng hiển thị do tài liệu về bối cảnh kinh doanh của các phát hiện bảo mật bị hạn chế hoặc thiếu.

## **Điều kiện tiên quyết để giải quyết vấn đề**

Để giải quyết vấn đề này, bạn phải có những điều kiện sau:

- **Tài khoản AWS có hạn ngạch dịch vụ phù hợp cho [Amazon DynamoDB](https://aws.amazon.com/dynamodb), [AWS Lambda](https://aws.amazon.com/lambda) và [Amazon Simple Queue Service (Amazon SQS)](https://aws.amazon.com/sqs)**

- **Quyền [AWS Identity and Access Management (IAM)](https://aws.amazon.com/iam) cần thiết để triển khai nhiều tài nguyên AWS khác nhau, bao gồm:**

- **Quyền IAM create-role và IAM put-role-policy (để tạo vai trò nhóm bảo mật và vai trò nhà phát triển)**

- [**AWS Stack Management CloudFormation**](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/aws-resource-cloudformation-stack.html)

- **Tạo và quản lý [các bảng DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithTables.html)**

- [**Amazon SQS**](https://aws.amazon.com/sqs/)

- [**Ánh xạ nguồn sự kiện AWS Lambda với Amazon SQS**](https://docs.aws.amazon.com/lambda/latest/dg/with-sqs.html)

- [**Amazon Policy SQS**](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-using-identity-based-policies.html)

- **Triển khai và cấu hình [các hàm Lambda](https://aws.amazon.com/lambda/)**

- [**Vai trò thực thi Lambda**](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html)

- **Cấu hình [các quy tắc Amazon EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rules.html)**

- [**Các thao tác trên thùng Amazon S3**](https://aws.amazon.com/s3/) **cho các hiện vật triển khai**

- [**Giao diện dòng lệnh AWS (AWS CLI)**](https://aws.amazon.com/cli/) **phiên bản 2.17.44 trở lên**

- [**Python**](https://www.python.org/downloads/release/python-3120/) **phiên bản 3.12 trở lên**

- [**Tiện ích Xử lý JSON jq**](https://jqlang.org/download/) **cho các hoạt động viết kịch bản**

- [**Security Hub CSPM**](https://aws.amazon.com/security-hub/) **đã bật trong Vùng AWS mục tiêu của bạn**

**aws securityhub enable-security-hub**

- [**AWS Config**](https://aws.amazon.com/config/) **khuyến nghị cho xác thực nâng cao**

**aws configservice put\-configuration\-recorder \\ \--configuration\-recorder name\=default,roleARN\=arn:aws:iam::ACCOUNT_ID:role/aws\-service\-role/config.amazonaws.com/AWSServiceRoleForConfig**

### **Xác thực Tự động**

Giải pháp bao gồm một kịch bản xác thực trước khi triển khai **( [validate-environment.sh](https://github.com/aws-samples/sample-automated-securityhub-validator/blob/main/scripts/validate-environment.sh) )** tự động xác minh các thông tin sau:

- Phiên bản và cài đặt công cụ

- Trạng thái kích hoạt dịch vụ AWS

- Xung đột tài nguyên

Xác thực này tự động chạy trong quá trình triển khai (Được tích hợp trong [deploy.sh](https://github.com/aws-samples/sample-automated-securityhub-validator/blob/main/scripts/deploy.sh) ) để giúp đảm bảo các điều kiện tiên quyết bắt buộc được đáp ứng trước khi bắt đầu tạo cơ sở hạ tầng.

### **Tài nguyên bổ sung**

Xem **[Hướng dẫn ước tính chi phí](https://github.com/aws-samples/sample-automated-securityhub-validator/blob/main/docs/guides/cost-estimation.md)** để biết bảng giá chi tiết cho các điều kiện tiên quyết và [**Hướng dẫn khắc phục sự cố**](https://github.com/aws-samples/sample-automated-securityhub-validator/blob/main/docs/guides/troubleshooting.md) để biết các sự cố thiết lập thường gặp và giải pháp.

## **Tổng quan về giải pháp**

Giải pháp này cung cấp mã mẫu và các mẫu CloudFormation mà các tổ chức có thể triển khai để tự động xác thực các biện pháp kiểm soát bù trừ cho các phát hiện CSPM của Security Hub bị che giấu, đồng thời duy trì sự phân tách nhiệm vụ phù hợp giữa các nhóm bảo mật và phát triển.

### **Kiến trúc**

**![Hình 1: Sơ đồ Kiến trúc Giải pháp]![văn bản thay thế](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-1-23.png)**

**_Hình 1: Sơ đồ Kiến trúc Giải pháp_**

Hình 1 minh họa quy trình giải pháp được khởi tạo khi nhà phát triển thay đổi trạng thái quy trình của kết quả CSPM của Security Hub thành **ĐÃ BỊ ỨC CHẾ**, yêu cầu một ngoại lệ bảo mật phù hợp với doanh nghiệp. Quy trình kết thúc bằng việc giải pháp thêm kết quả xác thực dưới dạng ghi chú vào kết quả CSPM của Security Hub tương ứng và duy trì nhật ký kiểm tra đầy đủ về yêu cầu ngoại lệ và kết quả xác thực.

**Lưu ý _:_** Trước khi bắt đầu quy trình làm việc này, nhà phát triển phải tham khảo ý kiến ​​của nhóm bảo mật trong tổ chức để giải thích tỷ lệ kinh doanhale cho ngoại lệ này. Trong quá trình tham vấn ban đầu này, nhóm bảo mật xác định các biện pháp kiểm soát bù trừ cần thiết cho loại phát hiện. Nhóm bảo mật sử dụng tập lệnh **add-controls-role-based.sh** để thêm các biện pháp kiểm soát vào DynamoDB. Nhà phát triển kích hoạt các biện pháp kiểm soát bù trừ cần thiết trước khi thay đổi trạng thái quy trình làm việc.

Quy trình làm việc được hiển thị trong Hình 1 bao gồm các bước sau:

1. Nhà phát triển thay đổi trạng thái tìm kiếm CSPM của Security Hub thành **SUPRESSED.**

2. EventBridge phát hiện thay đổi thành **SUPRESSED.**

3. Quy tắc EventBridge gửi sự kiện đến hàng đợi Amazon SQS.

4. Hàm Lambda truy xuất các thông báo từ hàng đợi Amazon SQS.

5. Hàm Lambda truy xuất các biện pháp kiểm soát bù trừ từ bảng điều khiển bù trừ của DynamoDB.

6. Hàm Lambda xác thực từng biện pháp kiểm soát bằng API dịch vụ AWS phù hợp.

7. Bằng chứng được thu thập cho mỗi lần xác thực và được lưu trữ trong DynamoDB.

8. Kết quả xác thực phát hiện và dấu thời gian được lưu trữ trong bảng DynamoDB **Phát hiện**

9. Lịch sử phiên bản của các lần xác thực được lưu trữ trong bảng **Lịch sử** DynamoDB.

10. Nếu các biện pháp kiểm soát do nhóm bảo mật cung cấp vượt qua xác thực, kết quả tìm kiếm vẫn ở trạng thái **BỊ ỨC CHẾ** và một ghi chú sẽ được thêm vào kết quả tìm kiếm Security Hub tương ứng với thông tin mức độ nghiêm trọng đã điều chỉnh (mức độ nghiêm trọng ban đầu do Security Hub chỉ định không bị thay đổi bởi giải pháp này). Nếu một trong các biện pháp kiểm soát này không vượt qua **xác thực, trạng thái tìm kiếm sẽ được chuyển thành ĐÃ THÔNG BÁO và một ghi chú sẽ được thêm vào kết quả tìm kiếm Security Hub cho các biện pháp kiểm soát không thành công (mức độ nghiêm trọng ban đầu do Security Hub CSPM chỉ định không bị thay đổi bởi giải pháp này).**

11. TÙY CHỌN: Mở rộng giải pháp với [**Amazon OpenSearch**](https://aws.amazon.com/opensearch-service/) để các nhóm SOC thực hiện tìm kiếm nâng cao, đối chiếu và trực quan hóa bằng chứng xác thực về các phát hiện, cũng như phân tích xu hướng lịch sử về hiệu quả của các biện pháp kiểm soát bù trừ. Sử dụng **[Amazon QuickSight](https://aws.amazon.com/quicksight/)** để trực quan hóa các chỉ số tuân thủ và **[AWS Security Lake](http://aws.amazon.com/security-lake/)** để tập trung dữ liệu xác thực trên nhiều tài khoản và khu vực, chuẩn hóa dữ liệu theo định dạng OCSF để phân tích toàn diện giữa các tài khoản và báo cáo tuân thủ dài hạn.

**Lưu ý:** Giải pháp này phải được triển khai theo chính sách bảo mật của tổ chức bạn và [**Mô hình Trách nhiệm Chia sẻ AWS**](https://aws.amazon.com/compliance/shared-responsibility-model/)**.** Vui lòng xem xét và kiểm tra các biện pháp kiểm soát bảo mật trước khi triển khai trên môi trường sản xuất.

### **Cách thức hoạt động**

Giải pháp này được thiết kế đặc biệt để các nhóm bảo mật của tổ chức triển khai và quản lý. Chỉ các nhóm bảo mật mới có quyền triển khai ngăn xếp **[AWS CloudFormation](https://aws.amazon.com/cloudformation),** sửa đổi mã xác thực Lambda, thêm/sửa đổi các điều khiển bù trừ hoặc truy cập bốn bảng DynamoDB (Điều khiển, Phát hiện, Lịch sử, Bằng chứng).

Các nhà phát triển bị giới hạn ở hai hành động cụ thể: ngăn chặn phát hiện CSPM của Security Hub và đọc các yêu cầu điều khiển bù trừ. Sự phân tách vai trò nghiêm ngặt này tạo điều kiện thuận lợi cho việc quản trị đúng đắn và giúp ngăn chặn việc bỏ qua logic xác thực bảo mật. Các tổ chức phải triển khai các chính sách IAM phù hợp để thực thi các hạn chế truy cập này trong môi trường sản xuất.

Giải pháp hoạt động như sau:

1. Nhóm bảo mật xác định các điều khiển: Nhóm bảo mật thiết lập các điều khiển bù trừ cho các loại phát hiện Security Hub cụ thể và lưu trữ chúng trong một bảng DynamoDB. Điều này giúp đảm bảo các ngoại lệ đã được phê duyệt tuân thủ các hướng dẫn bảo mật đã được phê duyệt và duy trì các tiêu chuẩn tuân thủ.

- Các tệp quan trọng dành cho nhóm bảo mật:

| Tài liệu                              | Mục đích                                        |
| :------------------------------------ | :---------------------------------------------- |
| add-controls-role-based.sh            | Tập lệnh tiện ích để thêm các điều khiển bù trừ |
| /templates/findings/\*.json           | Ví dụ về kiểm soát bù trừ để tham khảo          |
| /docs/guides/compensating-controls.md | Hướng dẫn định nghĩa kiểm soát                  |

- Các loại xác thực được hỗ trợ: Giải pháp hỗ trợ 13 phương thức xác thực để đáp ứng các yêu cầu bảo mật đa dạng:

| Loại xác thực            | Mô tả                                                | Ví dụ sử dụng                                                                                                                                             |
| ------------------------ | ---------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **CONFIG_RULE**          | Xác thực bằng **Quy tắc cấu hình AWS**               | Đối với **GuardDuty chưa được bật**, hãy tìm: `vpc-flow-logs-enabled` — quy tắc đảm bảo lưu lượng mạng được giám sát                                      |
| **API_CALL**             | Xác thực chúng tôiing trực tiếp **lệnh gọi API AWS** | Kiểm tra quyền truy cập công khai của **Amazon S3**: Lệnh gọi API xác minh **phân phối CloudFront** tồn tại trước **thùng S3**                            |
| **SECURITY_HUB_CONTROL** | Xác thực bằng trạng thái **Kiểm soát Security Hub**  | Đối với **GuardDuty chưa được bật**, hãy tìm: `CloudTrail.1` — đảm bảo ghi nhật ký API toàn diện                                                          |
| **CLOUDWATCH**           | Xác thực bằng **cảnh báo CloudWatch**                | Đối với **GuardDuty chưa được bật**, hãy tạo **cảnh báo** để theo dõi các lệnh gọi API đáng ngờ và lưu lượng mạng bất thường                              |
| **CLOUDTRAIL**           | Xác thực cấu hình **AWS CloudTrail**                 | Khi **GuardDuty chưa được bật**, hãy đảm bảo **CloudTrail đa vùng** có xác thực nhật ký và tích hợp **CloudWatch**                                        |
| **SYSTEMS_MANAGER**      | Xác thực bằng **Tham số AWS Systems Manager**        | Khi **GuardDuty chưa được bật**, hãy kiểm tra tham số khẳng định **giải pháp phát hiện mối đe dọa tùy chỉnh** đã được bật                                 |
| **PROCESS_CONTROL**      | Xác thực các điều khiển dựa trên **quy trình**       | Khi **GuardDuty không được bật**, hãy xác minh **quy trình phản hồi sự cố** đã được ghi lại cho các sự kiện bảo mật mạng                                  |
| **INSPECTOR**            | Xác thực cấu hình **Amazon Inspector**               | Kiểm tra lỗ hổng: **Quét EC2 của Inspector** được bật và không phát hiện thấy sự cố nghiêm trọng nào                                                      |
| **ACCESS_ANALYZER**      | Xác thực **AWS IAM Access Analyzer**                 | Đảm bảo **Access Analyzer** được bật và không cho phép **phát hiện hoạt động** nào                                                                        |
| **MACIE**                | Xác thực cấu hình **Amazon Macie**                   | Đảm bảo **Macie** được bật, phát hiện dữ liệu nhạy cảm và không bỏ sót nhóm dữ liệu nhạy cảm nào                                                          |
| **AUDIT_MANAGER**        | Xác thực **khung AWS Audit Manager**                 | Đảm bảo **khung bảo mật tùy chỉnh** đang hoạt động và bao gồm tất cả **điều khiển bắt buộc**                                                              |
| **EVENTBRIDGE**          | Xác thực các quy tắc **Amazon EventBridge**          | Khi **GuardDuty chưa được bật**, hãy xác thực **quy tắc EventBridge** theo dõi các sự kiện **AWS CloudTrail** với **mục tiêu Lambda** để phản hồi tự động |
| **TRUSTED_ADVISOR**      | Xác thực **kiểm tra AWS Trusted Advisor**            | Kiểm tra **quyền của bucket S3** — phải vượt qua mà không có **cảnh báo** hoặc **lỗi**                                                                    |

Lưu ý: Chỉ các thành viên nhóm bảo mật mới có quyền thêm hoặc sửa đổi các điều khiển bù trừ. Giải pháp thực thi quyền này thông qua các quyền IAM và kiểm tra thời gian chạy để duy trì quản trị phù hợp.
Các ngoại lệ bảo mật đã được phê duyệt phải có ngày hết hạn để tạo điều kiện cho việc xem xét định kỳ. Giải pháp tự động áp dụng các giới hạn thời gian này dựa trên ngày hết hạn do nhóm bảo mật xác định.
Trong bài viết này, chúng tôi cung cấp một tập lệnh tiện ích ( [add-controls-role-based.sh](https://github.com/aws-samples/sample-automated-securityhub-validator/blob/main/scripts/add-controls-role-based.sh) ) để minh họa việc thêm các điều khiển bù trừ. Tuy nhiên, trong môi trường doanh nghiệp sản xuất, các tổ chức nên tích hợp DynamoDB với các hệ thống quản trị hiện có (như Jira, ServiceNow, v.v.) để tự động điền các điều khiển từ các nguồn được nhóm bảo mật ủy quyền. Giải pháp này tập trung vào việc xác thực các điều khiển, chứ không phải quy định cách thức tiếp nhận chúng.

2\. Nhà phát triển triển khai các điều khiển: Khi phát hiện CSPM của Security Hub bị chặn, nhà phát triển phải triển khai các điều khiển bù trừ bắt buộc do nhóm bảo mật xác định.

Cách nhà phát triển tương tác với giải pháp:

1. Xem các điều khiển bắt buộc: Giải pháp cung cấp các yêu cầu rõ ràng cho từng loại tìm kiếm.

2. Triển khai các điều khiển bù trừ: Nhà phát triển nên triển khai các điều khiển bù trừ do nhóm bảo mật cung cấp trong môi trường AWS của họ, tham chiếu đến các điều khiển bù trừ do nhóm bảo mật xác định. Các điều khiển bù trừ cụ thể phụ thuộc vào loại phát hiện và yêu cầu của nhóm bảo mật.

3. Tìm kiếm các thay đổi trạng thái: Nhà phát triển thay đổi trạng thái tìm kiếm CSPM của Security Hub thành **ĐÃ BỊ BỊ CHẾ** trong Security Hub.

4. Xác thực Tự động: Giải pháp xác thực các biện pháp kiểm soát bù trừ khi trạng thái quy trình làm việc của CSPM Security thay đổi.

5. Cập nhật trạng thái: Kết quả vẫn là **ĐÃ BỊ BỎ QUA** nếu biện pháp kiểm soát vượt qua xác thực; kết quả sẽ chuyển thành **ĐÃ THÔNG BÁO** kèm theo chi tiết lỗi nếu xác thực không thành công.

Lưu ý: Giải pháp này không sửa đổi mức độ nghiêm trọng ban đầu của các phát hiện trong CSPM Security Hub. Giải pháp này bổ sung ngữ cảnh nghiệp vụ với mức độ nghiêm trọng đã được bảo mật phê duyệt vào các phát hiện dựa trên xác thực biện pháp kiểm soát bù trừ đã được bảo mật phê duyệt, giúp các nhóm bảo mật đưa ra quyết định sáng suốt.

Với giải pháp này, chúng tôi mô phỏng quy trình làm việc của nhà phát triển để xử lýg Phát hiện CSPM trên Security Hub bằng cách triển khai và xác thực các biện pháp kiểm soát bù trừ. Trong quá trình sản xuất, các nhà phát triển sẽ nhận được thông báo về các phát hiện cần chú ý, triển khai các biện pháp kiểm soát cần thiết theo chỉ đạo của nhóm bảo mật và sử dụng hệ thống xác thực này để xác minh việc triển khai. Giải pháp tập trung vào khía cạnh xác thực nhưng giả định rằng các tổ chức sẽ tích hợp nó với quy trình làm việc hiện có của nhà phát triển, hệ thống ticket và quy trình tích hợp và phân phối liên tục (CI/CD) để tạo ra một quy trình liền mạch từ phát hiện lỗi đến xác minh khắc phục.

**Thu thập bằng chứng và theo dõi kiểm toán**
Giải pháp tự động thu thập bằng chứng toàn diện cho từng hoạt động xác thực. Các tính năng chính của giải pháp bao gồm:

1. Thiết kế bốn bảng: Các bảng riêng biệt cho Kiểm soát, Phát hiện, Lịch sử và Bằng chứng (được hiển thị trong Hình 2) đảm bảo tính bảo mật thông qua việc tách biệt trong khi vẫn duy trì toàn bộ dấu vết kiểm toán.

**![Hình 2: Thiết kế bốn bảng để lưu trữ các kiểm soát bù trừ, bằng chứng, phát hiện và lịch sử]![văn bản thay thế](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-2-20.png)**
**_Hình 2: Thiết kế bốn bảng để lưu trữ các kiểm soát bù trừ, bằng chứng, phát hiện và lịch sử_**

1. Bằng chứng chi tiết: Mỗi xác thực lưu trữ bằng chứng cụ thể dựa trên loại của nó—từ chi tiết tuân thủ quy tắc AWS Config đến phản hồi API và xác minh tài liệu quy trình.

2. Bản ghi bất biến: Mỗi bằng chứng bao gồm dấu thời gian, ngữ cảnh xác thực và kết quả không thể sửa đổi. sau khi thu thập (hiển thị trong Hình 3)![]![văn bản thay thế](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-3-17.png)
   **\*Hình 3: Mẫu bằng chứng được thu thập để xác thực CONFIG_RULE hiển thị trạng thái ĐẠT\*\*\***Theo dõi lịch sử: Giải pháp lưu giữ toàn bộ lịch sử của mỗi lần xác thực, cho phép các tổ chức chứng minh sự tuân thủ liên tục theo thời gian\*\*

3. Theo dõi lịch sử: Giải pháp lưu giữ toàn bộ lịch sử của mỗi lần xác thực, cho phép các tổ chức chứng minh sự tuân thủ liên tục theo thời gian

**Triển khai và Cấu hình**
Bạn có thể triển khai giải pháp bằng các tập lệnh được cung cấp.

1. Sử dụng lệnh sau để sao chép kho lưu trữ:

**git clone [https://github.com/aws-samples/sample-automated-securityhub-validator.git](https://github.com/aws-samples/sample-automated-securityhub-validator.git)**

**cd automated-securityhub-validator**

2. Sử dụng lệnh sau để kiểm tra hạn ngạch dịch vụ và tạo nhóm bảo mật và vai trò nhà phát triển:

**cd scripts**

**./create-roles-quotas-check.sh**

3. Sử dụng lệnh sau để đảm nhận vai trò nhóm bảo mật:

**aws sts assume\-role \--role\-arn arn:aws:iam:: ACCOUNT_ID:role/securityhub\-validator\-SecurityTeamRole \--role\-session\-name SecurityTeamSession**

Trong kết quả của lệnh trước, hãy lưu ý AccessKeyId, SecretAccessKey và các dấu hiệu SessionToken. Dấu thời gian trong trường Expires được đặt theo UTC và hiển thị thời điểm thông tin xác thực tạm thời của vai trò IAM hết hạn. Sau khi thông tin xác thực tạm thời hết hạn, người dùng phải tiếp tục đảm nhận vai trò.

Lưu ý: Đối với thông tin xác thực tạm thời, bạn có thể sử dụng tham số DurationSeconds để tăng thời lượng phiên tối đa cho các vai trò IAM.

4. Tạo các biến môi trường để đảm nhận vai trò nhóm bảo mật và xác minh người dùng đã đảm nhận vai trò IAM:

- Chạy các lệnh sau để thiết lập các biến môi trường đảm nhận vai trò IAM:

**export AWS_ACCESS_KEY_ID\=RoleAccessKeyID**

**export AWS_SECRET_ACCESS_KEY\=RoleSecretKey**

**export AWS_SESSION_TOKEN\=RoleSessionToken**

Lưu ý: Thay thế các giá trị ví dụ bằng các giá trị bạn đã ghi chú khi đảm nhận vai trò IAM. Đối với Windows (OS), hãy thay export bằng set.

- Chạy lệnh get-caller-identitycommand để xác minh rằng người dùng đã đảm nhận vai trò IAM:

**aws sts get-caller-identity**
**Lưu ý:** Trong kết quả của lệnh trước, hãy xác nhận rằng ARN là **arn:aws:sts::ACCOUNT_ID:assumed-role/securityhub-validator-SecurityTeamRole/SecurityTeamSession** thay vì **arn:aws:iam::ACCOUNT_ID:user/username.**

5. Sử dụng lệnh sau để triển khai giải pháp:

**cd scripts**

**./deploy.sh**

6. Bạn có thể xác minh rằng ngăn xếp đã được tạo bằng cách truy cập Bảng điều khiển Quản lý AWS cho CloudFormation và làm theo các bước sau:

7. Trong bảng điều khiển CloudFormation, chọn Stacks rồi chọn Stack details trong ngăn điều hướng.

8. Tìm và chọn ngăn xếp securityhub-validator để mở trang chi tiết của nó. Gắn nó vào.

9. Trên trang chi tiết ngăn xếp, chọn tab Tài nguyên.

10. Trong mục Tài nguyên, bạn sẽ thấy danh sách các tài nguyên là một phần của ngăn xếp.

**![Hình ảnh 4: Tài nguyên được tạo bằng ngăn xếp CloudFormation]![văn bản thay thế](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-4-14.png)**
**_Hình ảnh 4: Tài nguyên được tạo bằng ngăn xếp CloudFormation_**
**Tập lệnh triển khai tạo một ngăn xếp CloudFormation với các tài nguyên cần thiết:**

- Bảng DynamoDB cho các điều khiển, phát hiện, lịch sử và bằng chứng

- Lambdachức năng xác thực và cập nhật Security Hub

- Quy tắc EventBridge để ghi lại các thay đổi trạng thái tìm kiếm

- Hàng đợi Amazon SQS và hàng đợi thư chết (DLQ) để xử lý tin nhắn

- Vai trò IAM có đặc quyền tối thiểu

7\. Thêm các điều khiển bù trừ (nhóm bảo mật):

**cd scripts**

**./add-controls-role-based.sh**

8\. Triển khai các điều khiển (nhà phát triển).

Nhà phát triển giờ đây sẽ đảm nhận vai trò nhà phát triển và triển khai các điều khiển cần thiết dựa trên thông số kỹ thuật của nhóm bảo mật. Giải pháp sẽ tự động xác thực các triển khai này khi nhà phát triển **BỊ ỨC CHẾ** thay đổi trạng thái quy trình làm việc CSPM của Security Hub.

Để biết ví dụ về cách triển khai các biện pháp kiểm soát phổ biến, hãy xem **[ví dụ về biện pháp kiểm soát bù trừ cho phát hiện GuardDuty.1](https://github.com/aws-samples/sample-automated-securityhub-validator/blob/main/templates/findings/example-guardduty-1.json).**

**Kiểm tra giải pháp**

Để kiểm tra giải pháp, bạn có thể xác thực các biện pháp kiểm soát bù trừ cho phát hiện GuardDuty bằng cách sử dụng tình huống ví dụ sau:

Một nhà phát triển muốn có một ngoại lệ bảo mật cho Security Hub CSPM khi tìm thấy GuardDuty.1: [**GuardDuty phải được bật**](https://docs.aws.amazon.com/securityhub/latest/userguide/guardduty-controls.html#guardduty-1) và do hạn chế về chi phí, tổ chức của nhà phát triển đã không triển khai GuardDuty và đã yêu cầu nhóm bảo mật của tổ chức họ ban hành một ngoại lệ bảo mật.

Các biện pháp kiểm soát bù trừ do nhóm bảo mật cung cấp bao gồm:

- [**Nhật ký luồng Amazon Virtual Private Cloud (Amazon VPC)**](https://aws.amazon.com/vpc) phải được bật để giám sát mạng

- Cảnh báo CloudWatch được kích hoạt để theo dõi hoạt động đáng ngờ

**Lưu ý:** Để mô phỏng phát hiện này, không bật GuardDuty để phát hiện _GuardDuty enabled_ xuất hiện trong bảng điều khiển Security Hub.

Khoảng 20–30 phút sau khi bật AWS Config và Security Hub CSPM, bạn có thể tìm thấy phát hiện này trong bảng điều khiển bằng cách thực hiện các bước sau, sau đó thêm các biện pháp kiểm soát bù trừ do nhóm bảo mật cung cấp.

Trong trường hợp sử dụng này, chúng tôi sử dụng _GuardDuty để bật_ Security Hub CSPM:

1. Điều hướng đến bảng điều khiển AWS Security Hub CSPM và chọn Findings trong ngăn điều hướng.

2. Trong thanh tìm kiếm Thêm Bộ lọc ở trên cùng, chọn nhãn Mức độ nghiêm trọng và đặt giá trị thành CAO.

3. Sau khi áp dụng bộ lọc, hãy chọn GuardDuty để bật trong cột Tìm kiếm để xem chi tiết ở khung bên phải.

4. Chọn Hành động ở góc trên bên phải và chọn Xem JSON .![]![văn bản thay thế](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-5-13.png)

**_Hình 5: Phát hiện CSPM của Security Hub_**

5. Trong cửa sổ chi tiết JSON, tìm trường SecurityControlId và ghi lại giá trị. Bạn sẽ được nhắc nhập giá trị này **tiện ích add-controls-role-based.sh ở bước tiếp theo.**

**Lưu ý: SecurityControlIdGiá trị này là bắt buộc để tiện ích add-controls-role-based.sh liên kết chính xác điều khiển offset của bạn với phát hiện CSPM của Security Hub.**

**![Hình ảnh 6: SecurityControlId từ phát hiện GuardDuty]![văn bản thay thế](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-6-9.png)**

**_Hình ảnh 6: SecurityControlId từ phát hiện GuardDuty_**

1. Sử dụng lệnh sau để sao chép kho lưu trữ:

**git clone [https://github.com/aws-samples/sample-automated-securityhub-validator.git](https://github.com/aws-samples/sample-automated-securityhub-validator.git)**

**cd sample-automated-securityhub-validator**

7. Trong bản demo này, bạn sẽ là thành viên của nhóm bảo mật bằng cách đảm nhận vai trò nhóm bảo mật và sử dụng tiện ích add-controls-role-based.sh để tạo các điều khiển offset và đẩy chúng vào bảng điều khiển offset của DynamoDB.

**cd sample-automated-securityhub-validator/scripts**

**./add-controls-role-based.sh**

8. Sử dụng các giá trị nhắc sau add-controls-role-based.sh để tạo các mục nhập bảng điều khiển bù trừ bằng bốn điều khiển bù trừ do nhóm bảo mật cung cấp cho GuardDuty.1search, nhập:

**./add\-controls\-role\-based.sh**

**Tiện ích Quản lý Điều khiển Bù trừ của Nhóm Bảo mật**

**\--------------------------------------------------------------**

**THÔNG BÁO BẢO MẬT: Tiện ích này chỉ dành cho thành viên nhóm bảo mật**

**Đang xác thực vai trò nhóm bảo mật...**

**✓ Vai trò nhóm bảo mật đã được xác thực: arn:aws:sts::xxxxxxxxxxxx:assumed\-role/securityhub\-validator\-SecurityTeamRole/SecurityTeamSession**

**Sử dụng Vùng AWS: us\-east\-1**

**Sử dụng ngăn xếp: securityhub\-validator**

**Sử dụng bảng điều khiển: securityhub\-validator\-ControlsTable\-ARDQCU67CBCN**

**Nhập loại tìm kiếm (ví dụ: GuardDuty.1): GuardDuty.1**

**Mức độ rủi ro điều chỉnh đã được bảo mật phê duyệt \[CAO/TRUNG BÌNH/THẤP/THẤP/THÔNG TIN\]: TRUNG BÌNH**

**Ngày hết hạn (YYYY\-MM\-DD): 2026\-12\-31**

**Tham chiếu phiếu: JIRA\-SEC\-1234**

**Lý do kinh doanh: Giải pháp giám sát thay thế cung cấp khả năng phát hiện tương đương**

**Thêm Kiểm soát \#1**

**ID Kiểm soát: VPC\-FLOW\-LOGS**

**Mô tả kiểm soát:Nhật ký luồng VPC phải được bật để giám sát mạng**

**Loại xác thực \[CONFIG_RULE/API_CALL/SECURITY_HUB_CONTROL/INSPECTOR/ACCESS_ANALYZER/CLOUDTRAIL/MACIE/AUDIT_MANAGER/CLOUDWATCH/SYSTEMS_MANAGER/EVENTBRIDGE/TRUSTED_ADVISOR/PROCESS_CONTROL\]: CONFIG_RULE**

**Tên quy tắc cấu hình (tên chính xác): vpc\-flow\-logs\-enabled**

**Mô tả cách quy tắc này giảm thiểu phát hiện: Cung cấp khả năng hiển thị lưu lượng mạng toàn diện tương tự như khả năng giám sát mạng của GuardDuty**

**Thêm một điều khiển khác? \[y/n\]: y**

**Thêm Kiểm soát \#2**

**ID Kiểm soát: CẢNH BÁO BẢO MẬT**

**Mô tả kiểm soát: Cảnh báo CloudWatch cho hoạt động đáng ngờ**

**Loại xác thực \[CONFIG_RULE/API_CALL/SECURITY_HUB_CONTROL/INSPECTOR/ACCESS_ANALYZER/CLOUDTRAIL/MACIE/AUDIT_MANAGER/CLOUDWATCH/SYSTEMS_MANAGER/EVENTBRIDGE/TRUSTED_ADVISOR/PROCESS_CONTROL\]: CLOUDWATCH**

**Mẫu tên cảnh báo: SecurityMonitoring\-**

**Các chỉ số bắt buộc (phân tách bằng dấu phẩy): UnauthorizedAPICalls,NetworkPortProbing**

**Trạng thái cảnh báo bắt buộc \[ALARM/OK/INSUFFICIENT_DATA/ANY\]: ANY**

**Số lượng cảnh báo khớp tối thiểu cần thiết: 2**

**Mô tả cách các cảnh báo này giảm thiểu phát hiện: Cảnh báo phát hiện các lệnh gọi API đáng ngờ và hoạt động mạng tương tự như tính năng phát hiện mối đe dọa của GuardDuty**

**Thêm một biện pháp kiểm soát khác? \[y/n\]: n**

**Các điều khiển được tạo:**

**{**

**"findingType": {**

**"S": "GuardDuty.1"**

**},**

**"securityApprovedAdjustedRiskLevel": {**

**"S": "MEDIUM"**

**},**

**"expirationDate": {**

**"S": "2026-12-31T00:00:00Z"**

**},**

**"ticketReference": {**

**"S": "JIRA-SEC-1234"**

**},**

**"businessJustification": {**

**"S": "Giải pháp giám sát thay thế cung cấp khả năng phát hiện tương đương"**

**},**

**"auditInfo": {**

**"S": "{\\"createdBy\":\"arn:aws:sts::xxxxxxxxxxx:assumed-role/securityhub-validator-SecurityTeamRole/SecurityTeamSession\",\"createdAt\":\"2025-08-05T08:49:51Z\",\"la stModifiedBy\":\"arn:aws:sts::xxxxxxxxxxx:assumed-role/securityhub-validator-SecurityTeamRole/SecurityTeamSession\",\"lastModifiedAt\":\\"2025-08-05T08:49:51Z\\"}"**

**},**

**"securityControlHash": {**

**"S": "a0b33a0a96a6b282bad1c093586d89cef832d40bb379abd4a004d00afdf603d1"**

**},**

**"requiredControls": {**

**"S": "\[{\\"controlId\":\"VPC-FLOW-LOGS\",\"description\":\"Nhật ký luồng VPC phải được bật để giám sát mạng\",\"validationType\":\"CONFIG_RULE\",\"validationParams\":{\\"ruleName\":\"vpc-flow-logs-enabled\",\"justification\":\"Cung cấp khả năng hiển thị lưu lượng mạng toàn diện tương tự như khả năng giám sát mạng của GuardDuty\"}},{\\"controlId\":\"SECURITY-ALARMS\",\"description\":\"Cảnh báo CloudWatch cho các trường hợp đáng ngờ activity\",\"validationType\":\"CLOUDWATCH\",\"validationParams\":{\\"alarmNamePattern\":\"SecurityMonitoring-\",\"requiredMetrics \\":\[\"UnauthorizedAPICalls\",\"NetworkPortProbing\"\],\"requiredState\":\"ANY\",\"minimumAlarms\":2,\"justification\":\"Alarms phát hiện các lệnh gọi API đáng ngờ và hoạt động mạng tương tự như tính năng phát hiện mối đe dọa của GuardDuty\"}}\]"**

**}**

**}**

**Lưu vào DynamoDB? \[y/n\]: y**

**Các điều khiển bù trừ đã được lưu vào DynamoDB\!**

**Hành động này đã được ghi lại cho mục đích kiểm tra.**

9. Khi được nhắc lưu vào DynamoDB, hãy nhập Y. Các điều khiển bù trừ sẽ được thêm vào bảng điều khiển bù trừ của DynamoDB.

**![Hình ảnh 7: Kiểm soát Bù trừ cho Phát hiện GuardDuty.1]![văn bản thay thế](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-7-8.png)**
**_Hình ảnh 7: Kiểm soát Bù trừ cho Phát hiện GuardDuty.1_**

1. Đối với bản demo chứng minh khái niệm này, việc triển khai các biện pháp kiểm soát bù trừ yêu cầu các quyền AWS bổ sung ngoài những gì vai trò nhà phát triển cung cấp. Trong môi trường sản xuất, các biện pháp kiểm soát này thường được triển khai bởi các nhóm cơ sở hạ tầng hoặc thông qua các quy trình triển khai tự động.

- Chuyển đến thông tin quản trị.

Để minh họa, hãy tạm thời chuyển về thông tin đăng nhập quản trị AWS của bạn (thông tin được sử dụng để tạo vai trò):

Gỡ cài đặt thông tin đăng nhập vai trò nhóm bảo mật
**unset AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY AWS_SESSION_TOKEN**

- Triển khai các điều khiển cần thiết

Điều khiển 1: Bật Nhật ký luồng VPC, bắt đầu bằng cách lấy ID VPC của bạn **VPC_ID=$(aws ec2 describe-vpcs \--query 'Vpcs\[0\].VpcId' \--output text)**
Tạo nhật ký luồng:

**aws ec2 create\-flow\-logs \\**

**\--resource\-type VPC \\**

**\--resource\-ids $VPC_ID \\**

**\--traffic\-type ALL \\**

**\--log\-destination\-type cloud\-watch\-logs \\**

**\--log\-group\-name VPCFlowLogs**Tạo một AWS Config Rule:

**aws configservice put\-config\-rule \\**

**\--config\-rule '{**

**"ConfigRuleName": "vpc-flow-logs-enabled",**

**"Source": {**

**"Owner": "AWS",**

**"SourceIdentifier": "VPC_FLOW_LOGS_ENABLED"**

**}**

**}'**

Điều khiển 2: Việc tạo cảnh báo giám sát bảo mật bắt đầu bằng việc tạo bộ lọc số liệu cho Nhật ký CloudTrail; Bắt đầu bằng cách tạo một nhóm nhật ký cho CloudTrail (nếu không có nhóm nào): **aws logs create-log-group \--log-group-name CloudTrail/SecurityEvents** Tạo bộ lọc số liệu cho các lệnh gọi API trái phép:**Nhật ký aws đặt\-metric\-filter \\**

**\--log\-group\-name CloudTrail/SecurityEvents \\**

**\--filter\-name UnauthorizedAPICallsFilter \\**

**\--filter\-pattern '{ ($.errorCode \= "\*UnauthorizedOperation") || ($.errorCode \= "AccessDenied\*") }' \\**

**\--metric\-transformations metricName\=UnauthorizedAPICalls,metricNamespace\=SecurityMetrics,metricValue\=1**

Tạo bộ lọc để thăm dò các cổng mạng:

**aws logs put-metric-filter \\**

**\--log-group-name CloudTrail/SecurityEvents \\**

**\--filter-name NetworkPortProbingFilter \\**

**\--filter-pattern '\[version, account, eni, source, destination, srcport, destport="22" || destport="3389" || destport="1433", protocol, packets, bytes, windowstart, windowend, action="REJECT", flowlogstatus\]' \\**

**\--metric-transformations metricName=NetworkPortProbing,metricNamespace\=SecurityMetrics,metricValue\=1**

Tạo các cảnh báo CloudWatch bắt buộc, bắt đầu bằng Cảnh báo 1 cho các lệnh gọi API trái phép:

**aws cloudwatch put\-metric\-alarm \\**

**\--alarm\-name "SecurityMonitoring-UnauthorizedAPICalls" \\**

**\--alarm\-description "Phát hiện các lệnh gọi API trái phép" \\**

**\--metric\-name "UnauthorizedAPICalls" \\**

**\--namespace "SecurityMetrics" \\**

**\--statistic Tổng \\**

**\--period 300 \\**

**\--threshold 1 \\**

**\--toán tử so sánh GreaterThanOrEqualToThreshold \\**

**\--evaluation\-periods 1**

Báo động 2: Thăm dò cổng mạng:

**aws cloudwatch put\-metric\-alarm \\**

**\--alarm\-name "SecurityMonitoring-NetworkPortProbing" \\**

**\--alarm\-description "Phát hiện hoạt động thăm dò cổng mạng" \\**

**\--metric\-name "NetworkPortProbing" \\**

**\--namespace "SecurityMetrics" \\**

**\--statistic Tổng \\**

**\--period 300 \\**

**\--threshold 5 \\**

**\--toán tử so sánh GreaterThanOrEqualToThreshold \\**

**\--evaluation\-periods 1**

11. Bây giờ hãy giả định một DeveloperRole để ngăn chặn việc phát hiện:

**aws sts assume\-role \\**

**\--role\-arn arn:aws:iam::ACCOUNT_ID:role/securityhub\-validator\-DeveloperRole \\**

**\--role\-session\-name DeveloperSession**

**Cấu hình thông tin đăng nhập được trả về:**

**export AWS_ACCESS_KEY_ID\=\<from assume\-role output\>**

**export AWS_SECRET_ACCESS_KEY\=\<from assume\-role output\>**

**export AWS_SESSION_TOKEN\=\<from assume\-role output\>**

12. Thay đổi trạng thái quy trình làm việc của Security Hub liên quan đến GuardDuty từ NEW thành SUPRESSED.

Để thay đổi trạng thái quy trình làm việc bằng AWS CLI (nhà phát triển):

**\# Trước tiên, hãy lấy ARN tìm kiếm (lệnh được hiển thị để tham khảo)**

**aws securityhub get-findings \\**

**\--filters '{"GeneratorId":\[{"Value":"security-control/GuardDuty.1","Comparison":"EQUALS"}\]}' \\**

**\--query 'Findings\[0\].Id'**

**\# Lấy ARN sản phẩm (lệnh được hiển thị để tham khảo)**

**aws securityhub get-findings \\**

**\--filters '{"GeneratorId":\[{"Value":"security-control/GuardDuty.1","Comparison":"EQUALS"}\]}' \\**

**\--query 'Findings\[0\].ProductArn' \\**

**\--output text**

**\# Sau đó, hãy ẩn kết quả tìm kiếm**

**aws securityhub batch-update-findings \\**

**\--finding-identifiers '\[{"Id":"finding-arn-from-above","ProductArn":"product-arn-from-above"}\]' \\**

**\--workflow '{"Status":"SUPRESSED"}' \\**

**\--note '{"Text":"Implemented compensating controls as per security team requirements","UpdatedBy":"developer@example.com"}'**

Để thay đổi trạng thái quy trình làm việc bằng bảng điều khiển (nhà phát triển):

1. Truy cập bảng điều khiển CSPM của Security Hub.

2. Trong ngăn điều hướng, chọn Detections.

3. Trong thanh tìm kiếm, chọn bộ lọc Mã Kiểm soát Bảo mật Tuân thủ và nhập giá trị Is là **GuardDuty.1.**

4. Chọn mục GuardDuty cần bật và trong Trạng thái Quy trình làm việc, chọn ĐÃ TẮT.

5. Trong trường Ghi chú, nhập **Kiểm soát bù trừ đã triển khai theo yêu cầu của nhóm bảo mật.**

6. Chọn Đặt Trạng thái để lưu ghi chú.

**![Hình ảnh 8: Trạng thái quy trình tìm kiếm GuardDuty.1 đã thay đổi từ MỚI thành ĐÃ NGĂN CHẶN]![văn bản thay thế](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-8-8.png)**

**_Hình ảnh 8: Trạng thái quy trình tìm kiếm GuardDuty.1 đã thay đổi từ MỚI thành ĐÃ NGĂN CHẶN_**
**Lưu ý:** Chỉ xóa các phát hiện sau khi triển khai các biện pháp kiểm soát bù trừ bắt buộc do nhóm bảo mật cung cấp.

13\. Sau khi trạng thái quy trình tìm kiếm của phát hiện là **ĐÃ NGĂN CHẶN,** quy trình xác thực tự động sẽ bắt đầu và bạn có thể xem nhật ký hàm Lambda trong bảng điều khiển CloudWatch liên quan đến các xác thực khác nhau đã thực hiện.

Để xem nhật ký hàm Lambda trong bảng điều khiển CloudWatch:

1. Truy cập bảng điều khiển Amazon CloudWatch.

2. Trong ngăn điều hướng, bên dưới mục Nhật ký, chọn Nhóm Nhật ký.

3. Chọn nhóm nhật ký có tên hàm Lambda.

4. Chọn luồng nhật ký gần đây nhất để xem nhật ký.

**![Hình ảnh 9: Nhật ký CloudWatch của Hàm Lambda]![văn bản thay thế](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-9-7.png)**
**_Hình ảnh 9: Nhật ký CloudWatch của Hàm Lambda_**

Giải pháp cập nhật ghi chú phát hiện trong Security Hub CSPM với kết quả xác thực:

Nếu tất cả các điều khiển đều đạt:

- Vẫn đang tìm kiếmr **ĐÃ BỊ ỨC CHẾ.**

- Một ghi chú sẽ được thêm vào với kết quả xác thực và mức độ rủi ro đã điều chỉnh.

- Bối cảnh nghiệp vụ sẽ được thêm vào kết quả tìm kiếm.

Nếu một trong các biện pháp kiểm soát không thành công:

- Tìm kiếm thay đổi trạng thái cho **ĐÃ THÔNG BÁO.**

- Một ghi chú sẽ được thêm vào với thông tin chi tiết về các biện pháp kiểm soát không thành công.

- Nhóm bảo mật sẽ xem xét những thay đổi này như một phần của quy trình chuẩn của họ.

**Để xem trạng thái quy trình làm việc của các ghi chú phát hiện và cập nhật bằng bảng điều khiển (nhà phát triển):**

1. Truy cập bảng điều khiển CSPM của Security Hub.

2. Trong ngăn điều hướng, chọn Findings.

3. Trong thanh tìm kiếm, chọn bộ lọc Compliance Security Control ID và nhập giá trị Is là **GuardDuty.1.**

4. Chọn mục GuardDuty cần được bật và kiểm tra trạng thái Quy trình làm việc.

5. Đối với Hành động, chọn Thêm Ghi chú.

6. Kiểm tra Ghi chú Cuối cùng Đã Thêm.

**![Hình ảnh 10: Ghi chú Tìm kiếm Cập nhật của Security Hub CSPM]![văn bản thay thế](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/image-10-4.png)**
**_Hình ảnh 10: Ghi chú Tìm kiếm Cập nhật của Security Hub CSPM_**

Ghi chú khám phá cho thấy quá trình xác thực tự động đã thực hiện kiểm tra và ghi lại kết quả, đồng thời lưu ý rằng mức độ nghiêm trọng ban đầu **CAO** do Security Hub CSPM chỉ định vẫn được duy trì, và mức độ nghiêm trọng đã điều chỉnh **TRUNG BÌNH** do nhóm bảo mật cung cấp được thêm vào phần Ghi chú và bảng Bằng chứng, đảm bảo tính minh bạch và trách nhiệm giải trình trong khi vẫn duy trì mức độ nghiêm trọng ban đầu do Security Hub CSPM chỉ định**.**
**Dọn dẹp**
Để tránh chi phí phát sinh, hãy sử dụng lệnh sau để dọn dẹp các tài nguyên được tạo cho bài đăng này.

**./cleanup.sh**
Quy trình triển khai này được thiết kế đơn giản và duy trì các biện pháp bảo mật tốt nhất như mã hóa, đặc quyền tối thiểu và phân tách nhiệm vụ.

**Kết luận**
Trong bài viết này, chúng tôi đã hướng dẫn bạn cách triển khai một giải pháp mà các nhóm bảo mật có thể sử dụng để xác định các biện pháp kiểm soát bù trừ cho các phát hiện CSPM của AWS Security Hub và tự động xác thực việc triển khai chúng. Chúng tôi đã phân tích những thách thức trong việc quản lý các ngoại lệ bảo mật và chứng minh cách giải pháp này giúp thu hẹp khoảng cách giữa các yêu cầu bảo mật và việc triển khai thực tế.
Giải pháp cung cấp một quy trình làm việc có cấu trúc, trong đó các nhóm bảo mật xác định các biện pháp kiểm soát bù trừ phù hợp, các nhà phát triển triển khai chúng và một hệ thống tự động xác thực hiệu quả của chúng. Với sự hỗ trợ cho 13 loại xác thực khác nhau, từ các quy tắc AWS Config đến tài liệu quy trình, giải pháp cung cấp phạm vi bảo mật toàn diện cho nhiều tình huống bảo mật khác nhau.
Chúng tôi cũng đã trình diễn một quy trình toàn diện để thêm các biện pháp kiểm soát bù trừ vào các phát hiện GuardDuty và cho thấy giải pháp này duy trì mức độ nghiêm trọng của phát hiện ban đầu do CSPM của Security Hub chỉ định, đồng thời ghi lại mức độ rủi ro đã điều chỉnh được nhóm bảo mật phê duyệt. Cách tiếp cận này giúp duy trì tính minh bạch và khả năng kiểm toán, đồng thời cho phép các ngoại lệ cần thiết.

Hãy dùng thử và chia sẻ phản hồi của bạn trong phần bình luận.
Tuyên bố miễn trừ trách nhiệm bảo mật: Các cấu hình Amazon S3 được trình bày trong bài viết này liên quan đến các thiết lập có thể truy cập công khai, làm lộ dữ liệu trên internet và chỉ nên được sử dụng cho mục đích trình diễn hoặc nội dung không nhạy cảm. Các bucket S3 công khai tiềm ẩn những rủi ro đáng kể, bao gồm rò rỉ dữ liệu, chi phí bất ngờ do sử dụng trái phép, vi phạm quy định và các lỗ hổng bảo mật tiềm ẩn. Đối với môi trường sản xuất, hãy sử dụng vai trò IAM, triển khai chính sách truy cập đặc quyền tối thiểu, bật cài đặt S3 Block Public Access và cân nhắc sử dụng CloudFront với Origin Access Control để phân phối nội dung công khai. Vui lòng tham khảo ý kiến ​​nhóm bảo mật của bạn và đảm bảo tuân thủ các chính sách của tổ chức trước khi triển khai các cấu hình S3 công khai trong môi trường sản xuất.

---

<div style="display: flex; gap: 20px; padding: 20px; border: 1px solid #e5e7eb; border-radius: 6px; margin-bottom: 24px;">
<img src="https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/Reetesh-Surjani-author.jpg"
width="130"
style="border-radius: 4px;">
<div>
<h3>Reetesh Surjani</h3>
<p>
Reetesh là Chuyên gia Tư vấn Triển khai về Rủi ro & Tuân thủ Bảo mật tại AWS Professional Services,
có trụ sở tại Pune, Ấn Độ. Anh làm việc chặt chẽ với khách hàng trên nhiều lĩnh vực khác nhau để giúp
củng cố cơ sở hạ tầng bảo mật và đạt được các mục tiêu bảo mật của họ.
</p>
</div>
</div>

<div style="display: flex; gap: 20px; padding: 20px; border: 1px solid #e5e7eb; border-radius: 6px; margin-bottom: 24px;">
<img src="https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2025/08/22/Satish-Kamat-author.jpg"
width="130"
style="border-radius: 4px;">
<div>
<h3>Satish Kamat</h3>
<p>
Satish là Chuyên gia Tư vấn Triển khai Cấp cao về Phát triển Ứng dụng tại AWS Professional Services,
có trụ sở tại Pune, Ấn Độ. Anh làm việc chặt chẽ với khách hàng trong quá trình chuyển đổi và di chuyển lên đám mây.hành trình xuyên suốt nhiều ngành dọc khác nhau như BFSI, ô tô và viễn thông.
</p>
</div>
</div>
