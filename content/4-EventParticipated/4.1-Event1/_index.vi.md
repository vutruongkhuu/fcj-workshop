---
title: "Sự kiện 1"
date: 2026-05-09
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---
# Bài thu hoạch “Prompt Engineering, Sơ đồ tư duy AI & Phương pháp BMAD”

| Thông tin | Chi tiết |
|---|---|
| Ngày | 09/05/2026 |
| Địa điểm | Tầng 26, Tòa nhà Bitexco Financial Tower, Phường Sài Gòn, TP.Hồ Chí Minh |
| Vai trò | Người tham dự |

### 1. Giới thiệu

Tài liệu này tổng hợp nội dung từ nhiều nguồn khác nhau, bao gồm:
- Bài thuyết trình: **“Automated Prompt Engineering: Enhancing LLM Output Quality”**
- Sơ đồ tư duy: **“Tương tác hiệu quả với AI & Kiến trúc ứng dụng AWS”**
- Phương pháp phát triển AI: **BMAD (Build More Architect Dreams)**

---

### 2. Nội dung chi tiết

#### 2.1 Bài thuyết trình: Automated Prompt Engineering

**Diễn giả: Nguyễn Tuấn Thịnh**

Bài thuyết trình này giải thích tầm quan trọng của prompt engineering khi làm việc với các mô hình ngôn ngữ lớn (LLM), đồng thời đưa ra các nguyên tắc và công cụ giúp nâng cao chất lượng đầu ra.

**Vì sao Prompt Engineering quan trọng:**
- Câu lệnh quá chung chung thường cho kết quả kém
- Lãng phí token
- Hướng dẫn mơ hồ làm giảm chất lượng và năng suất

**Thành phần của một prompt hiệu quả:**

| Thành phần | Mô tả |
|---|---|
| Vai trò | Xác định persona hoặc vai trò cho AI |
| Hướng dẫn | AI cần thực hiện điều gì |
| Bối cảnh | Thông tin nền cần thiết |
| Dữ liệu đầu vào | Dữ liệu cần xử lý |
| Định dạng đầu ra | Cách trình bày kết quả mong muốn |
| Ví dụ | Mẫu đầu vào và đầu ra |
| Ràng buộc | Giới hạn và quy tắc cần tuân theo |

**Hướng dẫn viết prompt:**
- Rõ ràng và cụ thể
- Dùng ngôn ngữ chỉ dẫn trực tiếp
- Mô tả chính xác điều cần làm
- Cho phép trả lời “Tôi không biết” khi cần
- Chia nhỏ các nhiệm vụ dài thành từng bước

**Kinh tế token:**
- Token là đơn vị xử lý của LLM
- Chi phí được tính theo token đầu vào và đầu ra
- Chi phí khác nhau theo ngôn ngữ, và tiếng Việt thường tốn nhiều token hơn tiếng Anh

**Kỹ thuật nâng cao:**
- Chain-of-Thought (CoT): lập luận từng bước
- Tree-of-Thoughts (ToT): xây nhiều nhánh suy luận
- Self-Consistency: so sánh nhiều hướng suy luận và chọn câu trả lời tốt nhất
- Retrieval-Augmented Generation (RAG): bổ sung kiến thức từ nguồn bên ngoài
- Role Prompting: gán vai trò cụ thể cho mô hình

**Công cụ - Proptimizer:**

Tiện ích mở rộng trên trình duyệt giúp tối ưu prompt tự động dựa trên kiến trúc serverless của AWS:
CloudFront -> S3 -> Cognito -> API Gateway -> Lambda -> Bedrock -> DynamoDB -> CloudWatch

---

#### 2.2 Sơ đồ tư duy: Tương tác AI hiệu quả & Kiến trúc ứng dụng AWS

**Tối ưu hóa tương tác với AI:**
- Sử dụng mô hình KFC (Knowledge, Format, Constraints) để cung cấp bối cảnh, định dạng và ràng buộc rõ ràng

**Phương pháp khuyến nghị:**
- Chia nhỏ nhiệm vụ
- Yêu cầu AI gợi ý phương án
- So sánh nhiều lựa chọn
- Bổ sung dữ liệu có cấu trúc

**Kỹ thuật AI nâng cao:**
- Áp dụng CoT, ToT, Self-Consistency, RAG và Role Prompting để cải thiện kết quả

**Kiến trúc AWS (Serverless):**
CloudFront, S3, Cognito, API Gateway, Lambda, Bedrock, DynamoDB, CloudWatch

---

#### 2.3 Phương pháp BMAD (Build More Architect Dreams)

BMAD là một khung phát triển hướng AI, mã nguồn mở và miễn phí, giúp đội ngũ xây dựng giải pháp theo một quy trình có cấu trúc rõ ràng.

**Các tính năng chính:**
- Các giai đoạn: Phân tích -> Lập kế hoạch -> Kiến trúc -> Triển khai
- Hơn 12 agent chuyên biệt hỗ trợ người dùng theo từng bước
- Tích hợp thực hành Agile
- Trợ lý AI: `bmad-help`
- Party Mode để phối hợp nhiều agent

**Các mô-đun chính:**

| Mô-đun | Mô tả |
|---|---|
| BMM | Khung lõi - 34 quy trình |
| BMB | Xây dựng agent tùy chỉnh |
| TEA | Kiểm thử và tự động hóa |
| BMGD | Phát triển game |
| CIS | Đổi mới và tư duy thiết kế |

**Cài đặt nhanh (yêu cầu Node.js 20+, Python 3.10+, và `uv`):**
```bash
npx bmad-method install
```

**Cộng đồng hỗ trợ:** Discord, YouTube, X/Twitter

---

### 3. Hình ảnh khi tham gia sự kiện

Dưới đây là hình ảnh ghi lại khi tham gia sự kiện:

![Hình ảnh tại Event 1](/images/event1.jpeg)

---

### 4. Kết luận

Sự kiện này đã trình bày rõ ba mảng nội dung riêng biệt: prompt engineering, mô hình tương tác với AI, và phương pháp phát triển BMAD. Mỗi phần có mục tiêu và nguồn gốc khác nhau, nên việc tách bạch nội dung giúp người đọc dễ hiểu và dễ áp dụng hơn. Tổng thể, đây là bộ kiến thức hữu ích để làm việc hiệu quả hơn với các hệ thống AI và kiến trúc đám mây AWS.