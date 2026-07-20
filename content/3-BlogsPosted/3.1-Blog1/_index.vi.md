---
title: "Blog 1"
date: 2026-07-08
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Vì sao Epic Games phát triển Lore và cách AWS giúp tối ưu lưu trữ binary assets

Chào mọi người cộng đồng AWS Study Group VN. Mình muốn chia sẻ bài viết này với mọi người.

Nếu bạn từng phát triển game với hàng nghìn texture, model, animation hay engine binaries, chắc hẳn bạn sẽ thấy các hệ thống version control truyền thống như Git không thực sự phù hợp với các file nhị phân. Mỗi lần chỉnh sửa một file vài trăm MB, hệ thống thường phải lưu lại gần như toàn bộ file dưới dạng phiên bản mới, dù chỉ thay đổi một phần rất nhỏ. Theo thời gian, lượng dữ liệu tăng rất nhanh và kéo theo chi phí lưu trữ ngày càng lớn.

Để giải quyết bài toán này, Epic Games đã phát triển **Lore** – một hệ thống version control mã nguồn mở dành riêng cho binary assets. Đồng thời, AWS cũng giới thiệu kiến trúc tham chiếu giúp triển khai Lore trên nền tảng đám mây.

Trong bài viết này, chúng ta sẽ cùng tìm hiểu:
* Lore hoạt động khác gì so với version control truyền thống?
* Kiến trúc triển khai trên AWS gồm những gì?
* Vì sao mô hình này giúp tối ưu chi phí lưu trữ?

---

## 1. Lore hoạt động khác gì?

Thay vì lưu lại toàn bộ file sau mỗi lần chỉnh sửa, Lore chia mỗi binary thành nhiều fragment (chunk) và định danh chúng bằng cryptographic hash. Điều này có nghĩa:
* Chỉ các fragment chứa dữ liệu thay đổi mới được lưu.
* Các fragment đã tồn tại sẽ được tái sử dụng.
* Một fragment xuất hiện ở nhiều file hoặc nhiều dự án chỉ cần lưu một lần.

Nhờ đó, tốc độ tăng dung lượng lưu trữ giảm đáng kể khi dự án phát triển.

---

## 2. Vì sao Lore giúp tiết kiệm chi phí?

AWS chỉ ra ba lợi ích nổi bật:
* **Giảm dung lượng lưu trữ**: Chỉ lưu phần dữ liệu thay đổi thay vì toàn bộ file.
* **Branch gần như miễn phí**: Branch mới chỉ tham chiếu đến các fragment đã có, không tạo thêm dữ liệu nếu asset không thay đổi.
* **Chia sẻ dữ liệu giữa nhiều dự án**: Các fragment trùng lặp chỉ lưu một lần, giúp tối ưu tài nguyên ở quy mô toàn studio.

---

## 3. Kiến trúc Lore trên AWS

AWS triển khai Lore với nhiều dịch vụ quen thuộc, mỗi dịch vụ đảm nhận một vai trò riêng biệt để đảm bảo hiệu suất và khả năng mở rộng:

* **Amazon EC2 (Edge Pods)**: Tiếp nhận kết nối từ client và cache fragment trên ổ NVMe để tăng tốc truy cập.
* **Amazon ECS (Write Tier)**: Xử lý deduplication (loại bỏ dữ liệu trùng lặp) và ghi dữ liệu mới.
* **Amazon S3**: Lưu trữ lâu dài các fragment duy nhất một cách an toàn.
* **Amazon DynamoDB**: Quản lý metadata, lock và thông tin branch với độ trễ tính bằng mili-giây.
* **AWS Cloud Map**: Giúp Edge Pods tự động tìm Write Tier thông qua DNS nội bộ.

Kiến trúc này giúp hệ thống vừa mở rộng dễ dàng, vừa giảm áp lực lên tầng lưu trữ dữ liệu chính.

---

## 4. Khi nào nên sử dụng Lore?

Hệ thống Lore đặc biệt phù hợp với:
* Các studio game có nhiều artist và developer cùng cộng tác.
* Các dự án chứa lượng lớn binary assets.
* Doanh nghiệp phát triển nhiều tựa game cùng lúc.
* Đội ngũ muốn tối ưu chi phí lưu trữ và đồng bộ dữ liệu nhanh chóng trên AWS.

---

## Kết luận

Lore mang đến một cách tiếp cận mới trong quản lý binary assets bằng cách chia nhỏ dữ liệu thành các fragment có thể tái sử dụng, thay vì lưu toàn bộ file sau mỗi lần chỉnh sửa.

Kết hợp với **Amazon EC2, Amazon ECS, Amazon S3, Amazon DynamoDB** và **AWS Cloud Map**, kiến trúc này giúp:
1. Giảm chi phí lưu trữ đáng kể.
2. Tăng tốc quá trình đồng bộ asset.
3. Hỗ trợ tạo nhánh (branching) hiệu quả.
4. Tận dụng chéo dữ liệu giữa nhiều dự án.
5. Dễ dàng mở rộng quy mô khi studio phát triển.

Đối với các studio game làm việc với lượng lớn binary assets, đây là một hướng tiếp cận vô cùng đáng cân nhắc để xây dựng hệ thống version control hiện đại trên AWS.

> **Bài viết gốc:** [How Lore rethinks binary asset storage on AWS](https://aws.amazon.com/blogs/gametech/how-lore-rethinks-binary-asset-storage-on-aws/)  
> **Tags:** #AWS #AWSForGames #EpicGames #Lore #AmazonS3 #AmazonDynamoDB #AmazonEC2 #AmazonECS #CloudArchitecture #GameDevelopment #VersionControl #BinaryAssets #StorageOptimization