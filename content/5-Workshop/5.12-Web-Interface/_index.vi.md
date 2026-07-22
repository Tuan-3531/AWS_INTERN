---
title: "Kiểm thử Giao diện & Báo cáo Sản phẩm"
date: 2024-01-01
weight: 12
chapter: false
pre: " <b> 5.12. </b> "
---

### Kiểm thử Giao diện Người dùng & Báo cáo Ứng dụng Web AI Riddle Generator

Sau khi hoàn tất cấu hình hạ tầng backend Serverless trên AWS, bước cuối cùng là truy cập và nghiệm thu ứng dụng web frontend hoàn chỉnh. 

Ứng dụng được thiết kế theo cấu trúc **Single-Page Application (SPA)** phát triển trên nền tảng **React 19** kết hợp với công cụ đóng gói **Vite 8**. Giao diện áp dụng phong cách thiết kế **Glassmorphism** (kính mờ) hiện đại, hỗ trợ hiệu ứng chuyển đổi mượt màng và giao diện tương thích tốt trên mọi thiết bị.


> **Hiện trạng tích hợp hệ thống:**
> *   **Chức năng hoạt động thực tế (Real Integration):** Các phân hệ **Đăng nhập (Cognito)**, **Tạo Câu Đố (Generator)**, và **Thư viện cá nhân (Library)** đã được tích hợp hoạt động thực tế 100%. Đăng nhập đăng ký kết nối trực tiếp AWS Cognito User Pool qua SDK và giải mã JWT token tiếng Việt; Sinh câu đố gọi trực tiếp AWS Bedrock Claude 3.5 và tự động lưu/xóa thư viện đồng bộ xuống bảng DynamoDB Single-Table thông qua các API Gateway endpoints thực tế.
> *   **Chức năng phát triển trong tương lai (Future Development):** Phân hệ **Cộng đồng (Community)** hiện tại được phát triển dưới dạng giao diện dữ liệu giả lập (Mock Data) và sẽ được tích hợp thực tế với DynamoDB Global Secondary Index (GSI) trên Cloud trong tương lai.

---

### 1. Kịch bản Kiểm thử Thực tế & Các Phân hệ Giao diện Web (Chi tiết từng bước)

Dưới đây là kịch bản chạy thử nghiệm thực tế liên tục từ đầu đến cuối (End-to-End User Journey) của ứng dụng web, kết hợp giới thiệu các phân hệ chức năng tương ứng trực tiếp dưới từng bước giao diện:

#### 1.1. Phân hệ Xác thực Người dùng (Cognito Authentication) - *Tích hợp thực tế*
*   **Mô tả chức năng:** Cổng xác thực và quản lý tài khoản người dùng kết nối trực tiếp với dịch vụ đám mây bảo mật **AWS Cognito User Pool** nhằm phục vụ việc phân quyền người dùng (gán vai trò Giáo viên - Teacher hoặc Phụ huynh - Parent) và bảo vệ an toàn các API endpoint phía sau.
*   **Trạng thái tích hợp:** Đã tích hợp thực tế thành công 100%. Luồng hoạt động hoàn toàn tự động từ đăng ký, xác thực qua email cho đến đăng nhập và đồng bộ tài khoản:
    1.  **Đăng ký tài khoản (Sign Up):** Nhập Họ tên, Email, Mật khẩu và Lựa chọn Vai trò (Giáo viên / Phụ huynh). Dữ liệu đăng ký được đẩy trực tiếp lên AWS Cognito User Pool.
    2.  **Xác minh Email (Confirm Sign Up):** Hệ thống Cognito tự động gửi email chứa mã OTP xác thực. Trang web hiển thị giao diện nhập mã gồm 6 ô số riêng biệt, hỗ trợ tự động chuyển tiếp tiêu điểm (focus shift), xóa lùi thông minh và dán mã OTP.
    3.  **Đăng nhập (Sign In):** Xác thực thông tin qua Cognito, nhận mã token JWT (ID Token, Access Token). Hệ thống giải mã JWT (tiếng Việt UTF-8 không lỗi phông chữ) để lấy thông tin cá nhân và hiển thị hồ sơ động ở góc màn hình.
    4.  **Đồng bộ DynamoDB:** Hồ sơ đăng ký sau khi xác thực sẽ tự động được ghi nhận xuống bảng DynamoDB Single-Table thông qua hàm Lambda `/riddles/profile` để lưu trữ lâu dài.
*   **Hình ảnh kiểm thử giao diện đăng nhập:**
    ![Đăng nhập Cognito](/images/5-Workshop/5.13-Web-Interface/test_login_page.png)
*   **Hình ảnh kiểm thử giao diện đăng ký tài khoản:**
    ![Đăng ký tài khoản Cognito](/images/5-Workshop/5.13-Web-Interface/register.png)
*   **Hình ảnh kiểm thử giao diện nhập mã xác thực OTP:**
    ![Xác thực mã OTP](/images/5-Workshop/5.13-Web-Interface/verify.png)
