---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Xây dựng ứng dụng AI Riddle Generator trên nền tảng AWS Serverless

#### Tổng quan

Trong loạt bài thực hành (Workshop) này, bạn sẽ được hướng dẫn từng bước (step-by-step) để tự tay xây dựng một ứng dụng AI tạo câu đố cho trẻ em (**AI Riddle Generator**). Hệ thống sẽ ứng dụng mô hình Trí tuệ nhân tạo tạo sinh (Generative AI) trên nền tảng điện toán đám mây AWS với kiến trúc **Serverless** hoàn chỉnh.

Chúng ta sẽ tìm hiểu và cấu hình toàn bộ các dịch vụ từ giao diện người dùng, định danh bảo mật, xử lý backend, tích hợp AI, cơ sở dữ liệu, cho đến khâu giám sát và cảnh báo tự động:

*   **Hosting & CDN**: Phân phối giao diện web qua AWS Amplify.
*   **Security (WAF & IAM)**: Lọc request độc hại bằng Web Application Firewall, phân quyền tối thiểu bằng IAM Role.
*   **Authentication & API**: Quản lý định danh qua Cognito User Pool và định tuyến API qua API Gateway HTTP API.
*   **Backend & AI Layer**: Xử lý logic qua AWS Lambda và gọi mô hình AI sinh câu đố qua Amazon Bedrock.
*   **Storage & Database**: Lưu trữ dữ liệu câu đố trên Amazon DynamoDB và xuất báo cáo PDF/Word thông qua Amazon S3 Presigned URL.
*   **Monitoring**: Giám sát lỗi tập trung bằng CloudWatch và bắn cảnh báo an toàn qua Amazon SNS.

#### Nội dung các chương

1. [Chương 5.1: Tổng quan về workshop & Kiến trúc hệ thống](5.1-workshop-overview/)
2. [Chương 5.2: Chuẩn bị tài nguyên](5.2-prerequiste/)
3. [Chương 5.3: Thiết lập Amazon DynamoDB](5.3-amazon-dynamodb/)
4. [Chương 5.4: Thiết lập Amazon S3](5.4-amazon-s3/)
5. [Chương 5.5: Triển khai AWS Lambda & Cấu hình IAM](5.5-aws-lambda/)
6. [Chương 5.6: Thiết lập CloudWatch cho AWS Lambda](5.6-lamda-to-cloudwatch/)
7. [Chương 5.7: Cấu hình Amazon SNS & Alarm Cảnh báo](5.7-cloudwatch-to-amazonsns/)
8. [Chương 5.8: Đẩy giao diện lên AWS Amplify](5.8-amplify/)
9. [Chương 5.9: Kích hoạt CloudFront, WAF & Route 53](5.9-cloudfront/)
10. [Chương 5.10: Khởi tạo API Gateway và liên kết](5.10-apigateway/)
11. [Chương 5.11: Cấu hình Amazon Cognito làm Authorizer](5.11-cognito/)
12. [Chương 5.12: Dọn dẹp tài nguyên trên AWS](5.12-cleanup/)