---
title: "Kiểm thử Giao diện & Báo cáo Sản phẩm"
date: 2024-01-01
weight: 12
chapter: false
pre: " <b> 5.12. </b> "
---

### Kiểm thử Giao diện Người dùng & Báo cáo Ứng dụng Web AI Riddle Generator

Sau khi hoàn tất cấu hình toàn bộ hạ tầng backend Serverless trên AWS (bao gồm DynamoDB, S3, Lambda, API Gateway, Cognito, và Amplify), bước cuối cùng là truy cập và nghiệm thu ứng dụng web frontend hoàn chỉnh. 

Ứng dụng được thiết kế theo cấu trúc **Single-Page Application (SPA)** phát triển trên nền tảng **React 19** kết hợp với công cụ đóng gói siêu tốc **Vite 8**. Giao diện áp dụng phong cách thiết kế **Glassmorphism** (giao diện kính mờ) hiện đại, hỗ trợ đầy đủ các hiệu ứng chuyển đổi mượt mà và khả năng đáp ứng (responsive layout) tốt trên mọi thiết bị.

---

### 1. Chi tiết Các Phân hệ Chức năng trên Giao diện

Ứng dụng web được chia thành các phân hệ chức năng tương ứng với các menu điều hướng chính:

#### 1.1. Phân hệ Tạo Câu Đố (Generator Hub)
Nơi người dùng (Giáo viên, Phụ huynh) tương tác trực tiếp với mô hình Trí tuệ nhân tạo (AI):
*   **Các bộ lọc cấu hình:** 
    *   *Độ tuổi:* Phân loại theo cấp học (Cấp 1, Cấp 2, Cấp 3) để AI tự điều chỉnh từ vựng và độ khó phù hợp với tư duy trẻ em.
    *   *Thể loại câu đố:* Hỗ trợ câu đố ghép chữ đầu từ khóa (Acrostic) hoặc câu đố tư duy logic (Lịch sử - Văn học, Khoa học - Toán học).
    *   *Chủ đề:* Địa lý, Khoa học, Lịch sử, Động vật, Thực vật...
*   **Cơ chế sinh câu đố:** Người dùng nhập một "Từ khóa" (Keyword). Khi nhấn nút tạo, hệ thống gửi payload đến API Gateway. Trình diễn kết quả bao gồm: nội dung câu đố, các gợi ý (Hint 1, Hint 2) và Đáp án được che giấu dưới dạng collapsible (nhấp vào để hiển thị).
*   **Tính năng lưu trữ:** Nếu đã đăng nhập, nút **"Lưu Vào Thư Viện"** sẽ xuất hiện cho phép lưu trữ câu đố trực tiếp vào cơ sở dữ liệu DynamoDB.

#### 1.2. Phân hệ Cộng Đồng (Community Board)
Bảng tin hiển thị các câu đố được chia sẻ công khai bởi các thành viên:
*   **Sắp xếp nổi bật (Featured Sorting):** Áp dụng cấu trúc chỉ mục phụ **GSI1 (Global Secondary Index)** trong DynamoDB để sắp xếp các câu đố theo số lượt bình chọn (Upvotes) giảm dần.
*   **Bình chọn (Upvoting):** Cơ chế kiểm soát chặt chẽ, người dùng đăng nhập chỉ được bình chọn tối đa một lần cho mỗi câu đố. Lượt bình chọn sẽ được cập nhật thời gian thực lên giao diện.
*   **Sao chép nhanh:** Cho phép người dùng lưu nhanh bất kỳ câu đố cộng đồng nào về thư viện cá nhân của mình.

#### 1.3. Phân hệ Thư Viện Của Tôi (Personal Library)
Kho lưu trữ cá nhân của riêng tài khoản đang đăng nhập:
*   **Quản lý danh sách:** Truy vấn DynamoDB theo khóa phân vùng (Partition Key - PK) `USER#<UserId>` và bắt đầu bằng khóa sắp xếp (Sort Key - SK) `RIDDLE#` để lấy ra danh sách câu đố mà tài khoản đó tự tạo.
*   **Xuất tài liệu (Document Export):** Nút xuất file PDF/Word liên kết trực tiếp với Amazon S3. Khi người dùng click, hệ thống yêu cầu một **S3 Presigned URL** có thời hạn ngắn để tải xuống tài liệu câu đố chất lượng cao, phục vụ cho việc in ấn giảng dạy trên lớp.

#### 1.4. Phân hệ Xác thực Người dùng (Cognito Authentication)
Cổng đăng nhập và đăng ký tích hợp hệ thống bảo mật:
*   **Đăng ký tài khoản:** Phân chia rõ ràng vai trò **Giáo viên (Teacher)** hoặc **Phụ huynh (Parent)** nhằm gán các quyền IAM (Identity and Access Management) tương thích.
*   **Giả lập đăng nhập nhanh:** Cung cấp các cấu hình tài khoản mẫu (Teacher Nguyễn Thị Mai, Parent Trần Văn Thu) để thuận tiện cho việc kiểm thử phân quyền ngay trên trình duyệt.

