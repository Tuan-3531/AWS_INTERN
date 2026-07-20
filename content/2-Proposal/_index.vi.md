---
title: "Bản đề xuất"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---


# AI Riddle Generator 
## Hệ thống tạo câu đố tư duy cho trẻ em sử dụng giải pháp AWS Serverless hợp nhất

### 1. Tóm tắt điều hành  
Dự án AI Riddle Generator là một nền tảng ứng dụng tích hợp Trí tuệ nhân tạo (AI) phục vụ mục đích giáo dục, được thiết kế để tự động chuyển đổi từ khóa hoặc đáp án do người dùng nhập thành các câu đố, đoạn văn gợi ý, hoặc mật mã (như thơ giấu đầu - acrostic verse). Hệ thống hướng tới đối tượng học sinh Cấp 1 và Cấp 2 nhằm kích thích tư duy logic, sáng tạo và mở rộng kiến thức nền tảng. Dự án sử dụng hoàn toàn kiến trúc AWS Serverless nhằm tối ưu hóa chi phí vận hành, đảm bảo khả năng phản hồi thời gian thực và quản lý người dùng tập trung thông qua Amazon Cognito, phục vụ trực tiếp cho các phụ huynh và giáo viên.  

### 2. Tuyên bố vấn đề  
*Vấn đề hiện tại*  
Phụ huynh và giáo viên thường mất rất nhiều thời gian, công sức và dễ bị "bí" ý tưởng khi muốn tự thiết kế các trò chơi trí tuệ, hoạt động săn tìm kho báu (treasure hunt) hoặc các bài tập khởi động đầu giờ (ice-breaking) cho học sinh. Các công cụ tạo câu đố hiện tại chưa tập trung, thiếu tính tùy biến theo bài học hoặc chủ đề văn hóa/lịch sử bản địa, và chưa tận dụng được sức mạnh của AI thế hệ mới để tự động hóa quy trình một cách thông minh.

*Giải pháp*  
Xây dựng hệ thống AI Riddle Generator ứng dụng mô hình ngôn ngữ lớn thông qua Amazon Bedrock để tạo câu đố tự động. Hệ thống sử dụng AWS Lambda làm lõi xử lý logic (gồm các hàm tạo câu đố và quản lý danh sách nổi bật), lưu trữ dữ liệu câu đố và lượt tương tác trong cơ sở dữ liệu Amazon DynamoDB. Toàn bộ hệ thống giao tiếp qua Amazon API Gateway (HTTP API) và triển khai Frontend (React/Vue) nhanh chóng bằng AWS Amplify. Quyền truy cập và định danh người dùng được bảo mật nghiêm ngặt qua Amazon Cognito. 

*Lợi ích và hoàn vốn đầu tư (ROI)*  
Lợi ích: Giải phóng thời gian chuẩn bị bài giảng/trò chơi cho giáo viên và phụ huynh; tăng tính tương tác sinh động trong giáo dục. Đồng thời, đây là nền tảng thực hành kiến trúc Cloud Serverless thực tế cho đội ngũ phát triển sinh viên.

*Chi phí & ROI*: Nhờ mô hình Serverless (chỉ trả tiền khi sử dụng) kết hợp các hạn mức miễn phí (Free Tier) của AWS Lambda, API Gateway và DynamoDB, chi phí vận hành hàng tháng ước tính cực kỳ thấp (gần như bằng 0 USD ở quy mô nhỏ, ngoại trừ phí token gọi AI của Amazon Bedrock). Thời gian hoàn vốn là ngay lập tức nhờ tiết kiệm hàng chục giờ chuẩn bị nội dung thủ công mỗi tháng.

### 3. Kiến trúc giải pháp  
Hệ thống áp dụng kiến trúc AWS Serverless toàn diện nhằm loại bỏ việc quản lý máy chủ, tự động mở rộng theo lượng người dùng truy cập trực tuyến. 

![Cấu trúc dịch vụ dự án](/images/2-Proposal/Cau-Truc-Service-Du-An.png)

