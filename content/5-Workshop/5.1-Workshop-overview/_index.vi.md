---
title: "Tổng quan về workshop"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### 1. Giới thiệu bài thực hành
Mục tiêu chính của bài thực hành này là giúp bạn tự tay thiết kế và cấu hình một kiến trúc Serverless hoàn chỉnh kết hợp Generative AI trên nền tảng AWS để xây dựng ứng dụng **AI Riddle Generator**. 

Qua bài thực hành, bạn sẽ nắm được cách hoạt động và khả năng tích hợp linh hoạt giữa các dịch vụ Serverless đám mây, giúp tối ưu hóa chi phí (chỉ trả tiền trên lượt request thực tế) và tăng độ an toàn an ninh mạng lên mức cao nhất.

### 2. Sơ đồ kiến trúc giải pháp
Dưới đây là sơ đồ kiến trúc tổng quan mô tả luồng đi của dữ liệu từ thiết bị đầu cuối của người dùng qua các tầng bảo mật, xử lý logic backend, gọi AI sinh câu đố và tương tác cơ sở dữ liệu:

![Sơ đồ kiến trúc toàn diện](/images/5-Workshop/5.1-Workshop-overview/1.png)

### 3. Quy trình luồng dữ liệu (Data Flow)
1. **Phân giải & Phân phối (Edge)**: Người dùng truy cập domain thông qua **Route 53**. Yêu cầu tải giao diện tĩnh được **CloudFront** CDN phân phối bảo mật qua giao thức HTTPS từ **AWS Amplify**.
2. **Lọc lưu lượng (WAF)**: Tường lửa **AWS WAF** lọc các lưu lượng request, chặn DDoS, Spam, SQL Injection và XSS.
3. **Xác thực (Cognito)**: Người dùng thực hiện đăng nhập và nhận Token JWT từ **Cognito User Pool**.
4. **Cửa ngõ API (API Gateway)**: Request gửi đến API Gateway kèm Token JWT. API Gateway kiểm tra tính hợp lệ của Token trước khi kích hoạt hàm tính toán phía sau.
5. **Xử lý tính toán (Lambda)**: Hàm **AWS Lambda** tiếp nhận dữ liệu đầu vào (Ví dụ: Từ khóa, Độ tuổi).
6. **Tích hợp Trí tuệ Nhân tạo (Bedrock)**: Lambda gửi prompt yêu cầu tới **Amazon Bedrock** để gọi mô hình AI xử lý sinh câu đố.
7. **Lưu trữ dữ liệu (DynamoDB & S3)**: Nội dung câu đố được lưu vào bảng **DynamoDB**. Mọi yêu cầu xuất file báo cáo (PDF/Word) sẽ được Lambda ghi vào **S3 Bucket** và trả về S3 Presigned URL có thời hạn ngắn để Frontend hiển thị liên kết tải về.
8. **Giám sát & Cảnh báo**: Lịch sử hoạt động và log lỗi của Lambda được đẩy về **CloudWatch**. Nếu phát hiện số lượng lỗi vượt ngưỡng, CloudWatch Alarm kích hoạt gửi tin nhắn cảnh báo qua **SNS** đến Email/Slack của quản trị viên.