---

### 2. Kịch bản Kiểm thử Thực tế Giao diện Web (Chi tiết từng bước)

Dưới đây là kịch bản chạy thử nghiệm thực tế liên tục từ đầu đến cuối (End-to-End User Journey) của ứng dụng web được ghi nhận trực tiếp từ môi trường chạy thử nghiệm:

#### 2.1. Bước 1: Khởi động & Luồng Đăng nhập (Authentication Flow)
1. **Truy cập trang đăng nhập:** Người dùng truy cập trang chủ và nhấn chọn nút **Đăng nhập** ở góc trên bên phải. Hệ thống chuyển sang giao diện xác thực tích hợp AWS Cognito.
   ![Đăng nhập Cognito](/images/5-Workshop/5.12-Web-Interface/test_login_page.png)
2. **Xác thực nhanh:** Sử dụng cấu hình tài khoản Giáo viên mẫu `"Cô Nguyễn Thị Mai"` để thực hiện luồng đăng nhập giả lập.
3. **Đăng nhập thành công:** Sau 2 giây, hệ thống trả về Token JWT và chuyển hướng về trang chủ. Góc phải màn hình hiển thị thông tin hồ sơ của tài khoản đang hoạt động.
   ![Đăng nhập thành công](/images/5-Workshop/5.12-Web-Interface/test_login_success.png)

#### 2.2. Bước 2: Tạo Câu Đố Với AI (Riddle Creation)
1. **Nhập tham số cấu hình:** Tại tab **Tạo Câu Đố**, chúng tôi chọn chủ đề "Địa lý", cấp học "Cấp 1" và nhập từ khóa đáp án là `"Trái Đất"`.
   ![Nhập từ khóa](/images/5-Workshop/5.12-Web-Interface/test_create_riddle_input.png)
2. **Kích hoạt AI tạo câu đố:** Nhấn chọn nút **⚡ Tạo Câu Đố Với AI**. Hệ thống gửi yêu cầu qua API Gateway đến AWS Lambda để kích hoạt Amazon Bedrock LLM sinh câu đố thơ chữ đầu (Acrostic).
3. **Hiển thị kết quả ban đầu:** Câu đố thơ Acrostic được kết xuất dưới định dạng Glassmorphism sang trọng với các gợi ý và đáp án ban đầu được ẩn kín.
   ![Kết quả tạo câu đố](/images/5-Workshop/5.12-Web-Interface/test_create_riddle_result.png)
4. **Mở gợi ý khái quát:** Nhấp chuột vào collapsible **💡 Gợi ý bước 1 (Khái quát)** để hiển thị gợi ý gợi mở tư duy đầu tiên cho trẻ.
   ![Hiển thị Gợi ý 1](/images/5-Workshop/5.12-Web-Interface/test_create_riddle_hint.png)
5. **Mở đáp án cuối cùng:** Nhấp chuột vào collapsible **🏆 XEM ĐÁP ÁN CUỐI CÙNG** để hiển thị chính xác từ khóa đáp án `"Trái Đất"`.
   ![Hiển thị Đáp án](/images/5-Workshop/5.12-Web-Interface/test_create_riddle_answer.png)
6. **Lưu trữ vào DynamoDB:** Giáo viên click chọn **Lưu Vào Thư Viện**. Hệ thống ghi dữ liệu xuống DynamoDB Table thông qua khóa phân vùng và khóa sắp xếp. Nút lưu chuyển sang trạng thái `⭐ Đã Lưu Vào Thư Viện`.
   ![Lưu câu đố thành công](/images/5-Workshop/5.12-Web-Interface/test_create_riddle_saved.png)

#### 2.3. Bước 3: Tương tác Cộng đồng & Cơ chế Giải đố (Community Interactivity)
Chuyển sang phân hệ **Cộng Đồng** nơi tổng hợp câu đố công khai của các thành viên:
1. **Đoán đúng đáp án:**
   * Tìm đến thẻ câu đố nổi bật về `"Bánh Chưng"`.
   * Nhập đáp án `"Bánh Chưng"` và nhấn nút **Đoán**.
   * Giao diện phản hồi thông báo màu xanh lá: `🎉 Chính xác! Đáp án đúng là: Bánh Chưng`.
   ![Đoán đúng đáp án](/images/5-Workshop/5.12-Web-Interface/test_community_guess_correct.png)
2. **Đoán sai đáp án:**
   * Thử nhập câu trả lời sai như `"Bánh dày"` trên một thẻ câu đố và nhấn **Đoán**.
   * Hệ thống phản hồi cảnh báo đỏ trực quan: `❌ Chưa đúng rồi, thử lại nhé!`.
   ![Đoán sai đáp án](/images/5-Workshop/5.12-Web-Interface/test_community_guess_incorrect.png)
