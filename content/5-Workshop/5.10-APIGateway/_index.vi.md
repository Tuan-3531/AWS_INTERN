---
title : "Khởi tạo API Gateway và liên kết"
date : 2024-01-01 
weight : 10 
chapter : false
pre : " <b> 5.10. </b> "
---


#### Kết nối Giao diện với Backend Serverless
Trong phần cuối cùng của bài lab, bạn sẽ cấu hình một API endpoint không máy chủ trên AWS API Gateway để nhận các yêu cầu HTTP từ giao diện Amplify và định tuyến chúng đến các hàm xử lý backend AWS Lambda.

Chúng ta áp dụng các chuẩn kiến trúc tối ưu sau:
- **REST API thay vì HTTP API**: Chúng ta chọn REST API vì nó hỗ trợ nhiều tính năng quản trị nâng cao cần thiết cho dự án như biến môi trường theo stage, xác thực chứng chỉ client, ánh xạ request nâng cao và tích hợp trực tiếp tường lửa WAF.
- **Lambda Proxy Integration (Tích hợp ủy quyền)**: Chúng ta bật tính năng này. Đây là lựa chọn tối ưu mặc định vì nó chuyển tiếp trực tiếp toàn bộ yêu cầu HTTP thô (headers, body, query strings) sang cho Lambda xử lý. Hàm Lambda sẽ tự giải mã tham số và quyết định mã phản hồi HTTP trả về ngay trong code, giúp giảm thiểu đáng kể việc cấu hình thủ công phức tạp trên API Gateway.
- **Kích hoạt CORS**: Bật CORS (Cross-Origin Resource Sharing) là yêu cầu bắt buộc vì các trình duyệt web ngăn chặn các đoạn mã chạy từ một tên miền (Amplify/CloudFront) gửi yêu cầu gọi API sang một tên miền khác (API Gateway) trừ khi API Gateway cấu hình cho phép điều đó.

---

### Các bước cấu hình chi tiết

#### Phần A: Thiết lập API Gateway

1. Tìm kiếm và truy cập dịch vụ **API Gateway**, nhấn chọn **Create API**.

   ![create-api](/images/5-Workshop/5.10-APIGateway/Create%20API.png)

2. Kéo xuống tìm loại **REST API** (Lưu ý: Không chọn loại REST API Private) và nhấn chọn **Build**.

   ![rest-api](/images/5-Workshop/5.10-APIGateway/Rest%20API.png)

3. Cấu hình thông tin API:
   - **API name**: `AI-Riddle-API`
   - **Endpoint Type**: Chọn **Regional** hoặc **Edge-optimized**.
   - Bấm **Create API**.

   ![api-config](/images/5-Workshop/5.10-APIGateway/AI-Riddle-API.png)

4. Sau khi tạo thành công, bạn sẽ được chuyển đến trang quản trị tài nguyên.

   ![api-created](/images/5-Workshop/5.10-APIGateway/thành%20công%20API.png)

5. Nhấn nút **Create resource** để tạo các đường dẫn endpoint cho ứng dụng.

   ![create-resource](/images/5-Workshop/5.10-APIGateway/Create%20resoure.png)

6. Tạo lần lượt các đường dẫn (paths) theo cấu trúc logic dự án:
   - `/Create-Riddle`
   - `/riddles`
   - `/export`
   - `/generate`

   ![api-paths](/images/5-Workshop/5.10-APIGateway/API%20theo%20cấu%20trúc.png)

7. Chọn đường dẫn tương ứng (Ví dụ: `/Create-Riddle`) và nhấn chọn **Create method**.

   ![create-method](/images/5-Workshop/5.10-APIGateway/create%20method.png)

8. Thiết lập thông số cho method:
   - **Method type**: Chọn `POST`.
   - **Integration type**: Chọn **Lambda function**.
   - **Lambda proxy integration**: Tích chọn ô vuông để kích hoạt.
   - Chọn đúng Region và tìm hàm Lambda xử lý nghiệp vụ tương ứng đã được code sẵn.
   - Nhấn **Create method**.

   ![method-integration](/images/5-Workshop/5.10-APIGateway/thiết%20lập%20method.png)
   ![method-region](/images/5-Workshop/5.10-APIGateway/thiết%20lập%20method%202.png)

9. Bạn sẽ thấy method được tạo thành công bên dưới cấu trúc thư mục.

   ![method-created](/images/5-Workshop/5.10-APIGateway/thành%20công%20method%20API.png)

10. Chọn đường dẫn resource và kích hoạt nút **Enable CORS** từ thanh tác vụ.

     ![enable-cors](/images/5-Workshop/5.10-APIGateway/enable%20CORS.png)

11. Tích chọn các phương thức (ví dụ: `POST` và `OPTIONS`) rồi nhấn chọn **Save** để lưu.

     ![cors-settings](/images/5-Workshop/5.10-APIGateway/opption%20CORS.png)

12. Chọn tác vụ **Deploy API** để xuất bản API của bạn.

     ![deploy-api](/images/5-Workshop/5.10-APIGateway/Deploy%20API.png)

13. Tại mục Stage, chọn **New stage** (hoặc chọn stage có sẵn), đặt tên stage (ví dụ: `dev`) rồi nhấn **Deploy**.

     ![stage-deploy](/images/5-Workshop/5.10-APIGateway/new%20state%20dev.png)

14. Sao chép lại địa chỉ **Invoke URL** được sinh ra (ví dụ: `https://44vwnl4k95.execute-api.ap-southeast-1.amazonaws.com/dev`).

---

#### Phần B: Liên kết API Gateway sang AWS Amplify

1. Quay lại bảng quản trị **AWS Amplify**, chọn ứng dụng giao diện của bạn, tại thanh bên trái chọn mục **Hosting** -> **Environment variables**.

   ![amplify-vars](/images/5-Workshop/5.10-APIGateway/Environment%20Variables.png)

2. Nhấn chọn nút **Manage variables** rồi chọn **Add variable**.

   ![manage-vars](/images/5-Workshop/5.10-APIGateway/appsetting.png)

3. Nhập biến môi trường để ánh xạ sang mã nguồn giao diện:
   - **Key**: Tên biến cấu hình trong code frontend của bạn (ví dụ: `VITE_API_URL` hoặc `REACT_APP_API_URL`).
   - **Value**: Địa chỉ Invoke URL của API Gateway đã copy ở bước trước.
   - Bấm **Save** để lưu lại.

   ![save-var](/images/5-Workshop/5.10-APIGateway/value%20theo%20state.png)

4. Thực hiện chạy lại một lượt deploy mới trên AWS Amplify.

Giao diện web của bạn lúc này đã được kết nối hoàn chỉnh với dịch vụ API Gateway ở phía backend!
