---
title: "Triển khai AWS Lambda & Cấu hình IAM"
date: 2026-07-09
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---


Trong phần này, chúng ta sẽ bắt đầu xây dựng tầng xử lý logic backend cốt lõi của dự án **AI Riddle Generator** bằng cách triển khai hàm **AWS Lambda** có tên `Create-Riddle`. Hàm này sẽ chịu trách nhiệm giao tiếp với dịch vụ AI **Amazon Bedrock** để tạo câu đố, ghi nhận dữ liệu vào bảng cơ sở dữ liệu **Amazon DynamoDB**, và lưu trữ các tệp xuất bản tĩnh lên **Amazon S3**.

Để đảm bảo bảo mật và khả năng mở rộng tối đa, chúng ta sẽ cấu hình các Trigger kết nối và phân quyền phân chia tối thiểu thông qua **AWS IAM Policies**.

---

### Các bước thực hiện chi tiết

#### Bước 1: Quản lý và Khởi tạo Hàm AWS Lambda
1. Truy cập vào **AWS Management Console**, tìm kiếm dịch vụ **Lambda** để đi tới bảng quản lý tổng quan.

   ![Bảng điều khiển AWS Lambda](/images/5-Workshop/5.5-AWS-Lambda/1-Lambda-Dashboard.png)

2. Bấm chọn mục **Functions** ở menu bên trái và nhấp vào nút **Create function**.
3. Thiết lập thông tin khởi tạo:
   - Chọn tùy chọn **Author from scratch** (Tạo mới từ đầu).
   - **Function name**: Điền `Create-Riddle`.
   - **Runtime**: Chọn **Node.js 24.x** (hoặc phiên bản Node.js LTS mới nhất ).
   - **Architecture**: Giữ mặc định `x86_64`.
   - **Permissions**: Để mặc định hệ thống tự động tạo một Execution role mới cho Lambda.
4. Bấm nút **Create function** ở cuối trang để xác nhận.

   ![Khởi tạo Hàm Create-Riddle](/images/5-Workshop/5.5-AWS-Lambda/2-Lambda-Create.png)

5. Sau khi quá trình khởi tạo hoàn tất, bạn sẽ được đưa tới giao diện thiết kế hàm. 
   *(Lưu ý: Hình ảnh minh họa dưới đây hiển thị tên hàm tạm thời là `ABC` do thao tác khởi tạo mẫu, tuy nhiên các cài đặt cấu hình hoàn toàn tương tự).*

   ![Khởi tạo thành công](/images/5-Workshop/5.5-AWS-Lambda/3-Lambda-Success-ABC.png)

---

#### Bước 2: Tích hợp Triggers Đầu vào và Đầu ra
Chúng ta cần thêm các trigger kết nối Lambda với các dịch vụ khác trong kiến trúc serverless của dự án.

1. **Thêm Trigger Amazon S3**:
   - Tại sơ đồ thiết kế ở trên cùng, nhấn **Add trigger**.
   - Chọn nguồn **S3**.
   - Chọn đúng bucket lưu trữ tĩnh của dự án: `ai-riddle-storage` (hoặc `riddle-store`).
   - Tích chọn hộp xác nhận cảnh báo cuộc gọi đệ quy (Recursive invocation warning).
   - Nhấn **Add** để lưu lại.

   ![Cấu hình S3 Trigger](/images/5-Workshop/5.5-AWS-Lambda/4-S3-Trigger.png)

2. **Thêm Trigger Amazon DynamoDB**:
   - Nhấn **Add trigger** một lần nữa.
   - Chọn nguồn **DynamoDB**.
   - Chọn bảng lưu trữ dữ liệu chính của dự án: `AiRiddleGenerator_Core`.
   - Nhấn **Add** để hoàn tất liên kết.

   ![Cấu hình DynamoDB Trigger](/images/5-Workshop/5.5-AWS-Lambda/5-DynamoDB-Trigger.png)