3. **Bình chọn câu đố (Upvoting):**
   * Nhấn biểu tượng nút 👍 ở góc trên thẻ câu đố Bánh Chưng. Điểm upvotes tăng từ 56 lên 57.
   * Hệ thống kiểm tra và lưu bản ghi loại thực thể `UPVOTE` để giới hạn mỗi tài khoản chỉ được bầu chọn một lần duy nhất.
   ![Upvote thành công](/images/5-Workshop/5.12-Web-Interface/test_community_upvote.png)

#### 2.4. Bước 4: Kho Lưu trữ Cá nhân & Xuất bản PDF (My Library & S3 Export)
1. **Truy vấn danh sách:** Chuyển sang tab **Thư Viện Của Tôi**. Hệ thống tự động truy vấn các câu đố do chính Giáo viên Mai tạo (bao gồm câu đố `"Trái Đất"` vừa lưu ở Bước 2).
   ![Thư viện câu đố cá nhân](/images/5-Workshop/5.12-Web-Interface/test_library_list.png)
2. **Xuất bản tài liệu qua Amazon S3:**
   * Nhấp chọn nút **🖨️ Xuất PDF / In câu đố** trên thẻ câu đố `"Trái Đất"`.
   * Trình duyệt kích hoạt lệnh yêu cầu **S3 Presigned URL** và mở hộp thoại in tài liệu / xem trước PDF chuẩn hóa phục vụ việc giảng dạy trực tiếp.
   ![Xuất tài liệu in ấn PDF](/images/5-Workshop/5.12-Web-Interface/test_library_export.png)
   ![Xuất tài liệu in ấn PDF](/images/5-Workshop/5.12-Web-Interface/test_library_export_PDF.png)

#### 2.5. Giao diện phụ trợ: Chế độ Tối (Dark Mode)
Ứng dụng hỗ trợ Dark Mode đầy đủ thông qua CSS Variables để giảm mỏi mắt cho giáo viên và trẻ em khi dùng vào ban đêm.
![Dark Mode](/images/5-Workshop/5.12-Web-Interface/riddle_dark_mode.png)

---

### 3. Các Tính năng Phát triển trong Tương lai (Future Development)

Mặc dù dự án đã hoàn thành các mục tiêu cốt lõi của Workshop, các tính năng sau đây được hoạch định phát triển trong tương lai để tối ưu hóa sản phẩm thương mại:

*   **Tích hợp Xác thực Cognito thực tế:** Thay thế luồng mô phỏng Cognito nội bộ bằng thư viện `@aws-amplify/auth` hoặc SDK chính thức `amazon-cognito-identity-js` nhằm quản lý phiên đăng nhập thực tế, bảo mật bằng JWT Token và lưu trữ token an toàn trong Cookie/LocalStorage.
*   **Liên kết API Gateway & DynamoDB thực:** Chuyển đổi toàn bộ cơ chế mô phỏng dữ liệu (Mock DB Client) sang tích hợp API Gateway HTTP API thông qua việc cấu hình tệp môi trường `.env` (`REACT_APP_API_URL`). Lúc này, mọi yêu cầu tạo, lưu, upvote câu đố sẽ tương tác trực tiếp với dữ liệu sống trên Cloud AWS.
*   **Tính năng Game hóa (Gamification) cho trẻ em:** Phát triển giao diện giải đố tương tác cho học sinh, tích hợp tính năng tính điểm, giới hạn thời gian trả lời và bảng xếp hạng (Leaderboard) để kích thích sự hứng thú học tập của trẻ.
*   **Xuất bản tài liệu PDF/Word hàng loạt:** Mở rộng backend Lambda tích hợp các thư viện xử lý tài liệu mạnh mẽ hơn như `pdfkit` hoặc `docx` để hỗ trợ Giáo viên tải về hàng loạt danh sách câu đố theo chủ đề chỉ với một cú nhấp chuột.
*   **Dashboard Phân tích cho Giáo viên (Analytics Dashboard):** Cấu hình AWS QuickSight hoặc xây dựng Dashboard biểu đồ ngay trên frontend hiển thị thống kê độ tuổi học sinh truy cập, chủ đề câu đố được tạo nhiều nhất và tỷ lệ giải đố thành công.

---

### 4. Đánh giá Tổng kết Sản phẩm

*   **Tính toàn vẹn của Kiến trúc (End-to-End Serverless):** Giao diện liên kết mượt mà với các giả lập thiết kế Single-Table Design trong DynamoDB, các luồng bảo mật của Cognito, và quản lý S3, phản ánh đúng logic nghiệp vụ được cấu hình trên AWS Cloud.
*   **Trải nghiệm Người dùng (UX/UI):** Phong cách Glassmorphism sang trọng, trực quan, dễ sử dụng cho các đối tượng giáo viên và phụ huynh. Thời gian phản hồi và chuyển đổi trang gần như tức thời nhờ cơ chế Single-Page Application.
*   **Khả năng mở rộng:** Cấu trúc mã nguồn React được mô đun hóa cao, dễ dàng mở rộng thêm các tính năng như trò chơi giải đố tương tác trực tiếp hoặc tính năng chấm điểm học sinh trong tương lai.
