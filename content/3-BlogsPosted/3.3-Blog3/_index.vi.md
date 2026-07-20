---
title: "Blog 3"
date: 2026-07-09
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Cách một tổ chức cắt giảm 39% chi phí AWS trong 12 tuần

**Bối cảnh:** Một đội ngũ Platform Engineering của một công ty SaaS xử lý 150 TB dữ liệu, với mức chi phí AWS hàng tháng khoảng 35.000 USD. Họ chuẩn bị đón đợt tăng trưởng gấp 10 lần (10x growth). Nếu không tối ưu, hóa đơn AWS của họ có thể đội lên 350.000 USD/tháng (gần 4,2 triệu USD/năm).

Mục tiêu của họ là kết hợp nhiều chiến lược tối ưu hóa tuần tự theo từng Phase (Giai đoạn) nhằm giảm thiểu rủi ro, dùng chính thành quả của Phase trước để "fund" (tài trợ/tạo đà) cho Phase sau.

## 5 Nguyên nhân gây lãng phí (The Cost Challenge)

Sau khi đánh giá, họ tìm ra 5 "nút thắt" chính gây tốn kém:

1. Đang sử dụng tài nguyên thế hệ cũ (EC2 dòng m5 và EBS gp2), bỏ lỡ ưu thế Price-Performance (hiệu năng/giá thành) của các dòng hiện đại.
2. Giao tiếp với Amazon S3 bị định tuyến qua NAT Gateway, gây lãng phí 2.400 USD/tháng tiền data transfer.
3. Sử dụng hơn 150 Network Load Balancers (NLB), tạo ra một khoản phí duy trì tĩnh khổng lồ.
4. 100% dữ liệu đang nằm trên S3 Standard, không phân tầng lưu trữ (Tiering) cho các dữ liệu ít truy cập.
5. Cấu hình (Right-sizing) dựa trên mức Peak thay vì dùng cơ chế Auto Scaling linh hoạt, gây lãng phí 30-50% năng lực tính toán.

## Phương pháp phân bổ: Công sức vs. Tác động (Effort vs. Impact)

Thay vì làm tất cả cùng lúc, đội ngũ sử dụng AWS Cost Optimization Hub để chấm điểm các hạng mục theo 3 tiêu chí: Công sức triển khai (Effort), Tác động chi phí (Impact), và Rủi ro (Risk). Những công việc rủi ro thấp, không cần sửa code ứng dụng sẽ được làm trước.

## Quá trình thực thi qua 3 Giai đoạn (3-Phase Strategy)

### Giai đoạn 1: Tối ưu nền tảng (Tuần 1 - 4)

Tập trung vào Storage, Networking và Load Balancing. Phase này mang lại lợi ích nhanh nhất (Quick Wins).
* **Storage (EBS & S3):** Dựa trên khuyến nghị của AWS Compute Optimizer, họ nâng cấp (online migration) hơn 200 volumes từ gp2 sang gp3 – không gây bất kỳ downtime nào. Kích hoạt S3 Intelligent-Tiering, tự động đẩy các object > 128KB xuống các tier rẻ hơn, tiết kiệm ngay 1.200 USD/tháng.
* **Networking:** Dùng AWS Cost and Usage Reports (CUR) và VPC Flow Logs, đội ngũ phát hiện 60% traffic đi qua NAT Gateway là để gọi về S3. Họ lập tức cấu hình VPC Gateway Endpoint for S3, định tuyến traffic chạy ngầm trong mạng nội bộ AWS, cắt giảm chi phí NAT Gateway được 2.400 USD/tháng.
* **Gom cụm Load Balancer (Consolidation):** Kiến trúc cũ dùng Kubernetes (EKS) cấp phát mặc định một NLB cho mỗi Service (hơn 150 NLBs đang chạy). Đội ngũ đã gom 150 NLBs này vào 5 Application Load Balancers (ALBs) chia theo các nhóm Domain (API, Web, Internal, Async, Admin). Nhờ AWS Load Balancer Controller và tính năng Route 53 Weighted Routing, họ shift traffic từ NLB cũ sang ALB mới (10% -> 50% -> 100%) mà không rớt một request nào. Việc dọn dẹp 150 NLB thừa giúp tiết kiệm thêm 3.400 USD/tháng.

**Kết thúc Giai đoạn 1:** Tiết kiệm ~8.400 USD/tháng (giảm 24% tổng bill).

### Giai đoạn 2: Tối ưu năng lực tính toán (Compute Right-Sizing) (Tuần 4-8)

AWS Compute Optimizer chỉ ra rằng nhiều instances chạy dòng General-purpose (m5.4xlarge) đang bị hiện tượng nghẽn bộ nhớ (Memory-bound) (chỉ dùng 45% CPU nhưng ngốn 85% RAM), dẫn đến dư thừa năng lực CPU.

**Hành động:** Chuyển đổi sang các dòng Memory-optimized dùng chip AMD (r6a.2xlarge). Việc này giữ nguyên mức RAM 64 GiB nhưng giảm nửa số vCPU, giúp hạ giá thành. Bằng cách can thiệp vào Launch Template và EC2 Auto Scaling, họ thực hiện A/B testing và Rollback an toàn nếu hiệu suất không đạt chuẩn.

### Giai đoạn 3: Tối ưu nâng cao và Kiến trúc Next-Gen (Tuần 8 - 12)

* **Triển khai Karpenter:** Thay thế Cluster Autoscaler truyền thống bằng mã nguồn mở Karpenter để provision node tự động và linh hoạt hơn cho EKS.
* **Chuyển đổi sang kiến trúc ARM (AWS Graviton):** Đội ngũ build các container images hỗ trợ Multi-architecture (cả x86 và arm64). Sau đó, dùng Karpenter Disruption Controller để tự động dọn (drain) các node x86 cũ (r6i.2xlarge) và thay thế bằng dòng Graviton2 (r6g.2xlarge). Việc chuyển sang vi xử lý ARM giúp tiết kiệm thêm 20% chi phí compute cho các workload được hỗ trợ.

## Kết quả & Quản trị (Governance)

1. **Kết quả đạt được:** Sau 12 tuần, tổng cộng tổ chức đã giảm được 39% chi phí AWS (tiết kiệm 13.700 USD/tháng). Nếu giữ nguyên kiến trúc cũ trong đợt tăng trưởng 10x, thì những nỗ lực này tương đương với việc tiết kiệm được khoảng 1,64 triệu USD/năm cho công ty.
2. **Quản trị bền vững (Sustaining):** Nguyên tắc cốt lõi của FinOps là "không để chi phí phình to trở lại". Đội ngũ đã:
   * Đưa quy trình review chi phí vào các buổi Sprint Planning định kỳ.
   * Thiết lập Service Control Policies (SCPs) qua AWS Organizations nhằm:
     * Cấm (Deny) tạo mới các volume chuẩn cũ gp2 (bắt buộc dùng gp3).
     * Chỉ cho phép cấp phát tài nguyên thuộc những Instance Families đã được phê duyệt và tại những AWS Region nhất định.

> **Nguồn tham khảo:** [How one organization cut AWS costs by 39% in 12 weeks](https://aws.amazon.com/vi/blogs/aws-cloud-financial-management/how-one-organization-cut-aws-costs-by-39-in-12-weeks/?content_source=fb&fb_content_id=Q9-wBQFW206aJU5vuo2CKD0obi06M-KIiAdM-zCAU_SURad5dGSS7XLwpBlxbO1QSg&channel_type=fb)
