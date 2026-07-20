---
title: "Dọn dẹp tài nguyên"
date: 2024-01-01
weight: 12
chapter: false
pre: " <b> 5.12. </b> "
---

### Dọn dẹp Tài nguyên trên AWS Đám mây để tránh phát sinh chi phí

Sau khi hoàn thành chuỗi bài thực hành này, hãy dọn dẹp các tài nguyên đã cấu hình trên tài khoản AWS của bạn để tránh các chi phí phát sinh không mong muốn (chủ yếu là phí duy trì Amplify Hosting, WAF rules hoặc lưu trữ dữ liệu S3/DynamoDB).

Thực hiện xóa các tài nguyên theo thứ tự sau:

---

### 1. Xóa CloudWatch Alarm và SNS Topic
1. Truy cập **CloudWatch Console** -> **Alarms** -> **All alarms** -> Chọn alarm `generateRiddle-HighErrorRate` -> Nhấp **Action** -> Chọn **Delete**.
2. Truy cập **SNS Console** -> **Topics** -> Chọn topic `RiddleAppAlerts` -> Nhấp chọn **Delete**. Đồng thời xóa các Subscription liên kết.

---

### 2. Xóa AWS Amplify App và Route 53 DNS records
1. Truy cập **AWS Amplify Console** -> Chọn ứng dụng Frontend -> **Actions** -> Chọn **Delete app**.
2. Truy cập **Route 53 Console** (nếu cấu hình tên miền riêng) -> Xóa các bản ghi CNAME hoặc A Record trỏ về Amplify.

---

### 3. Xóa AWS WAF Web ACL
1. Truy cập **AWS WAF Console** -> **Web ACLs** -> Chọn ACL đã tạo -> Chọn **Associated AWS resources** -> Gỡ bỏ liên kết với CloudFront/Amplify.
2. Quay lại danh sách ACLs, chọn ACL đó và nhấn **Delete**.

---

### 4. Xóa Amazon API Gateway
1. Truy cập **API Gateway Console** -> Chọn API `RiddleHTTPAPI` -> Nhấp **Actions** -> Chọn **Delete**.

---

### 5. Xóa Amazon Cognito User Pool
1. Truy cập **Cognito Console** -> Chọn User Pool `RiddleAppUserPool` -> Nhấp chọn **Delete**.

---

### 6. Xóa AWS Lambda và IAM Role/Policy
1. Truy cập **AWS Lambda Console** -> Chọn hàm `generateRiddle` -> Nhấp **Actions** -> Chọn **Delete**.
2. Truy cập **IAM Console**:
    *   **Roles**: Tìm `LambdaRiddleRole` và nhấn **Delete**.
    *   **Policies**: Tìm `LambdaRiddlePolicy` và nhấn **Delete**.

---

### 7. Xóa Amazon DynamoDB Table
1. Truy cập **DynamoDB Console** -> **Tables** -> Chọn bảng `Riddles` -> Nhấn chọn **Delete**. Nhập dòng xác nhận để xóa hoàn toàn dữ liệu.

---

### 8. Xóa Amazon S3 Bucket
1. Truy cập **S3 Console** -> Chọn bucket `riddle-document-exports-107204`.
2. Do S3 yêu cầu bucket phải trống trước khi xóa, nhấp chọn **Empty** để xóa tất cả các tệp tài liệu bên trong.
3. Sau khi bucket rỗng, quay lại danh sách chọn bucket đó và nhấp **Delete**. Nhập tên bucket để xác nhận xóa vĩnh viễn.

Chúc mừng bạn đã hoàn thành xuất sắc toàn bộ bài thực hành xây dựng ứng dụng AI Riddle Generator trên AWS Serverless!
