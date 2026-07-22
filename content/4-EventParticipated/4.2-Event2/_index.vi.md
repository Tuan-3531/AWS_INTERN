---
title: "Event 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 4.2. </b> "
---


# Bài thu hoạch sự kiện ngày 13/06

### Mục Đích Của Sự Kiện

- Định hướng nghề nghiệp và chia sẻ hành trình phát triển trong ngành công nghệ thông tin.
- Phân tích kiến trúc hệ thống thực tế (case study: Ứng dụng Rút gọn Link) trên nền tảng điện toán đám mây.
- Cung cấp góc nhìn chuyên sâu về ngành Data Analytics và con đường thăng tiến qua từng cấp độ.  
- Hiểu rõ quy trình tuyển dụng, văn hóa của các công ty đa quốc gia (MNC) và triết lý làm việc.  
- Trình bày bức tranh thực tế về nghề DevOps Engineer so với lý thuyết.

### Danh Sách Diễn Giả

- **Danh Hoàng Hiếu Nghị** - Chia sẻ chủ đề: Hành trình nghề nghiệp và xác định vấn đề thực sự của người dùng.
- **Trung Kiên (Leader) & Nguyễn Minh Thọ** - Chia sẻ chủ đề: Phân tích kiến trúc dự án Link rút gọn (URL Shortener).
- **Đạt Phạm** - Chia sẻ chủ đề: Data Analytics và lộ trình phát triển tư duy nghề nghiệp.
- **Nguyễn Cường** - Chia sẻ chủ đề: Văn hóa doanh nghiệp (MNC) và Triết lý "Đúng việc".
- **Trọng H Trương (DevOps tại Endava)** - Chia sẻ chủ đề: DevOps Engineer thực sự làm gì?

### Nội Dung Nổi Bật

#### Hành trình nghề nghiệp và Giải quyết vấn đề (Anh Hiếu Nghị)

- **Định hướng**: Chia sẻ về hành trình phát triển cá nhân trong ngành.
- **Tư duy**: Nhấn mạnh tầm quan trọng của việc phải đi sâu vào cốt lõi để xác định được điều khó khăn thực sự mà người dùng đang gặp phải, từ đó mới có thể đưa ra giải pháp công nghệ chính xác và mang lại giá trị thực tiễn.

#### Kiến trúc dự án Link rút gọn - URL Shortener (Anh Trung Kiên & Anh Minh Thọ)

- **Giải quyết khó khăn**: Nêu rõ bài toán hệ thống khi xử lý lượng truy cập lớn và cách sử dụng các dịch vụ đám mây để giải quyết.
- **Kiến trúc hệ thống 3 phần cốt lõi**:
    - **Tầng Frontend & Edge**: Kết hợp hoàn hảo giữa Amazon CloudFront, tường lửa AWS WAF và AWS Amplify.
    - **Tầng Xử lý (Compute & Cache)**: Sử dụng Amazon ECS tạo logic mã hóa. Request sẽ đi từ ECS sang ElastiCache (Redis).
    - **Tầng Database**: Trỏ về Amazon DynamoDB.
- **Luồng hoạt động (Cache-aside pattern)**: Khi có request lấy link, hệ thống kiểm tra ElastiCache trước (Cache hit thì trả về web ngay). Nếu không có (Cache miss), hệ thống sẽ truy vấn xuống DynamoDB, tạo/lấy link và lưu ngược lại vào Cache.
- **4 Ưu điểm của kiến trúc**:
    1. Separation of concerns (Phân tách mối quan tâm / logic rõ ràng).
    2. Defense at the edge (Bảo mật ngay từ tầng biên với WAF & CloudFront).
    3. Pre-computation (Tính toán trước dữ liệu).
    4. Cache-aside pattern (Mô hình tối ưu hóa truy xuất bộ nhớ đệm).

#### Data Analytics và Văn hóa MNC (Anh Đạt Phạm & Anh Nguyễn Cường)

##### Phần 1: Data Analytics (Anh Đạt Phạm)

