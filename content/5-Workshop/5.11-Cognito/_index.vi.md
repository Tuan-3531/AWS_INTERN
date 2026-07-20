---
title : "Cấu hình Amazon Cognito làm Authorizer"
date : 2024-01-01 
weight : 11 
chapter : false
pre : " <b> 5.11. </b> "
---


#### Bảo mật cổng API bằng Amazon Cognito
Để bảo vệ các tài nguyên API của dự án **AI Riddle Generator** khỏi các truy cập trái phép, chúng ta sẽ sử dụng dịch vụ định danh **Amazon Cognito**. Bằng cách thiết lập Cognito User Pool làm Authorizer trên API Gateway, chỉ những người dùng đã đăng nhập và có mã xác thực JWT Token hợp lệ mới có quyền gọi các API xử lý nghiệp vụ backend.

---

### Các bước cấu hình chi tiết

#### Phần A: Khởi tạo Cognito User Pool

1. Truy cập dịch vụ **Amazon Cognito** trên AWS Console, tại bảng quản trị chọn **User pools** và bấm **Create user pool**.

   ![create-userpool](/images/5-Workshop/5.11-Cognito/create-cognito.png)

2. Thiết lập ứng dụng khách:
   - Tại mục loại ứng dụng, chọn **Single-Page Application (SPA)**.
   - Nhập tên ứng dụng khách (**App client name**).
   - Chọn thuộc tính đăng nhập bằng **Email**.

   ![select-spa](/images/5-Workshop/5.11-Cognito/chon-spa-and-name.png)

3. Cấu hình các tùy chọn đăng ký & thuộc tính:
   - Tích chọn **Enable self-registration** để cho phép người dùng tự tạo tài khoản.
   - Tại phần thuộc tính bắt buộc (Required attributes), tích chọn thuộc tính **name**.
   - Bấm tiếp tục và tiến hành tạo User Pool.

   ![configure-options](/images/5-Workshop/5.11-Cognito/configure-option.png)

4. Tạo thành công Cognito User Pool. Ghi lại thông tin **User Pool ID** và **Client ID** để tích hợp vào code giao diện React/Vue của bạn.

   ![cognito-created](/images/5-Workshop/5.11-Cognito/thanh-cong-cognito.png)

---

#### Phần B: Thiết lập Authorizer trên API Gateway

1. Quay lại dịch vụ **API Gateway**, mở API `AI-Riddle-API` đã tạo ở phần trước. Tại menu bên trái chọn **Authorizers** rồi bấm **Create authorizer**.

   ![create-authorizer](/images/5-Workshop/5.11-Cognito/Create-authorizes.png)

2. Cấu hình thông tin Authorizer:
   - **Authorizer name**: Nhập tên (ví dụ: `Cognito-Auth`).
   - **Type**: Chọn **Cognito**.
   - **Cognito user pool**: Chọn vùng Region và chọn đúng User Pool vừa khởi tạo.
   - **Token source**: Điền `Authorization` (đây là header chứa ID Token mà Client gửi lên).
   - Bấm **Create authorizer**.

   ![authorizer-details](/images/5-Workshop/5.11-Cognito/chi-tiet-aut.png)

3. Tạo thành công bộ xác thực trên API Gateway.

   ![authorizer-created](/images/5-Workshop/5.11-Cognito/thanh-cong-auth-api.png)

---

#### Phần C: Kích hoạt Cognito Auth trên các API Methods

1. Chọn mục **Resources** từ menu bên trái của API Gateway, nhấn vào phương thức cần bảo vệ (Ví dụ: `POST` trong `/Create-Riddle`). Tại giao diện bên phải, chọn tab **Method request** và nhấn **Edit**.

   ![select-method-auth](/images/5-Workshop/5.11-Cognito/chon-auth-cho-api.png)

2. Tại mục cấu hình **Authorization**, thay đổi giá trị từ `NONE` sang bộ xác thực Cognito của bạn (ví dụ: `Cognito-Auth`), sau đó nhấn **Save**.

   ![save-method-auth](/images/5-Workshop/5.11-Cognito/cai-cognito-vao-api.png)

3. Cấu hình Cognito Auth đã được gán thành công vào API method.

   ![auth-linked](/images/5-Workshop/5.11-Cognito/thanh-cong-gan-auth-vao-lenh-api.png)

4. Tiến hành **Deploy API** lại lên stage `dev` để cập nhật bảo mật có hiệu lực.

Bây giờ các request gửi tới API Gateway bắt buộc phải chứa Header `Authorization` có chứa JWT Token hợp lệ từ Cognito User Pool!