3. **Thêm Trigger Amazon API Gateway**:
   - Nhấn **Add trigger**.
   - Chọn nguồn **API Gateway**.
   - Chọn tùy chọn **Use existing API** (Sử dụng API sẵn có).
   - Tìm kiếm và chọn API của dự án: `AI-Riddle-API`.
   - Nhấn **Add** để gán API Gateway làm cổng nhận yêu cầu HTTP dẫn vào Lambda.

   ![Cấu hình API Gateway Trigger](/images/5-Workshop/5.5-AWS-Lambda/6-APIGateway-Trigger.png)

---

#### Bước 3: Triển khai Mã nguồn Backend xử lý Nghiệp vụ
1. Cuộn xuống phần **Code source**, mở file `index.mjs` mặc định trong trình duyệt soạn thảo Code Editor.
2. Thay thế toàn bộ mã nguồn mặc định bằng đoạn code Node.js xử lý logic tạo câu đố bằng Bedrock và tương tác với DynamoDB/S3 như sau:

```javascript
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { DynamoDBDocumentClient, PutCommand } from "@aws-sdk/lib-dynamodb";
import { S3Client, PutObjectCommand } from "@aws-sdk/client-s3";
import { BedrockRuntimeClient, InvokeModelCommand } from "@aws-sdk/client-bedrock-runtime";

const ddbClient = new DynamoDBClient({});
const s3Client = new S3Client({});
const bedrockClient = new BedrockRuntimeClient({ region: "us-east-1" });

const dynamo = DynamoDBDocumentClient.from(ddbClient);
const tableName = "AiRiddleGenerator_Core";
const bucketName = "ai-riddle-storage";

export const handler = async (event) => {
    let body;
    let statusCode = 200;
    const headers = {
        "Content-Type": "application/json",
        "Access-Control-Allow-Origin": "*"
    };

    try {
        let requestJSON = JSON.parse(event.body || "{}");
        const prompt = requestJSON.prompt || "Tạo một câu đố vui tiếng Việt";

        // 1. Gọi Amazon Bedrock sinh câu đố
        const bedrockResponse = await bedrockClient.send(
            new InvokeModelCommand({
                modelId: "anthropic.claude-3-haiku-20240307-v1:0",
                contentType: "application/json",
                accept: "application/json",
                body: JSON.stringify({
                    anthropic_version: "bedrock-2023-05-31",
                    max_tokens: 500,
                    messages: [{ role: "user", content: prompt }]
                })
            })
        );
        const result = JSON.parse(new TextDecoder().decode(bedrockResponse.body));
        const riddleText = result.content[0].text;
        const riddleId = `riddle_${Date.now()}`;

        // 2. Ghi siêu dữ liệu câu đố vào DynamoDB
        await dynamo.send(
            new PutCommand({
                TableName: tableName,
                Item: {
                    PK: `RIDDLE#${riddleId}`,
                    SK: "METADATA",
                    Prompt: prompt,
                    Riddle: riddleText,
                    CreatedAt: new Date().toISOString()
                }
            })
        );

        // 3. Xuất lưu trữ nội dung câu đố dạng tệp JSON lên S3
        await s3Client.send(
            new PutObjectCommand({
                Bucket: bucketName,
                Key: `exports/${riddleId}.json`,
                Body: JSON.stringify({ id: riddleId, prompt, riddle: riddleText }),
                ContentType: "application/json"
            })
        );

        body = { id: riddleId, riddle: riddleText };
    } catch (err) {
        statusCode = 500;
        body = { error: err.message };
    }

    return {
        statusCode,
        body: JSON.stringify(body),
        headers
    };
};
```

3. Nhấp vào nút **Deploy** trên thanh công cụ để áp dụng mã nguồn mới.

   ![Mã nguồn hàm Create-Riddle](/images/5-Workshop/5.5-AWS-Lambda/7-Lambda-Code.png)

---

#### Bước 4: Thiết lập Phân quyền Bảo mật trên AWS IAM Console
Theo mặc định, hàm Lambda mới tạo sẽ không có quyền truy cập để ghi dữ liệu vào DynamoDB, ghi file lên S3 hay gọi API của Amazon Bedrock. Chúng ta cần thiết lập phân quyền cụ thể.

1. Tại tab **Configuration** (Cấu hình) của Lambda, bấm vào mục **Permissions** ở danh sách bên trái. Tìm tới phần **Execution role** và nhấp chọn liên kết tại **Role name** (Ví dụ: `Create-Riddle-role-xvuvaltf`) để mở giao diện quản lý vai trò trong dịch vụ **AWS IAM**.

   ![Xem Execution Role của Lambda](/images/5-Workshop/5.5-AWS-Lambda/8-Lambda-Permissions.png)

2. **Cấu hình Policy tùy chỉnh cho Amazon S3**:
   - Tại giao diện quản lý Role, nhấn chọn nút **Add permissions** và chọn **Create inline policy** (Tạo chính sách trực tiếp).

     ![Tạo Inline Policy mới](/images/5-Workshop/5.5-AWS-Lambda/9-IAM-Inline-Policy-Create.png)

   - Tại bảng soạn thảo chính sách, phần dịch vụ chọn **S3**.
   - Tại mục Actions, tìm kiếm và tích chọn lần lượt các quyền:
     - `GetObject` (Đọc dữ liệu đối tượng)
     
       ![Chọn quyền GetObject](/images/5-Workshop/5.5-AWS-Lambda/10-S3-Policy-GetObject.png)
       
     - `DeleteObject` (Xóa đối tượng)
     
       ![Chọn quyền DeleteObject](/images/5-Workshop/5.5-AWS-Lambda/11-S3-Policy-DeleteObject.png)
       
     - `PutObject` (Ghi/Tải lên đối tượng)
     
       ![Chọn quyền PutObject](/images/5-Workshop/5.5-AWS-Lambda/12-S3-Policy-PutObject.png)
       
   - Tại mục Resources, chọn **Specific** và nhấn **Add ARNs** để gắn định danh ARN của bucket `ai-riddle-storage` (ví dụ: `arn:aws:s3:::ai-riddle-storage/*`). Sau đó hoàn tất đặt tên policy là `S3AccessPolicy` và nhấn **Create policy**.

     ![Cấu hình Resources ARN cho S3](/images/5-Workshop/5.5-AWS-Lambda/13-S3-Policy-Resources.png)

3. **Gán thêm các chính sách quản trị (Managed Policies) của AWS**:
   - Quay lại trang tổng quan của Role, nhấn chọn nút **Add permissions** -> **Attach policies**.

     ![Gán chính sách AWS Managed](/images/5-Workshop/5.5-AWS-Lambda/14-Attach-Managed-Policies.png)

   - Gán quyền truy cập DynamoDB: Tìm kiếm từ khóa `DynamoDB` trong hộp tìm kiếm, tích chọn chính sách **AmazonDynamoDBFullAccess** (hoặc `AmazonDynamoDBFullAccess_v2`).

     ![Tìm kiếm và chọn DynamoDB Full Access](/images/5-Workshop/5.5-AWS-Lambda/15-Attach-DynamoDB-Policy.png)

   - Gán quyền truy cập Bedrock: Tìm kiếm từ khóa `bedrock`, tích chọn chính sách **AmazonBedrockFullAccess** (hoặc `AmazonBedrockMantleFullAccess` tùy theo vùng).

     ![Tìm kiếm và chọn Bedrock Access](/images/5-Workshop/5.5-AWS-Lambda/16-Attach-Bedrock-Policy.png)

   - Nhấn **Add permissions** ở cuối để áp dụng. Role thực thi của Lambda hiện tại đã có đủ 3 quyền truy cập cần thiết để vận hành hệ thống một cách trơn tru!
