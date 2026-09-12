# Deep-Dive Report - Phân loại và điều hướng phản ánh cư dân Vinhomes

## 1. Phạm vi và giả định

Báo cáo này phân tích quy trình tiếp nhận phản ánh của cư dân trên ứng dụng Vinhomes Resident. Prototype chỉ hỗ trợ **phân loại và đề xuất queue xử lý**; nhân viên CSKH vẫn là người kiểm tra và xác nhận trước khi ticket được chuyển. Các số liệu dưới đây là baseline giả định để lập kế hoạch thử nghiệm, cần được đối chiếu với log CRM thực tế trước khi triển khai.

## 2. Current-State Workflow Mapping

```text
[Cư dân gửi phản ánh qua App]
             |
             | Handoff: App -> CRM
             v
[1. CSKH mở ticket và đọc nội dung] ---- 2 phút
             |
             v
[2. Tra cứu thông tin căn hộ/tòa nhà] - 1 phút
             |
             v
[3. Đọc, phân loại loại sự cố và SLA] - 3 phút 🔴 BOTTLENECK
             |
             | Handoff: CSKH -> Ban quản lý / Kỹ thuật / An ninh
             v
[4. Gán queue và chuyển ticket] ------- 3 phút 🔴 BOTTLENECK
             |
             v
[5. Bộ phận nhận việc xác nhận] ------- 1 phút
             |
             v
[6. Cập nhật trạng thái cho cư dân] ---- 1 phút

Tổng thời gian xử lý ban đầu: khoảng 11 phút/ticket.
```

### Chi tiết handoff và rủi ro

| Bước | Người/hệ thống | Input | Output | Thời gian | Rủi ro |
|---|---|---|---|---:|---|
| 1 | CSKH + CRM | Nội dung, ảnh, mã cư dân | Ticket được mở | 2 phút | Bỏ sót ảnh hoặc lịch sử trao đổi |
| 2 | CSKH + CRM | Mã căn hộ/tòa nhà | Thông tin địa điểm | 1 phút | Cư dân nhập thiếu hoặc sai địa chỉ |
| 3 | CSKH | Mô tả tự nhiên, ảnh | Nhãn sự cố, mức độ, SLA | 3 phút | Từ ngữ mơ hồ, gán sai mức độ khẩn cấp |
| 4 | CSKH -> bộ phận xử lý | Nhãn và thông tin ticket | Queue nhận việc | 3 phút | Chuyển nhầm đội, phải chuyển lại |
| 5 | Ban quản lý/kỹ thuật/an ninh | Ticket đã route | Xác nhận tiếp nhận | 1 phút | Handoff không rõ người chịu trách nhiệm |
| 6 | CSKH/CRM | Trạng thái xử lý | Cập nhật cho cư dân | 1 phút | Cập nhật chậm, cư dân gọi lại nhiều lần |

### Bottleneck chính

Bước 3 và 4 chiếm khoảng 6/11 phút xử lý ban đầu. Mỗi ngày giả định có 600 ticket, tương đương khoảng 60 giờ công cho khâu tiếp nhận và điều hướng. Với tỷ lệ chuyển nhầm queue 12%, ticket phải đi qua thêm một vòng handoff, làm tăng thời gian phản hồi và giảm khả năng đáp ứng SLA.

## 3. Problem Statement - 6 fields

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Nhân viên CSKH tại ban quản lý Vinhomes là người đọc nội dung, kiểm tra thông tin cư dân, xác định loại sự cố và chuyển ticket cho đội phù hợp. Ban quản lý tòa nhà, kỹ thuật và an ninh là các đội nhận việc. |
| **2. Current Workflow** | Cư dân gửi phản ánh qua App Vinhomes Resident. Ticket được đồng bộ vào CRM; CSKH đọc mô tả và hình ảnh, tra cứu tòa nhà, tự gán loại sự cố/mức độ ưu tiên rồi chuyển sang queue xử lý. Quy trình hiện mất khoảng 11 phút cho một ticket, trong đó phân loại và route chiếm 6 phút. |
| **3. Bottleneck** | Mô tả của cư dân không theo mẫu cố định, có thể chứa tiếng lóng, nhiều vấn đề trong cùng một ticket hoặc thiếu vị trí. CSKH phải suy luận loại sự cố và SLA rồi chọn queue bằng tay, dẫn đến khoảng 12% ticket bị chuyển nhầm hoặc phải chuyển lại. |
| **4. Business Impact** | Với baseline 600 ticket/ngày, khâu tiếp nhận tiêu tốn khoảng 60 giờ công/ngày. Ticket chuyển nhầm làm chậm phản hồi, tăng số lần cư dân gọi lại và khiến các đội vận hành phải kiểm tra ticket ngoài phạm vi. Tác động chính là giảm SLA xử lý và tăng chi phí nhân sự, không phải tự động hóa quyết định an toàn. |
| **5. Success Metric** | Trong pilot 4 tuần: (1) 90% ticket có đề xuất queue trong dưới 10 giây; (2) macro-F1 tối thiểu 0.85 trên 10 nhóm sự cố; (3) giảm thời gian phân loại từ 6 phút xuống dưới 1 phút/ticket; (4) giảm tỷ lệ chuyển nhầm queue từ 12% xuống dưới 4%; (5) 100% ticket độ tin cậy thấp được chuyển sang hàng chờ review thủ công. |
| **6. Operational Boundary** | AI được phép đọc nội dung ticket, metadata đã được phân quyền và ảnh mô tả; trích xuất tòa nhà, loại sự cố, mức độ ưu tiên; đề xuất nhãn, SLA và queue trong taxonomy đã phê duyệt. AI **không được** tự đóng ticket, tự cam kết thời gian sửa chữa, tự trả lời cư dân, tự xử lý vấn đề phí/pháp lý hoặc tự chuyển ticket khi confidence dưới 0.85. Nhân viên CSKH phải duyệt đề xuất trước khi route; mọi quyết định được lưu log. Nếu thiếu thông tin, AI phải yêu cầu bổ sung hoặc fallback về phân loại thủ công. |

