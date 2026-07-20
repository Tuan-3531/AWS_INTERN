---
title: "Các bài blogs"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---


###  [Blog 1 - Vì sao Epic Games phát triển Lore và cách AWS giúp tối ưu lưu trữ binary assets](3.1-Blog1/)
Bài blog giới thiệu Lore, hệ thống version control mã nguồn mở do Epic Games phát triển để quản lý binary assets như texture, model, animation và engine binaries. Khác với các hệ thống truyền thống, Lore chia dữ liệu thành các fragment (chunk) và chỉ lưu những phần thay đổi, giúp giảm đáng kể dung lượng lưu trữ, tối ưu chi phí và tăng hiệu quả tái sử dụng dữ liệu giữa nhiều dự án.
Để triển khai Lore trên nền tảng đám mây, AWS cung cấp một kiến trúc tham chiếu sử dụng Amazon EC2, Amazon ECS, Amazon S3, Amazon DynamoDB và AWS Cloud Map. Kiến trúc này mang lại khả năng mở rộng linh hoạt, đồng bộ dữ liệu nhanh và quản lý hiệu quả các dự án game quy mô lớn với khối lượng binary assets rất lớn.
###  [Blog 2 - Kiến trúc bảo mật Serverless: Cách bảo vệ ứng dụng AI Riddle Generator trên AWS](3.2-Blog2/)
Bài blog giới thiệu kiến trúc bảo mật nhiều lớp (Defense in Depth) dành cho ứng dụng AI Riddle Generator được xây dựng theo mô hình Serverless trên AWS. Kiến trúc kết hợp các dịch vụ như AWS WAF, Amazon CloudFront, Amazon Cognito, Amazon API Gateway, AWS IAM, Amazon CloudWatch và Amazon SNS để bảo vệ hệ thống từ lớp giao diện, xác thực người dùng, phân quyền truy cập cho đến giám sát và cảnh báo sự cố theo thời gian thực.
Việc áp dụng nguyên tắc Least Privilege cùng các cơ chế xác thực và giám sát giúp giảm thiểu rủi ro tấn công, bảo vệ tài nguyên AI có chi phí cao như Amazon Bedrock, đồng thời đảm bảo ứng dụng có thể mở rộng an toàn và hoạt động ổn định khi phục vụ số lượng lớn người dùng.
###  [Blog 3 - Cách một tổ chức cắt giảm 39% chi phí AWS trong 12 tuần](3.3-Blog3/)
Bài blog chia sẻ cách một công ty SaaS đã giảm 39% chi phí AWS chỉ trong 12 tuần bằng cách áp dụng chiến lược tối ưu chi phí theo từng giai đoạn. Thay vì thực hiện mọi thay đổi cùng lúc, đội ngũ sử dụng AWS Cost Optimization Hub và các công cụ như AWS Compute Optimizer, AWS Cost and Usage Reports (CUR) và VPC Flow Logs để xác định các hạng mục có tác động lớn nhưng rủi ro thấp, từ đó tối ưu lưu trữ, mạng, cân bằng tải và tài nguyên tính toán.
Bằng việc chuyển đổi sang EBS gp3, kích hoạt S3 Intelligent-Tiering, sử dụng VPC Gateway Endpoint for Amazon S3, hợp nhất các Network Load Balancer, tối ưu EC2 với AWS Compute Optimizer, và triển khai AWS Graviton cùng Karpenter cho Amazon EKS, tổ chức không chỉ tiết kiệm hàng chục nghìn USD mỗi tháng mà còn xây dựng được quy trình FinOps bền vững để kiểm soát chi phí lâu dài.