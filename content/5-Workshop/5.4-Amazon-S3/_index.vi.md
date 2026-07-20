---
title: "Thiết lập Amazon S3"
date: 2026-07-09
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---


Trong phần này, chúng ta sẽ tiến hành tạo một Amazon S3 Bucket để phục vụ cho việc lưu trữ dữ liệu của dự án.

## Bước 1: Tạo Amazon S3 Bucket

1. Truy cập vào giao diện quản lý **Amazon S3**, chọn mục **Buckets** ở thanh điều hướng bên trái và nhấn nút **Create bucket** (Tạo nhóm).
2. Trong phần **General configuration** (Cấu hình chung), thiết lập các thông tin sau:
   - **AWS Region**: Chọn khu vực `Asia Pacific (Singapore) ap-southeast-1`.
   - **Bucket type** (Loại bucket): Chọn `General purpose` (Đa dụng).
   - **Bucket namespace**: Chọn `Global namespace`.
   - **Bucket name** (Tên bucket): Nhập `ai-riddle-storage` (hoặc tên tương tự như `riddle-store` tuỳ thuộc vào quy ước đặt tên của bạn). Lưu ý rằng tên bucket phải là duy nhất trên toàn hệ thống Amazon S3.

   ![Cấu hình tạo S3 Bucket](/images/5-Workshop/5.4-Amazon-S3/8.png)

3. Giữ nguyên các cài đặt mặc định còn lại (chẳng hạn như Object Ownership, Block Public Access) để đảm bảo an toàn cho dữ liệu.
4. Cuộn xuống cuối trang và nhấn **Create bucket** để hoàn tất quá trình tạo.
5. Sau khi tạo thành công, bạn sẽ thấy bucket mới của mình (ví dụ `riddle-store` hoặc `ai-riddle-storage`) xuất hiện trong danh sách Buckets.

   ![Danh sách S3 Buckets](/images/5-Workshop/5.4-Amazon-S3/7.png)
