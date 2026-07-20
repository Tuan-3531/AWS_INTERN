---
title: "Blog 2"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Kiến trúc bảo mật Serverless: Cách bảo vệ ứng dụng AI Riddle Generator trên AWS

Chào mọi người cộng đồng AWS Study Group VN.

Nếu bạn đang phát triển một ứng dụng Serverless kết hợp Generative AI — chẳng hạn như hệ thống tự động tạo câu đố tư duy cho trẻ em của nhóm mình (AI Riddle Generator) — bài toán đặt ra không chỉ là làm sao cho AI chạy mượt, prompt viết tiếng Việt chuẩn. Một thách thức lớn hơn mà các nhà phát triển thường bỏ qua ở giai đoạn đầu chính là: Làm sao để bảo mật kiến trúc này một cách toàn diện?

Khi ứng dụng của bạn mở rộng cho hàng nghìn phụ huynh và giáo viên truy cập, việc không có một cơ chế kiểm soát truy cập, chống tấn công từ chối dịch vụ (DDoS), hoặc phân quyền lỏng lẻo giữa các dịch vụ có thể dẫn đến rò rỉ dữ liệu, cạn kiệt tài nguyên hệ thống, hoặc làm tăng vọt chi phí API gọi sang mô hình AI (như Amazon Bedrock).

Để giải quyết triệt để bài toán này, việc áp dụng mô hình bảo mật nhiều lớp (Defense in Depth) trên AWS là bắt buộc. Trong bài viết này, chúng ta sẽ cùng tìm hiểu:
- Lớp bảo mật vòng ngoài (Edge & Presentation) chống tấn công mạng như thế nào?
- Cách quản lý định danh người dùng và bảo vệ API Backend?
- Nguyên tắc phân quyền tối thiểu (Least Privilege) giữa các dịch vụ Serverless?
- Giám sát và cảnh báo tự động khi có sự cố bảo mật?

---

## 1. Bảo mật vòng ngoài: Chặn đứng hiểm họa từ "Cửa ngõ"
Ngay khi người dùng (Phụ huynh, Giáo viên) tương tác với ứng dụng thông qua tên miền được phân giải bởi Amazon Route 53, hệ thống đã kích hoạt các lớp phòng vệ vòng ngoài:

*   **AWS Amplify & Amazon CloudFront**: Toàn bộ source code giao diện tĩnh (React/Vue) được phân phối qua CDN. CloudFront tự động hỗ trợ mã hóa dữ liệu trên đường truyền bằng HTTPS (mặc định sẵn SSL/TLS), ngăn chặn hoàn toàn các cuộc tấn công nghe lén hoặc thay đổi dữ liệu giữa chừng (Man-in-the-Middle).
*   **AWS WAF (Web Application Firewall)**: Lớp lá chắn này được cấu hình trực tiếp tại CloudFront/API Gateway để lọc lưu lượng, chặn các địa chỉ IP xấu, ngăn chặn các kiểu tấn công phổ biến như SQL Injection, Cross-Site Scripting (XSS), và thiết lập luật Rate Limiting để chống Spam request.

---

## 2. Xác thực người dùng và Bảo vệ API Tầng Backend
Khi người dùng thực hiện đăng nhập hoặc gửi yêu cầu tạo câu đố, tầng Authentication & API sẽ làm nhiệm vụ kiểm soát:
*   **Amazon Cognito**: Đảm nhận vai trò quản lý danh tính (User Pool). Sau khi người dùng đăng nhập thành công, Cognito sẽ cấp một mã JWT Token (JSON Web Token) hợp lệ.
*   **Amazon API Gateway**: Đóng vai trò là "người gác cổng" cho hệ thống Backend. API Gateway sẽ không trực tiếp chuyển request đến Lambda ngay, mà nó sẽ phối hợp với Cognito để Xác thực tính hợp lệ của Token. Nếu Token giả mạo hoặc hết hạn, request sẽ bị chặn đứng ngay lập tức, bảo vệ tầng tính toán Serverless phía sau khỏi các truy cập trái phép.

