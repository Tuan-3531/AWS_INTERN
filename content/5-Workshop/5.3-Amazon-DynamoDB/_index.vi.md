---
title: "Thiết lập Amazon DynamoDB"
date: 2026-07-09
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---


Trong phần này, chúng ta sẽ tiến hành tạo bảng Amazon DynamoDB và cấu hình Global Secondary Index (GSI).

## Bước 1: Tạo bảng DynamoDB (Create table)

1. Truy cập vào giao diện quản lý **Amazon DynamoDB**, chọn mục **Tables** ở thanh điều hướng bên trái và nhấn **Create table**.
2. Trong phần **Table details** (Chi tiết bảng), điền các thông tin sau:
   - **Table name** (Tên bảng): `AiRiddleGenerator_Core`
   - **Partition key**: `PK` (Kiểu dữ liệu: **String**)
   - **Sort key**: `SK` (Kiểu dữ liệu: **String**)

   ![Tạo bảng DynamoDB 1](/images/5-Workshop/5.3-Amazon-DynamoDB/1.png)

3. Trong phần **Table settings** (Cài đặt bảng):
   - Chọn **Customize settings** (Tùy chỉnh cài đặt).
   - **Table class** (Lớp bảng): Chọn **DynamoDB Standard**.
   - **Capacity mode** (Chế độ dung lượng): Chọn **On-demand** (Theo nhu cầu).

   ![Tạo bảng DynamoDB 2](/images/5-Workshop/5.3-Amazon-DynamoDB/2.png)

4. Nhấn nút **Create table** để hoàn tất.
5. Sau khi bảng được tạo thành công, trạng thái của bảng sẽ hiển thị là **Active**.

   ![Tạo bảng DynamoDB thành công](/images/5-Workshop/5.3-Amazon-DynamoDB/3.png)

## Bước 2: Tạo Global Secondary Index (GSI)

Để hỗ trợ truy vấn các câu đố nổi bật, chúng ta sẽ tạo một Global Secondary Index (GSI).

1. Nhấp vào tên bảng `AiRiddleGenerator_Core` vừa tạo.
2. Chuyển sang tab **Indexes** (Chỉ mục) và nhấn **Create index**.

   ![Tạo GSI 1](/images/5-Workshop/5.3-Amazon-DynamoDB/4.png)

3. Trong phần **Index details**, cấu hình như sau:
   - **Index name** (Tên chỉ mục): `GSI1_FeaturedRiddles`
   - **Partition key**: `GSI1PK` (Kiểu dữ liệu: **String**)
   - **Sort key**: `GSI1SK` (Kiểu dữ liệu: **String**)
4. Trong phần **Attribute projections** (Dự phóng thuộc tính):
   - Chọn **Include** (Chỉ bao gồm các thuộc tính được chỉ định).
   - Nhấn **Add a new attribute** để thêm lần lượt các thuộc tính sau:
     - `riddle_id`
     - `keyword`
     - `riddle_content`
     - `upvotes`
     - `genre`

   ![Tạo GSI 2](/images/5-Workshop/5.3-Amazon-DynamoDB/5.png)

5. Nhấn nút **Create index**. Yêu cầu tạo chỉ mục sẽ được gửi đi, và trạng thái của GSI lúc này sẽ là **Creating** và chuyển thành **Active** sau ít phút.

   ![Tạo GSI thành công](/images/5-Workshop/5.3-Amazon-DynamoDB/6.png)
