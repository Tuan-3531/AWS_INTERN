---
title: "Chuẩn bị"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

### Chuẩn bị môi trường AWS và công cụ local

Để hoàn thành bài lab này, bạn cần chuẩn bị đầy đủ các điều kiện tiên quyết sau:

### 1. Tài khoản AWS (AWS Account)
*   Một tài khoản AWS đang hoạt động (có thể dùng tài khoản cá nhân Sandbox hoặc Free Tier).
*   **Kích hoạt quyền truy cập mô hình trên Amazon Bedrock**:
    1. Đăng nhập vào AWS Console, chuyển sang Region hỗ trợ Bedrock (Ví dụ: `us-east-1` hoặc `us-west-2`).
    2. Tìm kiếm dịch vụ **Amazon Bedrock**.
    3. Ở danh mục bên trái, chọn **Model access**.
    4. Nhấp vào **Manage model access**, tick chọn mô hình bạn muốn sử dụng (Ví dụ: **Claude 3 Haiku** hoặc **Claude 3.5 Sonnet** thuộc Anthropic, hoặc mô hình **Amazon Titan**) và nhấp **Request model access**. Đợi vài phút để trạng thái chuyển thành *Access granted*.

### 2. Thiết lập CLI và Quyền truy cập Local
*   **Cài đặt AWS CLI**: Đảm bảo đã cài đặt AWS CLI trên máy tính của bạn.
*   **Cấu hình thông tin xác thực**: Chạy lệnh `aws configure` trong Terminal và nhập:
    *   `AWS Access Key ID`
    *   `AWS Secret Access Key`
    *   `Default region name` (Region bạn đã kích hoạt Bedrock, ví dụ: `us-east-1`)
    *   `Default output format` (`json`)

### 3. Môi trường phát triển cục bộ (Local Development)
*   **Node.js**: Phiên bản Node.js từ `18.x` trở lên đã được cài đặt trên máy tính (dùng lệnh `node -v` để kiểm tra).
*   **IDE**: Trình soạn thảo mã nguồn VS Code hoặc IDE tương đương.
*   Một tài khoản **GitHub** cá nhân (phục vụ kết nối CI/CD tự động deploy Frontend qua AWS Amplify).