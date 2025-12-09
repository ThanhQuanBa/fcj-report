---
title: "Blog 1"
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Xây dựng các ứng dụng đa cụm linh hoạt với Amazon EKS, Phần 1: Triển khai cân bằng tải liên cụm với NLB

bởi Krishna Sarabu, Anuj Butail và Pushkar Patil vào ngày 22 tháng 9 năm 2025 trong [Amazon Elastic Kubernetes Service](https://aws.amazon.com/blogs/networking-and-content-delivery/category/compute/amazon-kubernetes-service/) , [Compute](https://aws.amazon.com/blogs/networking-and-content-delivery/category/compute/) , [Elastic Load Cân bằng](https://aws.amazon.com/blogs/networking-and-content-delivery/category/networking-content-delivery/elastic-load-balancing/) , [Mạng và Phân phối Nội dung](https://aws.amazon.com/blogs/networking-and-content-delivery/elastic-load-balancing/) , [Mạng và Phân phối Nội dung](https://aws.amazon.com/blogs/networking-and-content-delivery/elastic-load-balancing/) liên kết](https://aws.amazon.com/blogs/networking-and-content-delivery/category/networking-content-delivery/) [vĩnh viễn](https://aws.amazon.com/blogs/networking-and-content-delivery/building-resilient-multi-cluster-applications-with-amazon-eks/) [Chia sẻ](https://aws.amazon.com/vi/blogs/networking-and-content-delivery/building-resilient-multi-cluster-applications-with-amazon-eks/#)

Chuỗi bài viết gồm ba phần này khám phá các mẫu thiết kế và chiến lược nhằm tăng cường khả năng phục hồi của ứng dụng thông qua việc triển khai đa cụm trên [Amazon Elastic Kubernetes Service (EKS)](https://aws.amazon.com/eks/). Trong phần đầu tiên này, chúng tôi sẽ giải quyết một thách thức thường gặp khi sử dụng [Network Load Balancer (NLB)](https://aws.amazon.com/elasticloadbalancing/network-load-balancer/) trong môi trường đa cụm.

Các tổ chức ngày càng phụ thuộc vào Kubernetes—dù thông qua [Amazon Elastic Kubernetes Service (EKS)](https://aws.amazon.com/eks/) hay các cụm tự quản lý trên [Amazon Web Services (AWS)](https://aws.amazon.com/)—để chạy và mở rộng quy mô các ứng dụng quan trọng. Mặc dù việc chạy khối lượng công việc trên một cụm EKS duy nhất mang lại sự tiện lợi, nhưng nó cũng đặt ra những thách thức trong việc duy trì tính khả dụng cao trong các hoạt động quan trọng. Các hoạt động như nâng cấp cụm, cập nhật tiện ích bổ sung và thay đổi quy trình làm việc có thể ảnh hưởng đến khả năng phục hồi khối lượng công việc và tính khả dụng của ứng dụng, khiến việc chủ động giải quyết những vấn đề này là cần thiết.

Để giảm thiểu những thách thức này, người dùng thường triển khai ứng dụng trên nhiều cụm EKS. Phương pháp đa cụm này mang lại một số lợi ích chính:

- **Nâng cấp Xanh-Xanh**: Nâng cấp không có thời gian chết thông qua các triển khai xanh-xanh, cho phép lưu lượng di chuyển dần dần giữa các cụm.

- **Nâng cấp Cụm và Cập nhật Tiện ích Bổ sung**: Các cụm và tiện ích bổ sung được cập nhật theo từng cụm, giảm thiểu gián đoạn trên toàn bộ hệ thống.

- **Khả năng phục hồi Khối lượng Công việc**: Cải thiện khả năng phục hồi khối lượng công việc khi các cụm bị xóa nhầm.

- **Chuyển đổi dự phòng và Phục hồi Thảm họa**: Cải thiện khả năng phục hồi thảm họa với khả năng chuyển đổi dự phòng liên cụm.

Mặc dù những lợi ích này rất hấp dẫn, nhưng việc triển khai cân bằng tải hiệu quả trên nhiều cụm trước đây vẫn là một thách thức. Tuy nhiên, [Bộ điều khiển Cân bằng Tải AWS (LBC)](https://docs.aws.amazon.com/eks/latest/userguide/aws-load-balancer-controller.html) v2.10+ hiện đã giải quyết vấn đề này bằng cách hỗ trợ phân phối lưu lượng liên cụm thông qua TargetGroupBinding đa cụm, một tính năng mạnh mẽ mà chúng tôi sẽ trình bày chi tiết trong bài viết này. Tính năng này đặc biệt hữu ích cho các mô hình máy khách-máy chủ và các tình huống mà tổ chức tự quản lý cấu hình proxy Lớp 7 của mình, vì Bộ cân bằng tải mạng (NLB) cung cấp tính linh hoạt và hiệu suất cần thiết cho các trường hợp sử dụng này.

## **NLB Target Group Binding được cải tiến: Hỗ trợ triển khai EKS đa cụm**

[Tính năng] mới này (https://kubernetes-sigs.github.io/aws-load-balancer-controller/latest/guide/targetgroupbinding/targetgroupbinding/#multicluster-target-group) cho phép NLB đăng ký các mục tiêu (TargetGroupBinding) từ các cụm EKS khác nhau vào cùng một nhóm mục tiêu NLB, đảm bảo lưu lượng được phân phối liền mạch.

### **Cách thức hoạt động**

Với LBC phiên bản 2.10 trở lên, tính năng mới cho phép quản lý mục tiêu hiệu quả trên nhiều cụm EKS. Sử dụng ConfigMap cho mỗi cụm TargetGroupBinding cho phép bộ điều khiển hỗ trợ triển khai đa cụm một cách liền mạch.

Tham số mới, multiClusterTargetGroup, cho phép NLB xử lý các mục tiêu trên nhiều cụm. Khi được bật, cờ này đảm bảo mỗi cụm quản lý các mục tiêu của mình một cách độc lập, cải thiện độ tin cậy và hợp lý hóa việc cân bằng tải trên các cụm.

Hình sau đây minh họa kiến ​​trúc tham chiếu:

![alt text](https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2025/09/21/Arch1-1024x888.png)

Hình 1 – Kiến trúc

Quy trình diễn ra như sau:

1. Quản lý Mục tiêu: Đối với mỗi cụm EKS, LBC duy trì một ConfigMap riêng để theo dõi các mục tiêu cho các điểm cuối dịch vụ của cụm đó. Điều này đảm bảo rằng chỉ các mục tiêu cụ thể của cụm mới được đăng ký hoặc hủy đăng ký khỏi NLB, avhỗ trợ bất kỳ thay đổi vô ý nào đối với các cụm khác.

2. Đăng ký Pod: Khi một pod mới được khởi tạo, LBC sẽ cập nhật ConfigMap trong vòng lặp đối chiếu của nó. Mục tiêu mới được đăng ký trong NLB, đảm bảo lưu lượng có thể được định tuyến chính xác đến pod mới.

3. Xóa Pod: Tương tự, khi một pod bị xóa, LBC sẽ cập nhật ConfigMap để phản ánh thay đổi và hủy đăng ký mục tiêu đã xóa khỏi NLB, giúp hệ thống nhất quán và không có lỗi.

4. Quy trình đối chiếu: LBC thường xuyên đối chiếu các điểm cuối dịch vụ với các mục tiêu NLB, thêm các điểm cuối mới và loại bỏ các điểm cuối lỗi thời trong khi sử dụng ConfigMap để duy trì sự phân tách cụm. Khi có thay đổi, LBC sẽ cập nhật toàn bộ đối tượng ConfigMap thành một thao tác duy nhất. Bộ điều khiển không hỗ trợ chức năng cập nhật hoặc vá lỗi một phần.

Bây giờ, hãy triển khai cấu hình đa cụm này.

## **Điều kiện tiên quyết**

Đối với hướng dẫn này, bạn cần có tài khoản AWS với quyền [AWS Identity and Access Management (IAM)](https://aws.amazon.com/iam/) phù hợp để tạo cụm EKS và vai trò IAM, cũng như có thể khởi chạy mẫu [AWS CloudFormation](https://aws.amazon.com/cloudformation/) được cung cấp. Để biết thông tin chi tiết về giá, vui lòng tham khảo trang [Bảng giá AWS](https://aws.amazon.com/pricing) chính thức.

## **Triển khai giải pháp với CloudFormation**

Chúng tôi sử dụng ngăn xếp CloudFormation để triển khai giải pháp này. Ngăn xếp này tạo ra tất cả các tài nguyên cần thiết, chẳng hạn như sau:

- Các thành phần mạng như [Amazon Virtual Private Cloud (Amazon VPC),](https://aws.amazon.com/vpc/) các mạng con và [NAT Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) .

- Hai cụm EKS: Mỗi cụm EKS có hai nút đang hoạt động ([Amazon EC2)](https://aws.amazon.com/ec2/) được triển khai trong cùng một VPC.

Để bắt đầu, hãy hoàn thành các bước sau:

1. Đăng nhập vào [AWS Management Console](https://aws.amazon.com/console/) .

2. Chọn Launch Stack trong bất kỳ [Khu vực AWS] nào (https://aws.amazon.com/about-aws/global-infrastructure/regions_az/) và mở nó trong một tab mới: ![văn bản thay thế] (https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2025/09/22/launchstack.png)

3. Trên trang Create Stack, tiếp tục với tất cả các thiết lập mặc định.

4. Chọn hộp kiểm để xác nhận việc tạo tài nguyên IAM.

5. Chọn Create Stack.

6. Chờ quá trình tạo stack hoàn tất. Bước này có thể mất đến 15 phút để hoàn tất.

Lúc này, bạn đã có một cụm EKS Chính và Phụ. Hãy cấu hình tiện ích bổ sung LBC trên cả hai cụm và triển khai một ứng dụng mẫu để minh họa tính năng cân bằng tải đa cụm.

Từ Bảng điều khiển, khởi chạy [AWS CloudShell](https://aws.amazon.com/cloudshell/) :

![văn bản thay thế](https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2025/09/21/cs.png)

Hình 2 – Khởi chạy CloudShell từ Bảng điều khiển AWS

Sử dụng CloudShell, chạy lệnh sau:

git clone https://github.com/aws-samples/sample-eksapps-ha-design-patterns.git

cd sample-eksapps-ha-design-patterns/nlb_config_aws_lbc/src

bash ./install_prereqs.sh

Tập ​​lệnh triển khai thành công ba nginxgroup với dịch vụ NLB LoadBalancer trên máy chủ chính Cụm EKS (pri-eks-clu1), trong khi cụm EKS phụ (sec-eks-clu1) đã được khởi tạo và sẵn sàng cho các cấu hình tiếp theo.

Chúng ta sẽ xem xét hai kịch bản trong các phần sau:

1. Kịch bản 1 (khối lượng công việc hiện có): Sử dụng NLB hiện có để định tuyến lưu lượng đến dịch vụ nginx đang chạy trên cả cụm EKS chính và phụ.

2. Kịch bản 2 (khối lượng công việc mới): Tạo một NLB mới để định tuyến lưu lượng đến dịch vụ nginx đang chạy trên cả cụm EKS chính và phụ.

Mỗi kịch bản minh họa cách phân phối lưu lượng liền mạch trên nhiều cụm bằng cách sử dụng một thiết lập NLB duy nhất.

Chúng ta bắt đầu với Kịch bản 1 cho các khối lượng công việc hiện có.

### **Kịch bản 1: Mở rộng cấu hình NLB hiện có để hỗ trợ nhiều cụm**

Trong kịch bản này, bạn làm việc với một NLB hiện có và Nhóm mục tiêu cùng TargetGroupBinding liên quan được cấu hình trên cụm EKS chính. Bạn cập nhật TargetGroupBinding trên cụm EKS chính để kích hoạt hỗ trợ đa cụm. Tiếp theo, bạn tạo một TargetGroupBinding mới trên cụm EKS phụ cho dịch vụ nginx. Cấu hình này cho phép NLB hiện tại phân phối lưu lượng giữa các dịch vụ nginx chạy trên cả cụm EKS chính và phụ bằng cùng một Nhóm mục tiêu. Cách tiếp cận này đảm bảo phân phối lưu lượng liền mạch trên cả hai cụm trong khi vẫn duy trì cơ sở hạ tầng cân bằng tải hiện có.

Bước 1: Xác minh hỗ trợ đa cụm trong LBC

Sử dụng CloudShell, hãy xác minh rằng cả cụm EKS chính và phụ đều đã cài đặt aws-load-balancer-controller với chế độ đa cụm được bật bằng cách chạy các lệnh sau:

kubectl \--context pri-eks-clu1 explain targetGroupBinding.spec.multiClusterTargetGroup

kubectl \--context sec-eks-clu1 explain targetGroupBinding.spec.multiClusterTargetGroup

Dam

Kết quả đầu ra thành công sẽ hiển thị trường multiClusterTargetGroup defined trong TargetGroupBinding cho cả hai cụm, xác nhận rằng khả năng đa cụm của LBC đã được bật đúng cách. Nếu trường này bị thiếu, hãy đảm bảo bạn đã cài đặt phiên bản LBC mới nhất.

Bước 2: Bật hỗ trợ đa cụm và bảo vệ xóa trên NLB

Sử dụng CloudShell, chạy lệnh sau để thêm bảo vệ xóa và bật nhóm mục tiêu đa cụm với chú thích trên cụm EKS chính:

kubectl \--context pri-eks-clu1 patch service nginx \\

\--patch '{"metadata": {"annotations": {"service.beta.kubernetes.io/aws-load-balancer-multi-cluster-target-group": "true", "service.beta.kubernetes.io/aws-load-balancer-attributes": "deletion_protection.enabled=true"}}}' \\

\--type=merge

Kết quả trả về sẽ hiển thị service/nginx patched.

Bước 3: Xác minh cấu hình đa cụm trong TargetGroupBinding

Sử dụng CloudShell, chạy lệnh sau để xác minh rằng cờ targetGroupBinding đã được cập nhật thành multiClusterTargetGroup:

kubectl \--context pri-eks-clu1 get targetgroupbinding \-l service.k8s.aws/stack-name\=nginx \\

\-o jsonpath='{range .items\[\*\]}{.metadata.name}{"\\t"}{.spec.multiClusterTargetGroup}{"\\n"}{end}'

Kết quả trả về thành công sẽ hiển thị là true.

Bước 4: Xác minh đăng ký mục tiêu trong ConfigMap

Khi cờ TargetGroupBinding multiClusterTargetGroup được cập nhật, LBC sẽ tạo một đối tượng ConfigMap chứa danh sách các mục tiêu và khớp các Pod mục tiêu với NLB Target.

Sử dụng CloudShell, hãy chạy các lệnh sau để xác minh rằng ConfigMap tạo và đăng ký các mục tiêu:

kubectl \--context pri-eks-clu1 get cm aws-lbc-targets-$(kubectl get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.metadata.name}{end}')

kubectl \--context pri-eks-clu1 describe cm aws-lbc-targets-$(kubectl get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.metadata.name}{end}')

Kết quả đầu ra thành công cho thấy ConfigMap chứa các mục tiêu đã đăng ký, xác nhận sự khớp mục tiêu LBC giữa nginxpods và NLB.

Bước 5: Triển khai nginxapp trên cụm EKS phụ

Sử dụng CloudShell, chạy các lệnh sau để tạo một đối tượng triển khai và một đối tượng dịch vụ loại ClusterIP trên cụm EKS phụ:

kubectl \--context sec-eks-clu1 create \-f nginxapp.yaml

kubectl \--context sec-eks-clu1 create \-f nginx_sec-eks-clu1.yaml

kubectl get all

Kết quả thành công cho thấy nginxdeployment với 2 pod đang chạy và dịch vụ ClusterIP đã được tạo trên cụm phụ.

Bước 6: Tạo nhiều ClusterTargetGroupBinding trên Cluster EKS phụ

Sử dụng CloudShell, chạy lệnh sau để tạo TargetGroupBinding với cờ multiClusterTargetGroup trên Cluster EKS phụ:

\# Lấy ARN targetGroup từ Cluster EKS chính

targetgrouparn=$(kubectl \--context pri-eks-clu1 get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.spec.targetGroupARN}{end}')

lb=$(kubectl \--context pri-eks-clu1 get svc nginx \-o jsonpath='{.status.loadBalancer.ingress\[0\].hostname}' | sed \-n 's/.\*\\(k8s-default-nginx-\[a-z0-9\]\*\\).\*/\\1/p')

\# Lấy Security GroupId

secgrpid=$(aws ec2 describe-security-groups \--group-ids $(aws elbv2 describe-load-balancers \--names ${lb} \--query 'LoadBalancers\[\*\].SecurityGroups\[\]' \--output text) \--query "SecurityGroups\[?Description=='\[k8s\] Shared Backend SecurityGroup for LoadBalancer'\].GroupId" \--output text)

\# cập nhật tệp tgb.yaml

sed "s|\<securitygroupid\>|${secgrpid}|g; s|\<targetgrouparn\>|${targetgrouparn}|g" tgb.yaml \> tgb_sec.yaml

kubectl \--context sec-eks-clu1 create \-f tgb_sec.yaml

Bước 7: Xác minh việc tạo ConfigMap trên cụm EKS thứ cấp

Sử dụng CloudShell, chạy các lệnh sau để xác minh đối tượng ConfigMap và nội dung của nó đã được tạo bởi bộ điều khiển cùng với targetGroupBinding:

kubectl \--context sec-eks-clu1 get cm aws-lbc-targets-nginx

kubectl \--context sec-eks-clu1 describe cm aws-lbc-targets-nginx

Kết quả trả về thành công sẽ hiển thị thông tin ConfigMap aws-lbc-targets-nginx chứa địa chỉ IP và cổng của đích nginx.

Bước 8: Xác minh Đăng ký Mục tiêu trên Cả hai Cụm EKS

Sử dụng CloudShell, chạy lệnh sau để xác minh rằng NLB đã đăng ký các mục tiêu từ cả cụm EKS chính và phụ:

aws elbv2 describe-target-health \--target-group-arn $( kubectl get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.spec.targetGroupARN}{end}') \--query 'TargetHealthDescriptions\[\*\].\[Target.Id,Target.AvailabilityZone,TargetHealth.State\]' \--output table

Kết quả đầu ra thành công cho thấy các mục tiêu khỏe mạnh được phân bổ trên cả cụm EKS chính và phụ, xác nhận cấu hình đa cụm thành công.

Bước 9: Xác minh Phân phối Lưu lượng trên Cụm EKS

Sử dụng CloudShell, chạy các lệnh sau để thực hiện kiểm tra khối lượng công việc tổng hợp và xác minh phân phối lưu lượng đến các Pod trên cả cụm chính và cụm phụ.Các cụm EKS condary:

\# Lấy tên máy chủ NLB

lb=$(kubectl \--context pri-eks-clu1 get svc nginx \-o jsonpath='{.status.loadBalancer.ingress\[0\].hostname}')

\# Chạy thử nghiệm ApacheBench

ab \-n 1000 \-c 40 http://$lb/

\# Kiểm tra nhật ký trên cụm chính

echo "Cụm EKS chính:"

kubectl \--context pri-eks-clu1 logs deployment/nginx \--tail=2

\# Kiểm tra nhật ký trên cụm phụ

echo "Cụm EKS phụ:"

kubectl \--context sec-eks-clu1 logs deployment/nginx \--tail=2

Kết quả đầu ra thành công cho thấy các yêu cầu HTTP được ghi lại trong cả hai cụm, xác nhận rằng NLB đang phân phối đúng cách lưu lượng truy cập trên tất cả các pod nginx trên cụm EKS chính và phụ.

Dọn dẹp: Sử dụng CloudShell, chạy các lệnh sau:

kubectl \--context sec-eks-clu1 delete targetgroupbinding nginx

kubectl \--context sec-eks-clu1 delete svc nginx

kubectl \--context pri-eks-clu1 patch service nginx \--patch '{"metadata": {"annotations": {"service.beta.kubernetes.io/aws-load-balancer-attributes": "deletion_protection.enabled=false"}}}' \--type=merge

kubectl \--context pri-eks-clu1 delete svc nginx

kubectl \--context pri-eks-clu1 delete deployment nginx

Bạn đã thử nghiệm thành công việc di chuyển các khối lượng công việc hiện có trong Kịch bản 1. Trong Kịch bản 2, bạn có thể khám phá cách triển khai cân bằng tải đa cụm cho các triển khai mới.

### **Kịch bản 2: Triển khai cân bằng tải đa cụm cho các triển khai mới**

Trong kịch bản này, bạn tạo một NLB mới được thiết kế để hỗ trợ phân phối lưu lượng đa cụm ngay từ đầu. Việc triển khai này yêu cầu LBC 2.10 trở lên phải được cài đặt trên cả hai cụm EKS.

Bước 1: Triển khai nginx với Dịch vụ LoadBalancer trên cụm EKS chính

Sử dụng CloudShell, hãy chạy các lệnh sau:

kubectl \--context pri-eks-clu1 create \-f nginxapp.yaml

kubectl \--context pri-eks-clu1 create \-f nginxsvc.yaml

Bước 2: Xác minh việc tạo ConfigMap bằng LBC

Sử dụng CloudShell, hãy chạy các lệnh sau để xác minh rằng đối tượng ConfigMap và nội dung của nó được tạo bằng targetGroupBinding:

kubectl \--context pri-eks-clu1 get cm aws-lbc-targets-$(kubectl get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.metadata.name}{end}')

kubectl \--context pri-eks-clu1 describe cm aws-lbc-targets-$(kubectl get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.metadata.name}{end}')

Kết quả trả về thành công hiển thị địa chỉ IP ConfigMap và cổng của nginxtarget, xác nhận cấu hình LBC chính xác.

Bước 3: Triển khai dịch vụ nginx trên cụm EKS phụ

Sử dụng CloudShell, chạy các lệnh sau để tạo triển khai dịch vụ nginx và ClusterIP trên cụm EKS phụ:

kubectl \--context sec-eks-clu1 create \-f nginxapp.yaml

kubectl \--context sec-eks-clu1 create \-f nginx_sec-eks-clu1.yaml

Bước 4: Cấu hình TargetGroupBinding trên cụm EKS phụ

Sử dụng CloudShell, chạy các lệnh sau để tạo cụm TargetGroupBinding hỗ trợ đa cụm:

\# Lấy ARN targetGroup từ cụm EKS chính

targetgrouparn=$(kubectl \--context pri-eks-clu1 get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.spec.targetGroupARN}{end}')

lb=$(kubectl \--context pri-eks-clu1 get svc nginx \-o jsonpath='{.status.loadBalancer.ingress\[0\].hostname}' | sed \-n 's/.\*\\(k8s-default-nginx-\[a-z0-9\]\*\\).\*/\\1/p')

\# Lấy Security GroupId

secgrpid=$(aws ec2 describe-security-groups \--group-ids $(aws elbv2 describe-load-balancers \--names ${lb} \--query 'LoadBalancers\[\*\].SecurityGroups\[\]' \--output text) \--query "SecurityGroups\[?Description=='\[k8s\] Shared Backend SecurityGroup for LoadBalancer'\].GroupId" \--output text)

\# cập nhật tệp tgb.yaml

sed "s|\<securitygroupid\>|${secgrpid}|g; s|\<targetgrouparn\>|${targetgrouparn}|g" tgb.yaml \> tgb_p2.yaml

kubectl \--context sec-eks-clu1 create \-f tgb_p2.yaml

Bước 5: Xác minh ConfigMap trên cụm EKS phụ

Sử dụng CloudShell, hãy chạy các lệnh sau để xác minh đối tượng ConfigMap và nội dung của nó đã được tạo bởi bộ điều khiển:

kubectl \--context sec-eks-clu1 get cm aws-lbc-targets-$(kubectl \--context sec-eks-clu1 get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.metadata.name}{end}')

kubectl \--context sec-eks-clu1 describe cm aws-lbc-targets-$(kubectl \--context sec-eks-clu1 get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.metadata.name}{end}')

Kết quả thành công hiển thị ConfigMap chứa các IP và cổng đích của cụm EKS thứ cấp nginx.

Bước 6: Xác minh Đăng ký Mục tiêu trên Cả hai Cụm EKS

Sử dụng CloudShell, chạy lệnh sau để xác minh rằng NLB đã đăng ký thành công các mục tiêu từ cả cụm EKS chính và phụ:

aws elbv2 describe-target-health \--target-group-arn $( kubectl get targetgroupbinding \-l service.k8s.aws/stack-name=nginx \-o jsonpath='{range .items\[\*\]}{.spec.targetGroupARN}{end}') \--query 'TargetHealthDescriptions\[\*\].\[Target.Id,Target.AvailabilityZone,TargetHealth.State\]' \--bảng đầu ra

Đầu ra dự kiến:

\------------------------------------------------

| DescribeTargetHealth |

\+--------------+--------------+----------+

| xx.xx.xx.xxx| us-east-1b | healthy |

| xx.xx.xx.xxx| us-east-1c | healthy |

| xx.xx.xx.xxx| us-east-1b | healthy |

\+--------------+--------------+----------+

Đầu ra hiển thị các mục tiêu khỏe mạnh được phân bổ trên các cụm EKS chính và phụ, xác nhận cấu hình đa cụm thành công.

Dọn dẹp: Sử dụng AWS CloudShell, hãy chạy các lệnh sau để dọn dẹp tất cả tài nguyên:

kubectl \--context sec-eks-clu1 delete targetgroupbinding nginx

kubectl \--context sec-eks-clu1 delete svc nginx

kubectl \--context pri-eks-clu1 patch service nginx \--patch '{"metadata": {"annotations": {"service.beta.kubernetes.io/aws-load-balancer-attributes": "deletion_protection.enabled=false"}}}' \--type=merge

kubectl \--context pri-eks-clu1 delete svc nginx

kubectl \--context pri-eks-clu1 delete deployment nginx

aws cloudformation delete-stack \--stack-name eks-nlb

Bản demo này cho thấy cách tính năng NLB đa cụm tăng cường khả năng phục hồi dịch vụ bằng cách cho phép phân phối lưu lượng trên nhiều cụm EKS cho cả triển khai bộ cân bằng tải hiện tại và mới.

## **Cân nhắc**

- Tính năng này hiện chỉ hỗ trợ phân phối đồng đều, chủ động-chủ động giữa các mục tiêu trong cả hai cụm EKS. Cân bằng tải mục tiêu có trọng số hiện không được hỗ trợ.

- Giới hạn tài khoản VPC, giới hạn API và giới hạn NLB hiện tại vẫn có hiệu lực với tính năng này.

- Tốt nhất nên bật tính năng bảo vệ xóa trên NLB để tránh xóa nhầm.

- Mỗi dịch vụ có ánh xạ một-một tới đối tượng ConfigMap, đảm bảo quản lý TargetGroupBindings phù hợp cho từng dịch vụ.

- LBC ghi tất cả các mục tiêu vào ConfigMap (giới hạn ở mức 1MB) trong một bản cập nhật duy nhất thay vì tăng dần. Theo dõi tình trạng mặt phẳng điều khiển EKS.

## **Kết luận**

Trong phần đầu tiên của loạt bài này, chúng tôi đã trình bày cách đạt được khả năng phục hồi trên nhiều cụm Amazon EKS bằng cách sử dụng tính năng NLB mới theo cách khai báo. Khi các tổ chức ngày càng di chuyển và hiện đại hóa ứng dụng sang môi trường Kubernetes, việc triển khai các giải pháp mạnh mẽ và có khả năng mở rộng trở nên quan trọng để duy trì tính khả dụng cao. Việc phân bổ khối lượng công việc trên các Cụm không chỉ cải thiện khả năng chịu lỗi mà còn hợp lý hóa việc nâng cấp và tăng cường khả năng phục hồi sau thảm họa. Truy cập [Trung tâm Thực hành Tốt nhất EKS](https://github.com/aws/aws-eks-best-practices) của chúng tôi để biết các mẫu kiến ​​trúc, hướng dẫn bảo mật và chiến lược tối ưu hóa chi phí cho khối lượng công việc sản xuất.

Hãy theo dõi các bài đăng tiếp theo trong loạt bài này, nơi chúng tôi khám phá các mẫu thiết kế bổ sung để cải thiện hơn nữa khả năng phục hồi của khối lượng công việc chạy trên Amazon EKS.

## **Giới thiệu về Tác giả**

| <img src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2025/09/21/krishna.png" width="600"> | Krishna Sarabu là Kỹ sư Cơ sở dữ liệu Cao cấp tại AWS. Anh tập trung vào các lĩnh vực container, hiện đại hóa ứng dụng, cơ sở hạ tầng và các công cụ cơ sở dữ liệu nguồn mở Amazon RDS for PostgreSQL và Amazon Aurora PostgreSQL. Anh thích làm việc với người dùng để giúp thiết kế, triển khai và tối ưu hóa khối lượng công việc cơ sở dữ liệu quan hệ trên AWS.                                                                               |
| :---------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ![alt text](https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2025/09/21/anuj.png)               | Anuj Butail là Kiến trúc sư Giải pháp Chính tại AWS. Làm việc tại San Francisco, anh hỗ trợ người dùng ở San Francisco và Thung lũng Silicon thiết kế và xây dựng các ứng dụng quy mô lớn trên AWS. Anh chuyên về AWS, dịch vụ biên và container. Anh thích chơi tennis, đọc sách và dành thời gian cho gia đình.                                                                                                                                  |
| ![alt text](https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2025/09/21/pushkar.png)            | Pushkar Patil là Chủ sở hữu Sản phẩm của nhóm mạng AWS có trụ sở tại California. Anh có hơn một thập kỷ kinh nghiệm thúc đẩy đổi mới sản phẩm và chiến lược trong lĩnh vực điện toán đám mây và cơ sở hạ tầng. Pushkar đã ra mắt thành công nhiều sản phẩm mới bằng cách thấu hiểu nhu cầu của người dùng và cung cấp các giải pháp sáng tạo. Khi không làm việcg, bạn có thể tìm thấy người đam mê môn cricket này đang đi du lịch cùng gia đình. |
