---
title: "Thiết lập CloudWatch cho AWS Lambda"
date: 2026-07-09
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---


Trong phần này, chúng ta sẽ tìm hiểu về kiến trúc giám sát và tiến hành thiết lập bảng điều khiển (Dashboard) trên Amazon CloudWatch để theo dõi hoạt động của AWS Lambda.

## Kiến trúc giám sát

Dưới đây là sơ đồ kiến trúc thể hiện cách luồng dữ liệu giám sát hoạt động:

![Kiến trúc Lambda to CloudWatch](/images/5-Workshop/5.6-Lamda-to-CloudWatch/lamdatocloudwatch.jpg)

- **AWS Lambda**: Tự động đẩy các Logs & Metrics (Nhật ký và Số liệu) về CloudWatch.
- **Amazon CloudWatch**: Đóng vai trò giám sát, lưu trữ log và có thể thiết lập Kích hoạt báo lỗi (Alarm) gửi tới Amazon SNS khi có sự cố.
- **AWS IAM**: Đảm bảo cấp quyền thực thi tối thiểu cho các dịch vụ giao tiếp an toàn với nhau.
- **Amazon SNS**: Nhận các cảnh báo lỗi từ CloudWatch.

## Bước 1: Tạo Dashboard giám sát

1. Truy cập vào dịch vụ **CloudWatch**, chọn **Dashboards** (Bảng điều khiển) từ menu bên trái và tạo mới với tên `AI-Riddle-System-Monitor`.
2. Tại bảng điều khiển trống, nhấn vào nút **Add a first widget** để thêm biểu đồ đầu tiên.

![CloudWatch Dashboard](/images/5-Workshop/5.6-Lamda-to-CloudWatch/9.png)

## Bước 2: Cấu hình Widget biểu đồ

1. Trong hộp thoại **Add widget** (Thêm tiện ích):
   - **Data sources types**: Chọn **Cloudwatch**.
   - **Data type**: Chọn **Metrics**.
   - **Widget type**: Chọn biểu đồ dạng đường **Line**.
2. Nhấn nút **Next** để tiếp tục.

![Cấu hình Widget CloudWatch](/images/5-Workshop/5.6-Lamda-to-CloudWatch/10.png)

## Bước 3: Thêm chỉ số (Metrics) của Lambda

1. Tại màn hình cấu hình biểu đồ, tìm kiếm và chọn các chỉ số (metrics) của Lambda function `Create-Riddle`.
2. Tích chọn các chỉ số quan trọng sau để hiển thị trên biểu đồ:
   - **Errors** (Lỗi)
   - **Duration** (Thời gian thực thi)
   - **Invocations** (Số lượt gọi)
3. Cuối cùng, nhấn nút **Create widget** để hoàn tất và biểu đồ sẽ được hiển thị trên Dashboard của bạn.

![Thêm Metrics cho Widget](/images/5-Workshop/5.6-Lamda-to-CloudWatch/11.png)