---

## 3. Nguyên tắc Phân quyền tối thiểu (Least Privilege) với AWS IAM
Điểm cốt lõi trong bảo mật kiến trúc Serverless chính là việc cô lập quyền hạn của các dịch vụ trong tầng Serverless Compute và Data Storage:

*   **AWS IAM (Identity and Access Management)**: Thay vì cấp toàn quyền cho hàm AWS Lambda, mỗi hàm Lambda chỉ được gán một IAM Role với quyền hạn tối thiểu vừa đủ để chạy nhiệm vụ của nó (Least Privilege).
*   **Cơ chế liên kết an toàn**:
    *   Hàm `generateRiddle` (Xử lý tạo câu đố) chỉ có quyền gửi prompt sang Amazon Bedrock và ghi dữ liệu vào đúng bảng Riddles của Amazon DynamoDB. Nó hoàn toàn không có quyền xóa dữ liệu hay can thiệp vào các dịch vụ khác.
    *   Hàm xuất file PDF/Word chỉ được quyền ghi file vào một Amazon S3 Bucket cụ thể (`ai-riddle-storage`) và tạo S3 Presigned URL có thời hạn ngắn (vài phút) để trả về cho Frontend. Nhờ vậy, file lưu trữ trên S3 luôn được giữ ở trạng thái Private, không bị lộ ra Internet công cộng.

---

## 4. Giám sát an ninh chủ động với CloudWatch & SNS
Hệ thống bảo mật sẽ không hoàn thiện nếu thiếu đi khả năng giám sát và phản ứng nhanh:

*   **Amazon CloudWatch**: Toàn bộ lịch sử hoạt động, mã lỗi ứng dụng (ví dụ: các lỗi Access Denied hoặc tần suất gọi API Bedrock bất thường) đều được đẩy mã Logs & Metrics về CloudWatch tập trung.
*   **Amazon SNS (Simple Notification Service)**: Khi CloudWatch phát hiện các chỉ số vượt ngưỡng an toàn (ví dụ: số lượng request lỗi 4XX tăng đột biến do có dấu hiệu dò quét API), hệ thống sẽ lập tức Kích hoạt báo lỗi (Alarm). Qua Amazon SNS, một cảnh báo qua Email hoặc Slack sẽ được gửi ngay đến đội ngũ vận hành để kịp thời xử lý.

---

## 5. Kết luận
Bảo mật cho một ứng dụng Serverless tích hợp AI như AI Riddle Generator không đơn thuần là đặt mật khẩu cho cơ sở dữ liệu. Đó là sự phối hợp chặt chẽ của một chuỗi giải pháp mã hóa, tường lửa, xác thực tập trung và phân quyền thông minh:
*   **AWS WAF & CloudFront**: Lọc lưu lượng và chống DDoS từ vòng ngoài.
*   **Amazon Cognito & API Gateway**: Đảm bảo chỉ người dùng hợp lệ mới có quyền gọi tài nguyên.
*   **AWS IAM**: Giới hạn vùng ảnh hưởng (Blast Radius) xuống mức thấp nhất bằng nguyên tắc phân quyền tối thiểu.
*   **CloudWatch & SNS**: Đem lại tầm nhìn toàn diện để phát hiện và ứng phó sự cố theo thời gian thực.

Thiết kế một kiến trúc bảo mật chuẩn chỉnh ngay từ đầu giúp ứng dụng của bạn vừa vận hành ổn định, vừa bảo vệ được tài nguyên AI có chi phí đắt đỏ, tạo nền tảng vững chắc để phát triển sản phẩm an toàn trên đám mây.

---
**Nguồn tham khảo:** [Secure API Access with Amazon Cognito, Amazon API Gateway, and AWS Lambda](https://aws.amazon.com/blogs/compute/secure-api-access-with-amazon-cognito-federated-identities-amazon-cognito-user-pools-and-amazon-api-gateway/)