- **Vai trò**: Lập báo cáo hàng tuần, tìm kiếm nguyên nhân gốc rễ, giao tiếp với các phòng ban. Phân tích chi phí (chi tiết, rủi ro) và các chỉ số kinh doanh (tỷ lệ đặt hàng).
- **Kỹ năng cốt lõi**: Tư duy phản biện, kỹ năng giao tiếp, kể chuyện bằng dữ liệu (Data storytelling) và tư duy phát triển.
- **Mindset**: Hãy áp dụng góc nhìn của người không hiểu gì về kỹ thuật để giải thích. Phải thực sự hiểu những con số đại diện cho điều gì ở thế giới thực.
- **5 Cấp độ phát triển nghề nghiệp**:
    - Follower: Thực tập sinh, cần người chỉ việc tận tay.
    - Learner: Hiểu công việc nhưng vẫn cần hướng dẫn.
    - Problem Solver: Tự giải quyết được các tác vụ được giao.
    - System Thinker: Nhìn bài toán ở tầm vóc toàn cục (Ví dụ: Luật mới ra ảnh hưởng đến công ty thế nào).
    - Super Star: Xây dựng tầm nhìn hệ thống, vạch ra chiến lược dài hạn và định vị doanh nghiệp.

##### Phần 2: Văn hóa MNC & Triết lý "Đúng việc" (Anh Nguyễn Cường)

- **Tuyển dụng MNC**: Qua 4 vòng (Lọc hồ sơ/Sơ vấn -> Test năng lực -> Phỏng vấn chuyên môn -> Đánh giá độ hòa hợp văn hóa).
- **Văn hóa doanh nghiệp**: Là tổng hòa "cách nghĩ, cách sống, cách làm" đối với công việc của tất cả các thành viên.
- **Bối cảnh hội nhập**: Phân tích các cột mốc của Việt Nam (1975-1986 cô lập, 1986-1995 Đổi mới, 1997 Internet & gia nhập WTO). Nhấn mạnh Việt Nam cần hướng tới tiêu chuẩn quốc tế, đặc biệt không được làm mất uy tín về bảo mật dữ liệu.
- **Triết lý "Đúng việc" (theo sách của TS. Giản Tư Trung)**:
    Làm người: Tử tế (Vùng xã hội).
    Làm nghề: Giải quyết bài toán thực tế với tinh thần phụng sự (Vùng kinh tế).
    Làm dân: Trách nhiệm với quốc gia, gánh vác vận mệnh đất nước (Vùng chính trị).

#### Thực tế nghề DevOps (Anh Trọng H. Trương)

- **Lý do chọn nghề**: Ngầu, thu nhập tốt, tiềm năng việc làm cao. So sánh với sự bùng nổ của AI, DevOps vẫn giữ vị thế quan trọng.
- **Thực tế vs Lý thuyết**: Học lý thuyết là tạo/viết code, CI/CD, Docker, sửa lỗi giữa đêm. Tuy nhiên thực tế, tùy thuộc quy mô công ty (Startup vs Tập đoàn) mà khối lượng công việc sẽ khác nhau. Ở startup, thành viên thường phải làm "trái ngành" và kiêm nhiệm rất nhiều trách nhiệm.
- **Bộ công cụ (Tools)**: Phải học dàn trải từ Linux, Network, Git, ngôn ngữ lập trình, CI/CD đến Container. Tuy nhiên, công cụ luôn thay đổi liên tục.
- **Mindset sống còn của DevOps**:
    Hãy kiểm tra kỹ những đoạn code copy/paste, học từ sai lầm thực tế.
    Giao tiếp cực kỳ quan trọng (phải biết cách nói chuyện với các team khác).
    DevOps không phải là người rảnh rỗi đi giải quyết mọi thứ! Họ cần có không gian để xử lý các sự cố (incident) xảy ra đột ngột.
    Tư duy hệ thống (System Thinker) thay vì chỉ nhìn vào Task. Xây dựng hệ thống clean, dễ tiếp thu.
    Sử dụng AI để nâng cao hiệu suất, đừng để não lười biếng.
- Kể về hành trình tự xây dựng Product của bản thân để đúc kết kinh nghiệm.

### Những Gì Học Được

#### Tư Duy Thiết Kế & Kỹ Thuật

- Nắm vững kiến trúc Cache-aside và sức mạnh của việc phân tách module (Separation of concerns).
- Hiểu được tầm quan trọng của việc chặn đứng các truy cập độc hại ngay tại tầng biên (Defense at the edge) bằng tường lửa và CDN thay vì để lọt xuống hệ thống tính toán backend.
- Hiểu rằng trong Data, dữ liệu vô tri nếu không có người "kể chuyện". Phải luôn đặt mình vào vị trí end-user để hiểu con số mang ý nghĩa gì.