## 4. Future-State Flow và AI Fit

### AI-Fit Matrix

| Phương án | Vai trò | Đánh giá |
|---|---|---|
| Rule / State Machine | Kiểm tra mã tòa nhà, taxonomy, SLA và ngưỡng confidence | Bắt buộc để giữ output trong tập nhãn được phép |
| LLM Feature | Hiểu mô tả tự nhiên, chuẩn hóa nội dung, đề xuất nhãn và queue | Phù hợp nhất cho prototype |
| Agentic Loop | Tự gọi nhiều hệ thống và tự chuyển ticket | Chưa phù hợp vì tăng blast radius và không cần thiết cho quy trình một lần phân loại |

**Kiến trúc đề xuất:** Rule + LLM Feature. Rule kiểm soát dữ liệu và các trường được phép; LLM chỉ đưa ra đề xuất có cấu trúc. Đây không phải agent tự trị.

### Future-State Flow

```text
[1. Ticket mới vào CRM]
          |
          v
[2. Rule kiểm tra quyền truy cập, mã tòa nhà, file đính kèm]
          |
          v
[3. 🔵 AI trích xuất và đề xuất]
   loại sự cố / mức độ / SLA / queue / confidence
          |
          +---- confidence < 0.85 hoặc thiếu dữ liệu ----+
          |                                                |
          v                                                v
[4. 🟢 CSKH review đề xuất]                       [↩️ Fallback]
          |                                  CSKH phân loại thủ công,
          |                                  yêu cầu cư dân bổ sung
          |                                  hoặc chuyển supervisor
          v
[5. Rule kiểm tra nhãn và SLA lần cuối]
          |
          v
[6. 🟢 CSKH bấm duyệt -> CRM chuyển ticket]
          |
          v
[7. Bộ phận xử lý xác nhận và cập nhật trạng thái]
```

### Cơ chế Human-in-the-loop

- CSKH nhìn thấy nội dung gốc, nhãn AI đề xuất, confidence và lý do ngắn gọn trước khi duyệt.
- CSKH có thể sửa nhãn, queue hoặc mức ưu tiên; bản sửa được lưu để đánh giá chất lượng và cải thiện taxonomy.
- Các nhóm nhạy cảm như phí, pháp lý, tranh chấp, an ninh hoặc nguy cơ an toàn luôn yêu cầu supervisor review.
- AI không gửi thông báo ra ngoài và không thay thế phán đoán của nhân viên.

### Fallback và xử lý lỗi

1. Nếu model timeout, trả JSON không hợp lệ hoặc confidence thấp, CRM đưa ticket vào hàng chờ thủ công.
2. Nếu thiếu tòa nhà hoặc thông tin không đủ, CSKH gửi mẫu yêu cầu bổ sung, không đoán queue.
3. Nếu nhãn AI không thuộc taxonomy, rule validator loại output và dùng quy trình thủ công.
4. Nếu AI phân loại sai, nhân viên sửa trực tiếp; hệ thống lưu input, output, quyết định cuối và nguyên nhân sửa để audit.

## 5. Evaluate - AI Readiness Checklist

| Câu hỏi | Đánh giá | Bằng chứng cần thu thập / hành động |
|---|---|---|
| Có dữ liệu mẫu/log sạch để test không? | **Một phần** | Có thể lấy ticket CRM đã đóng trong 3-6 tháng, nhưng cần khử thông tin cá nhân, chuẩn hóa 10 nhãn và tạo tập validation do CSKH gán nhãn lại. |
| Rủi ro khi AI sai có kiểm soát được không? | **Có, trong scope hẹp** | AI chỉ đề xuất; CSKH duyệt trước khi route; có confidence threshold, rule validator, audit log và fallback thủ công. Không đưa quyết định phí/pháp lý vào pilot. |
| Stakeholders sẵn sàng thay đổi workflow không? | **Cần xác nhận** | Pilot với một ban quản lý và một ca CSKH; đo thời gian xử lý, tỷ lệ sửa đề xuất và mức hài lòng trước khi mở rộng. |

## 6. Quyết định

### **NOT YET - cần chuẩn bị dữ liệu và baseline trước khi xây production**

Đề xuất vẫn được **GO cho prototype offline/pilot có kiểm soát**, nhưng chưa nên triển khai production ngay. Lý do là dữ liệu lịch sử cần được làm sạch và thống nhất taxonomy; đồng thời cần baseline chính xác về thời gian phân loại, tỷ lệ chuyển nhầm và SLA theo từng tòa nhà. Sau pilot 4 tuần, dự án có thể chuyển sang GO nếu đạt đủ các ngưỡng: macro-F1 tối thiểu 0.85, 90% đề xuất dưới 10 giây, tỷ lệ chuyển nhầm dưới 4%, không có sự cố liên quan dữ liệu nhạy cảm và CSKH xác nhận workflow không làm tăng thời gian review.

### Phạm vi prototype được duyệt

- Chỉ dùng dữ liệu đã ẩn danh và 5 nhóm sự cố có volume cao nhất.
- Chỉ trả về đề xuất có cấu trúc: `category`, `priority`, `queue`, `confidence`, `missing_fields`.
- Chỉ chạy trên ticket mô phỏng hoặc replay offline; chưa tự động cập nhật CRM production.
- Đánh giá theo tập test cố định và review thủ công toàn bộ case confidence thấp.
