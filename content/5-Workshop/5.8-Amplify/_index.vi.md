---
title : "Đẩy giao diện lên AWS Amplify"
date : 2024-01-01 
weight : 8 
chapter : false
pre : " <b> 5.8. </b> "
---



#### Tại sao sử dụng AWS Amplify?
AWS Amplify Hosting là dịch vụ lưu trữ (hosting) web tĩnh được quản lý hoàn toàn. Dịch vụ này được chọn cho tầng trình diễn (presentation) của AI Riddle Generator vì khả năng tích hợp trực tiếp với các nhà cung cấp Git (như GitHub) để tự động thiết lập đường dẫn CI/CD. Mỗi khi có mã nguồn mới được đẩy lên nhánh Git đã chọn, Amplify sẽ tự động xây dựng và triển khai lại. Việc giữ nguyên cấu hình mặc định (default settings) là tối ưu nhất vì Amplify có khả năng tự động nhận diện các cấu hình build chuẩn của các frontend framework (React, Vue,...) và tối ưu hóa bộ nhớ đệm tài nguyên tĩnh mà không cần quản trị thủ công.

---

### Các bước triển khai chi tiết

1. Tìm kiếm và truy cập vào dịch vụ **AWS Amplify**. Nếu bạn chưa có ứng dụng nào, nhấn **Deploy an app** (hoặc **Create new app**).

   ![deploy-app](/images/5-Workshop/5.8-Amplify/deloy-app.png)

2. Chọn nguồn mã nguồn từ **GitHub** rồi bấm **Next**.

   ![select-github](/images/5-Workshop/5.8-Amplify/Chọn%20GitHub.png)

3. Tiến hành xác thực tài khoản GitHub của bạn để cho phép AWS Amplify liên kết với các repositories.

   ![auth-github](/images/5-Workshop/5.8-Amplify/Xác%20thực%20GitHub.png)

4. Chọn đúng kho chứa (repository) của dự án và chọn nhánh chứa giao diện (ví dụ: `main` hoặc nhánh giao diện riêng của bạn). Bấm **Next**.

   ![select-branch](/images/5-Workshop/5.8-Amplify/nhánh%20giao%20diện.png)

5. Tại mục cài đặt Build, **giữ nguyên giá trị mặc định**. Amplify sẽ tự động nhận diện câu lệnh build (ví dụ: `npm run build`) và thư mục đầu ra. Bấm **Next** để kiểm tra lại, và chọn **Save and deploy**.

   ![save-deploy](/images/5-Workshop/5.8-Amplify/save%20and%20deploy%20app.png)

6. Chờ vài phút để Amplify thực hiện các giai đoạn: Provision, Build, Deploy.

   ![deployed-site](/images/5-Workshop/5.8-Amplify/deploy%20ra%20trang%20web.png)

Sau khi hoàn tất, Amplify sẽ hiển thị một đường dẫn web mặc định (ví dụ: `https://main.d1w4ziet0548q.amplifyapp.com/`). Hãy lưu lại đường dẫn này để sử dụng làm gốc (origin) cho CloudFront ở bước tiếp theo.