*   **Hình ảnh kiểm thử đăng nhập thành công:**
    Góc phải màn hình hiển thị chính xác thông tin hồ sơ người dùng thực tế vừa đăng nhập cùng vai trò tương ứng để phân quyền:
    ![Đăng nhập thành công](/images/5-Workshop/5.13-Web-Interface/test_login_success.png)

#### 1.2. Phân hệ Tạo Câu Đố (Generator Hub) - *Tích hợp thực tế*
*   **Mô tả chức năng:** Nơi người dùng (Giáo viên, Phụ huynh) tương tác trực tiếp với mô hình Trí tuệ nhân tạo (AI). Người dùng có thể cấu hình độ tuổi (Cấp 1, Cấp 2, Cấp 3), thể loại (Acrostic, Lịch sử - Văn học, Khoa học - Toán học), chủ đề và nhập "Từ khóa" đáp án.
*   **Trạng thái tích hợp:** Chức năng hoạt động thực tế, tích hợp trực tiếp qua API Gateway để gọi AWS Lambda và Amazon Bedrock LLM sinh câu đố theo từ khóa. Kết quả trả về gồm bài thơ đố, các gợi ý (khái quát/chi tiết) và đáp án ẩn dưới dạng collapsible. Nút "Lưu Vào Thư Viện" cho phép lưu cục bộ câu đố này vào LocalStorage của trình duyệt (giả lập thao tác lưu vào DynamoDB Table).
*   **Hình ảnh kiểm thử cấu hình đầu vào:**
    Nhập từ khóa `"Trái Đất"`, chọn chủ đề Địa lý, nhóm tuổi Cấp 1:
    ![Nhập từ khóa](/images/5-Workshop/5.13-Web-Interface/test_create_riddle_input.png)
*   **Hình ảnh kiểm thử kết quả tạo câu đố:**
    Hệ thống hiển thị câu đố thơ Acrostic sinh từ AI dưới định dạng Glassmorphism sang trọng với các gợi ý và đáp án ban đầu được ẩn kín:
    ![Kết quả tạo câu đố](/images/5-Workshop/5.13-Web-Interface/test_create_riddle_result.png)
*   **Hình ảnh kiểm thử gợi ý khái quát:**
    Khi nhấp chuột vào collapsible **💡 Gợi ý bước 1 (Khái quát)** để mở rộng khung nội dung gợi ý:
    ![Hiển thị Gợi ý 1](/images/5-Workshop/5.13-Web-Interface/test_create_riddle_hint.png)
*   **Hình ảnh kiểm thử đáp án cuối cùng:**
    Khi nhấp chuột vào collapsible **🏆 XEM ĐÁP ÁN CUỐI CÙNG** để hiển thị chính xác từ khóa đáp án:
    ![Hiển thị Đáp án](/images/5-Workshop/5.13-Web-Interface/test_create_riddle_answer.png)
*   **Hình ảnh kiểm thử lưu trữ câu đố:**
    Giáo viên click chọn **Lưu Vào Thư Viện**, dữ liệu được lưu xuống LocalStorage, nút lưu chuyển sang trạng thái đã lưu: `⭐ Đã Lưu Vào Thư Viện`.
    ![Lưu câu đố thành công](/images/5-Workshop/5.13-Web-Interface/test_create_riddle_saved.png)

#### 1.3. Phân hệ Thư Viện Của Tôi (Personal Library) - *Tích hợp thực tế*
*   **Mô tả chức năng:** Kho lưu trữ cá nhân hiển thị danh sách câu đố của tài khoản đang đăng nhập (truy vấn trực tiếp từ bảng DynamoDB theo khóa PK `USER#<user_id>` và SK bắt đầu bằng `RIDDLE#`). Cho phép người dùng xuất tài liệu in ấn PDF/Word.
*   **Trạng thái tích hợp:** Đã tích hợp thực tế. Các hành động Lưu câu đố (POST), Tải danh sách thư viện cá nhân (GET), và Xóa câu đố khỏi thư viện (DELETE) được đồng bộ hóa trực tiếp xuống bảng DynamoDB Single-Table thiết lập trên Cloud AWS thông qua REST API Gateway và Lambda.
*   **Hình ảnh kiểm thử danh sách thư viện:**
    Hiển thị danh sách câu đố đã lưu cục bộ (bao gồm câu đố `"Trái Đất"` vừa tạo):
    ![Thư viện câu đố cá nhân](/images/5-Workshop/5.13-Web-Interface/test_library_list.png)
*   **Hình ảnh kiểm thử xuất bản in ấn PDF:**
    Nhấn nút **🖨️ Xuất PDF / In câu đố** trên thẻ câu đố `"Trái Đất"` để mở hộp thoại in tài liệu / preview PDF chuẩn hóa phục vụ việc giảng dạy trực tiếp:
    ![Xuất tài liệu in ấn PDF](/images/5-Workshop/5.13-Web-Interface/test_library_export.png)
    ![Xem trước bản in PDF](/images/5-Workshop/5.13-Web-Interface/test_library_export_PDF.png)

