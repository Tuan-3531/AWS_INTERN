---
title: "Cấu hình Amazon SNS"
date: 2026-07-09
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

Thiết lập CloudWatch gửi cảnh báo tới Amazon SNS

Trong phần này, chúng ta sẽ thiết lập hệ thống cảnh báo (Alarm) trên Amazon CloudWatch. Khi hàm Lambda gặp lỗi vượt quá ngưỡng cho phép, CloudWatch sẽ kích hoạt báo động và gửi thông báo qua email sử dụng Amazon Simple Notification Service (SNS).

## Bước 1: Tạo CloudWatch Alarm

1. Truy cập vào bảng điều khiển **CloudWatch**, chọn **Alarms** -> **All alarms** ở menu bên trái và nhấn **Create alarm**.
2. Tại bước **Specify metric and conditions**:
   - Chọn **Select metric**.
   - Tìm kiếm chỉ số (metric) của Lambda: Chọn hàm `Create-Riddle` và chọn metric **Errors**.

   ![CloudWatch Alarm 1](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/12.1.png)

   - Trong phần cấu hình metric, đặt **Statistic** là `Average` và **Period** là `5 minutes` (hoặc tuỳ chỉnh theo nhu cầu giám sát của bạn).

   ![CloudWatch Alarm 2](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/12.2.png)

3. Tại phần **Conditions** (Điều kiện):
   - **Threshold type**: Chọn `Static`.
   - **Whenever Errors is**: Chọn `Greater/Equal (>= threshold)` và điền giá trị là `1`. Điều này có nghĩa là khi có từ 1 lỗi trở lên, báo động sẽ được kích hoạt.

   ![CloudWatch Alarm 3](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/13.png)

4. Nhấn **Next** để tiếp tục.

   ![CloudWatch Alarm 4](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/14.png)

## Bước 2: Cấu hình Hành động (Actions) và Amazon SNS

1. Tại bước **Configure actions**, phần **Notification**:
   - **Alarm state trigger**: Chọn `In alarm`.
   - Trong phần **Send a notification to the following SNS topic**, chọn **Create new topic**.
   - **Create a new topic**: Đặt tên là `Riddle-API-Error-Alerts`.

   ![Configure Actions 1](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/15.png)

   - **Email endpoints**: Điền địa chỉ email mà bạn muốn nhận cảnh báo (ví dụ: `dangquoctuan12t1nh2022@gmail.com`).
   - Nhấn nút **Create topic**. Topic SNS mới sẽ được tạo tự động và gán vào hành động cảnh báo này.

   ![Configure Actions 2](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/16.1.png)

2. Nhấn **Next** để sang bước tiếp theo.

## Bước 3: Đặt tên và Hoàn tất tạo Alarm

1. Tại bước **Add alarm details**, điền **Alarm name** là `Alert-Riddle-API-Error` và nhấn **Next**.

   ![Add Alarm Details](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/17.png)

2. Ở bước **Preview and create**, kiểm tra lại toàn bộ thông tin và biểu đồ (bạn sẽ thấy đường báo động màu đỏ ở mức 1). Nhấn **Create alarm** để hoàn thành.

   ![Preview and Create](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/18.png)

## Bước 4: Xác nhận đăng ký Amazon SNS (Subscription)

Sau khi tạo SNS Topic với email, AWS sẽ tự động gửi một email yêu cầu xác nhận tới địa chỉ bạn đã đăng ký.

1. Bạn có thể kiểm tra Topic và Subscriptions đã được tạo thành công trên giao diện **Amazon SNS**.

   ![SNS Topics](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/19.png)

2. Ở phần **Subscriptions**, trạng thái của email ban đầu sẽ là `Pending confirmation`.

   ![SNS Subscriptions 1](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/20.png)

3. Mở hộp thư đến của bạn, tìm email từ AWS SNS và nhấp vào liên kết **Confirm subscription**.
4. Sau khi xác nhận, trạng thái đăng ký trên Amazon SNS sẽ chuyển thành `Confirmed`. Kể từ lúc này, mọi cảnh báo từ CloudWatch sẽ được gửi thẳng tới email của bạn.

   ![SNS Subscriptions 2](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/21.png)