#### Kỹ Năng Mềm & Định Hướng

- Việc thăng tiến không chỉ nằm ở kỹ thuật (coding/tooling) mà phụ thuộc vào việc chuyển đổi từ tư duy giải quyết vấn đề (Problem Solver) sang tư duy hệ thống (System Thinker).
- Giao tiếp là kỹ năng sống còn trong cả Data Analytics lẫn DevOps để kết nối các phòng ban, giải quyết mâu thuẫn và thống nhất luồng công việc.
- Rút ra nguyên tắc: Học cách dùng AI để tối ưu hóa năng suất xử lý công việc chứ không ỷ lại làm mất đi tư duy cốt lõi.

#### Ứng Dụng Vào Công Việc

- **Áp dụng mô hình "Defense at the edge"**: Trực tiếp thiết lập AWS WAF kết hợp cùng Amazon CloudFront và Amplify để bảo vệ giao diện ứng dụng web tĩnh ngay từ tầng ngoài cùng, chống lại các cuộc tấn công DDoS và Prompt Injection trước khi request chạm tới API Gateway.
- **Tư duy tối ưu hóa Backend**: Việc tận dụng DynamoDB để lưu trữ kết quả, kết hợp với Lambda tính toán tự động mở rộng giúp triển khai nguyên tắc Separation of concerns (tách biệt rõ ràng tầng tính toán và tầng lưu trữ), đúng với mô hình mà các diễn giả đã phân tích.
- **Phát triển định hướng System Thinker**: Khi xây dựng kiến trúc, thay vì chỉ hoàn thành từng task rời rạc, em luôn nhìn vào tổng thể quy trình truyền dữ liệu (từ Route 53 -> Frontend -> API Gateway -> Lambda -> DynamoDB/Bedrock) để kiểm soát lỗi toàn diện, nâng tầm bản thân từ cấp độ Learner lên Problem Solver/System Thinker.
- **Chuẩn bị tâm thế vào môi trường làm việc**: Hiểu rõ triết lý "Đúng việc" và cách hành xử, tư duy trong môi trường công ty đa quốc gia (MNC) để nâng cao sự chuyên nghiệp trong suốt quá trình thực tập.

### Trải nghiệm trong event

- Buổi sự kiện mang lại cho em những góc nhìn cực kỳ "thực chiến" mà trường lớp hiếm khi đề cập chi tiết. Điểm ấn tượng nhất là sự kết nối nhịp nhàng giữa lý thuyết và thực tiễn kiến trúc hệ thống AWS từ bài chia sẻ của nhóm anh Trung Kiên, minh họa chính xác sức mạnh của Cloud Computing đối với các hệ thống có lưu lượng lớn.
- Đồng thời, phần chia sẻ về Data Analytics và DevOps đã phá vỡ lăng kính màu hồng của em. Em nhận ra rằng dù làm ở vị trí nào, từ phân tích dữ liệu đến vận hành hệ thống, kỹ năng quyết định sự thành bại lại là **Giao tiếp (Communication)** và **Tư duy hệ thống (System Thinking)**. Khái niệm làm việc với tinh thần "Phụng sự" và triết lý "Đúng việc" thực sự là kim chỉ nam quý giá, giúp em định hình lại thái độ làm việc và xây dựng tinh thần trách nhiệm cao độ đối với bất kỳ dòng code hay bản thiết kế nào mình tạo ra.

#### Một số hình ảnh khi tham gia sự kiện

![Ảnh Tham Gia Sự Kiện](/images/4-EventParticipated/Event2/1783679263617_118571171431119328_g2710655335341090523_88121a123a90aa42bd6a7d6b606595ce.jpg)
![Ảnh Tham Gia Sự Kiện](/images/4-EventParticipated/Event2/1783679263622_118571171431119328_g2710655335341090523_acbde0923b02fe0cc5322e38e358e398.jpg)
![Ảnh Tham Gia Sự Kiện](/images/4-EventParticipated/Event2/1783679263626_118571171431119328_g2710655335341090523_3163d5fa6c753f52282132d7523da0f4.jpg)
![Ảnh Tham Gia Sự Kiện](/images/4-EventParticipated/Event2/1783679263630_118571171431119328_g2710655335341090523_f29790f1ac468a56789b01ab3f9c1795.jpg)