#### 1.4. Phân hệ Cộng Đồng (Community Board) - *Phát triển trong tương lai*
*   **Mô tả chức năng:** Bảng tin hiển thị các câu đố được chia sẻ công khai bởi các thành viên, sắp xếp theo số lượt bình chọn (Upvotes) giảm dần thông qua DynamoDB GSI1. Hỗ trợ học sinh giải đố tương tác trực tiếp và người dùng bình chọn (upvote) câu đố hay.
*   **Trạng thái tích hợp:** Hoạch định phát triển trong tương lai. Giao diện hiện tại dùng dữ liệu cứng (mock data) để mô phỏng tương tác giải đố và bình chọn (upvote) trên bộ nhớ cục bộ.
*   **Hình ảnh kiểm thử giải đố chính xác:**
    Nhập đáp án đúng `"Bánh Chưng"` và bấm **Đoán**:
    ![Đoán đúng đáp án](/images/5-Workshop/5.13-Web-Interface/test_community_guess_correct.png)
*   **Hình ảnh kiểm thử giải đố chưa chính xác:**
    Nhập đáp án sai `"Bánh dày"` và bấm **Đoán**:
    ![Đoán sai đáp án](/images/5-Workshop/5.13-Web-Interface/test_community_guess_incorrect.png)
*   **Hình ảnh kiểm thử bình chọn câu đố (Upvoting):**
    Bấm biểu tượng 👍 ở góc trên thẻ câu đố Bánh Chưng. Điểm upvotes tăng từ 56 lên 57 ở bộ nhớ cục bộ:
    ![Upvote thành công](/images/5-Workshop/5.13-Web-Interface/test_community_upvote.png)

#### 1.5. Giao diện phụ trợ: Chế độ Tối (Dark Mode)
*   Ứng dụng hỗ trợ Dark Mode đầy đủ thông qua CSS Variables để giảm mỏi mắt khi dùng vào ban đêm:
    ![Dark Mode View](/images/5-Workshop/5.13-Web-Interface/riddle_dark_mode.png)

---

### 2. Kế hoạch Phát triển trong Tương lai (Future Development)

Để mở rộng dự án hơn nữa trong tương lai, các nhiệm vụ cần triển khai bao gồm:

*   **Tích hợp API Gateway thực tế cho Cộng đồng:** Chuyển đổi cơ chế cộng đồng và upvote cục bộ sang kết nối API Gateway và Lambda, lưu trữ trực tiếp các lượt upvotes của người dùng thông qua DynamoDB Transaction để đảm bảo tính toàn vẹn dữ liệu.
*   **Hiện thực hóa luồng xuất file S3 Presigned URL thật:** Tích hợp hàm Lambda backend xuất file PDF/Word kết nối với S3 bucket thực tế để sinh và trả về Presigned URL có thời hạn cho phép tải xuống tài liệu thật, thay vì giao diện in ấn mô phỏng hiện tại.
*   **Tính năng Game hóa (Gamification) tương tác:** Thiết kế bảng xếp hạng (Leaderboard) thời gian thực và chế độ giải đố tính giờ cho trẻ em.
*   **Dashboard Phân tích cho Giáo viên (Analytics Dashboard):** Cấu hình AWS QuickSight hoặc xây dựng Dashboard biểu đồ ngay trên frontend hiển thị thống kê độ tuổi học sinh truy cập, chủ đề câu đố được tạo nhiều nhất và tỷ lệ giải đố thành công.

---

### 3. Đánh giá Tổng kết Sản phẩm

*   **Tính toàn vẹn của Kiến trúc (End-to-End Serverless):** Giao diện liên kết mượt mà với các giả lập thiết kế Single-Table Design trong DynamoDB, các luồng bảo mật của Cognito, và quản lý S3, phản ánh đúng logic nghiệp vụ được cấu hình trên AWS Cloud.
*   **Trải nghiệm Người dùng (UX/UI):** Phong cách Glassmorphism sang trọng, trực quan, dễ sử dụng cho các đối tượng giáo viên và phụ huynh. Thời gian phản hồi và chuyển đổi trang gần như tức thời nhờ cơ chế Single-Page Application.
*   **Khả năng mở rộng:** Cấu trúc mã nguồn React được mô đun hóa cao, dễ dàng mở rộng thêm các tính năng như trò chơi giải đố tương tác trực tiếp hoặc tính năng chấm điểm học sinh trong tương lai.

---

*   **Link bài viết số 1:** [Facebook Post #1](https://web.facebook.com/groups/660548818043427/user/100029043690648)
*   **Link bài viết số 2:** [Facebook Post #2](https://www.facebook.com/share/p/1ENb8bUptS/)
*   **Link bài viết số 3:** [Facebook Post #3](https://www.facebook.com/groups/660548818043427/user/100015108252190)
