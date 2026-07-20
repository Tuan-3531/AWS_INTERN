---
title: "Tối ưu hóa chi phí vận hành backend qua kiến trúc serverless với AWS Lambda"
date: 2026-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

# Tối ưu hóa chi phí vận hành backend qua kiến trúc serverless với AWS Lambda

Trong quá trình xây dựng hệ thống backend, bài toán chi phí vận hành luôn là một trong những vấn đề quan trọng mà doanh nghiệp và lập trình viên cần cân nhắc. Đặc biệt với các startup hoặc hệ thống có lưu lượng truy cập chưa ổn định, việc duy trì hạ tầng truyền thống có thể gây lãng phí tài nguyên đáng kể. AWS Lambda ra đời như một giải pháp serverless giúp tối ưu hóa chi phí, tự động mở rộng và giảm gánh nặng quản trị hệ thống.

---

## 1. Bài toán chi phí của kiến trúc truyền thống

Một sai lầm phổ biến khi triển khai backend là duy trì các máy chủ EC2 hoạt động liên tục 24/7 bất kể lưu lượng thực tế.

Trong nhiều hệ thống nội bộ hoặc startup giai đoạn đầu:

- CPU thường chỉ sử dụng dưới **10–20%**
- RAM thường dư thừa trong phần lớn thời gian
- Lưu lượng truy cập không ổn định

Tuy nhiên doanh nghiệp vẫn phải trả tiền cho toàn bộ tài nguyên đã cấp phát.

Điều này dẫn đến:

- **Idle Cost** (trả tiền cho thời gian nhàn rỗi)
- Tốn công quản trị server
- Khó mở rộng khi traffic tăng đột biến
- Chi phí vận hành tăng theo số lượng máy chủ

AWS Lambda giải quyết bài toán này bằng mô hình **Serverless Compute**, nơi code chỉ chạy khi có yêu cầu thực tế.

---

## 2. Bản chất kỹ thuật của AWS Lambda

AWS Lambda là dịch vụ **Function-as-a-Service (FaaS)**.

Thay vì quản lý server, lập trình viên chỉ cần upload code và định nghĩa trigger.

Lambda có thể được kích hoạt bởi nhiều sự kiện:

- HTTP Request từ API Gateway
- Upload file lên Amazon S3
- Tin nhắn từ Amazon SQS
- Event từ Amazon EventBridge
- Thay đổi dữ liệu trong DynamoDB

Quy trình hoạt động:

```text
Event Trigger
     │
     ▼
AWS Lambda
     │
     ▼
Execute Code
     │
     ▼
Return Response