*Dịch vụ AWS sử dụng*  
- *Amazon Bedrock*: Gọi các mô hình AI Foundation (thông qua SDK @aws-sdk/client-bedrock-runtime) để sinh câu đố tự động từ từ khóa.
- *AWS Lambda*: Gồm 2 hàm xử lý chính: generateRiddle (gọi AI Bedrock và lưu kết quả) và getFeaturedRiddles (lấy danh sách câu đố nổi bật).
- *Amazon DynamoDB*: Cơ sở dữ liệu NoSQL lưu trữ thông tin câu đố, đáp án, và dữ liệu upvote/tương tác.
- *Amazon API Gateway*: Cấu hình HTTP API để định tuyến các endpoint (POST /riddles/generate, GET /riddles/featured, POST /riddles/upvote) và bật CORS cho phép Frontend kết nối.
- *AWS Amplify*: Kết nối trực tiếp với kho lưu trữ GitHub để tự động build và deploy giao diện Frontend (React/Vue).
- *Amazon Cognito*: Quản lý đăng ký, đăng nhập và phân quyền cho các đối tượng người dùng (Phụ huynh/Giáo viên).

*Thiết kế thành phần*  
- *Giao diện người dùng (Frontend)*: Ứng dụng React/Vue được deploy trên AWS Amplify, sử dụng thư viện axios hoặc fetch để gửi yêu cầu tới API.
- *Tầng API & Xử lý (Backend)*: API Gateway tiếp nhận request $\rightarrow$ Kích hoạt Lambda tương ứng $\rightarrow$ Lambda xử lý logic nghiệp vụ và tương tác với AI/Database.
- *Tầng Trí tuệ nhân tạo (AI Layer)*: Amazon Bedrock nhận prompt từ Lambda, xử lý ngữ nghĩa và trả về nội dung câu đố dạng văn bản/thơ.
- *Tầng Lưu trữ (Database Layer)*: DynamoDB lưu lại các câu đố đã tạo để tái sử dụng và phục vụ tính năng hiển thị câu đố nổi bật. 

### 4. Triển khai kỹ thuật  
*Các giai đoạn triển khai*  

1. *Thiết kế & Nghiên cứu Prompt Engineering*: Nghiên cứu cách tối ưu cấu trúc Prompt cho mô hình trên Amazon Bedrock để đảm bảo câu đố sinh ra phù hợp với học sinh Cấp 1, Cấp 2. Vẽ sơ đồ kiến trúc hệ thống chi tiết.  
2. *Phát triển Backend & Database*: Thiết lập bảng trên DynamoDB. Viết các hàm AWS Lambda bằng Node.js/Python, tích hợp SDK Bedrock và SDK DynamoDB. Cấu hình HTTP API trên API Gateway và kiểm tra kết nối nội bộ.
3. *Phát triển Frontend & Tích hợp*: Thành viên phát triển xây dựng giao diện React/Vue trên máy cục bộ, tích hợp gọi các endpoint từ API Gateway. Đẩy code lên GitHub.
4. *Triển khai & Kiểm thử*: Kết nối AWS Amplify với GitHub để thiết lập CI/CD tự động deploy Frontend. Tích hợp Amazon Cognito để bảo mật. Thực hiện kiểm thử toàn diện (End-to-End) và đưa vào vận hành thực tế.

*Yêu cầu kỹ thuật*  
- *Phía Client*: Giao diện web responsive (chạy tốt trên cả máy tính của giáo viên và điện thoại của phụ huynh). Code sạch, quản lý state tốt để xử lý trạng thái chờ (loading) khi AI đang tạo câu đố.

- *Phía Cloud*: Thành thạo việc cấu hình IAM Role/Policy an toàn cho phép Lambda gọi Bedrock và ghi dữ liệu vào DynamoDB. Cấu hình CORS trên API Gateway để tránh lỗi chặn domain. Hiểu biết về AWS SDK để tối ưu thời gian phản hồi của Lambda.

### 5. Lộ trình & Mốc triển khai  
- *Trước thực tập (Tháng 0)*: Khảo sát nhu cầu thực tế từ giáo viên/phụ huynh và phác thảo các tính năng cốt lõi (tạo câu đố, lọc theo độ tuổi, hệ thống thơ giấu đầu).  

- *Trong thực tập (Tháng 1 - Tháng 3)*:  
    - Tháng 1: Hoàn thiện thiết kế hệ thống, nghiên cứu AWS Bedrock và thiết lập môi trường phát triển.  
    - Tháng 2: Hoàn thành lập trình các hàm Lambda, cấu hình API Gateway và hoàn thiện giao diện Frontend cơ bản trên máy local.  
    - Tháng 3: Tiến hành deploy lên AWS Amplify, cấu hình CI/CD qua GitHub, kiểm thử bảo mật với Cognito và nghiệm thu dự án.  

- *Sau triển khai*: Theo dõi chi phí, tối ưu hóa prompt dựa trên phản hồi của người dùng để nâng cao chất lượng câu đố và mở rộng thêm các dạng câu đố mới hình ảnh/mật mã phức tạp hơn.  

### 6. Ước tính ngân sách  
Có thể xem chi phí trên [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=621f38b12a1ef026842ba2ddfe46ff936ed4ab01)  
Hoặc tải [tệp ước tính ngân sách](../attachments/budget_estimation.pdf).  

*Chi phí hạ tầng*  
- *AWS Lambda*: 0,00 USD (Nằm trong hạn mức 1 triệu request miễn phí mỗi tháng).  
- *Amazon DynamoDB*: 0,00 USD (Hạn mức 25GB lưu trữ miễn phí đủ cho hàng triệu câu đố bằng chữ).  
- *Amazon API Gateway (HTTP API)*: ~0,01 USD (Rất rẻ, chỉ tính trên số lượng triệu request thực tế).  
- *AWS Amplify*: Free Tier cho năm đầu tiên (hoặc khoảng 0,35 - 1,00 USD/tháng cho băng thông và lưu trữ nhỏ).  
- *Amazon Bedrock*: Chi phí tính theo Token thực tế (Ví dụ: ~0.0015 USD cho mỗi 1000 tokens đầu ra của mô hình Claude/Titan, ước tính khoảng vài USD cho hàng ngàn câu đố).  
- *Phần cứng/Thiết bị*: 0 USD (Sử dụng máy tính cá nhân sẵn có của các thành viên để lập trình, không cần mua server vật lý).  

Tổng chi phí ước tính: ~2.00 USD - 5.00 USD/tháng (chủ yếu là chi phí sử dụng API của Amazon Bedrock tùy thuộc vào tần suất tạo câu đố).  

### 7. Đánh giá rủi ro  
*Ma trận rủi ro*  

- AI sinh nội dung không phù hợp (Hallucination/Toxic content): Ảnh hưởng cao, xác suất thấp.  
- Lỗi CORS hoặc chặn kết nối API: Ảnh hưởng trung bình, xác suất trung bình (thường gặp khi cấu hình API Gateway lần đầu).  
- Độ trễ (Latency) khi gọi AI quá lâu: Ảnh hưởng trung bình, xác suất trung bình (do AI cần thời gian suy nghĩ để sinh câu thơ/câu đố).  

*Chiến lược giảm thiểu*  

- Nội dung AI: Thiết lập System Prompt cực kỳ nghiêm ngặt trên Bedrock, ép buộc AI chỉ tập trung vào chủ đề giáo dục lành mạnh cho học sinh.  
- Lỗi CORS: Thiết lập chính xác cấu hình Access-Control-Allow-Origin trên API Gateway ngay từ giai đoạn cấu hình Route.  
- Độ trễ: Thêm hiệu ứng loading hoạt họa sinh động ở Frontend để người dùng không có cảm giác phải chờ đợi lâu.  

*Kế hoạch dự phòng*  

- Nếu Amazon Bedrock gặp sự cố gián đoạn dịch vụ, hệ thống sẽ tự động chuyển hướng sang lấy các câu đố có sẵn (được lưu trữ nhiều lượt upvote nhất) trong DynamoDB để hiển thị tạm thời cho người dùng.


### 8. Kết quả kỳ vọng  
*Cải tiến kỹ thuật*: Xây dựng thành công một ứng dụng Web Fullstack ứng dụng GenAI hoàn chỉnh, tự động hóa 100% quy trình tạo câu đố tư duy thời gian thực thay vì soạn thảo thủ công. Hệ thống chạy ổn định, bảo mật và phản hồi nhanh chóng dưới 5 giây.

*Giá trị dài hạn*: Đem lại một công cụ giáo dục số hữu ích, trực quan cho cộng đồng giáo viên và phụ huynh. Đồng thời tạo ra kho dữ liệu câu đố giáo dục phong phú, mở ra hướng phát triển các trò chơi học đường thông minh trong tương lai.