# Vin Smart Future - Phase 1 & Phase 2

## Bối cảnh lựa chọn

Tôi đóng vai AI Product Engineer tại Vin Smart Future. Các cơ hội dưới đây được quét từ những quy trình vận hành có khối lượng lặp lại cao, nhiều thao tác thủ công, hoặc gây pain trực tiếp cho khách hàng và nhân viên tuyến đầu. Các số liệu là baseline ước tính dùng để xác định phạm vi prototype; khi triển khai cần xác minh lại bằng log vận hành thực tế.

## Phase 1 - SCAN

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|---|---|---|
| 1 | Vinhomes | Lặp lại | Phân loại phản ánh của cư dân trên ứng dụng (mất nước, hỏng đèn, tiếng ồn) và route đúng ban quản lý/tòa nhà. |
| 2 | Vinhomes | Tốn thời gian | Nhân viên CSKH đọc lịch sử trao đổi, tra cứu quy định và soạn phản hồi cho các yêu cầu của cư dân; mỗi case mất khoảng 12 phút. |
| 3 | Xanh SM | Pain từ người khác | Điều phối viên phải đọc tin nhắn và nghe cuộc gọi của tài xế để xác định lý do hủy chuyến, khiến việc tìm nguyên nhân theo ngày bị chậm. |
| 4 | VinFast | Lặp lại | Đối chiếu dữ liệu phiên sạc từ các trạm đối tác với hóa đơn và dữ liệu tài chính mỗi tuần; các dòng sai lệch phải kiểm tra thủ công. |
| 5 | Vinmec | Tốn thời gian | Bác sĩ tổng hợp bệnh án, kết quả xét nghiệm và ghi chú để soạn bản nháp tóm tắt xuất viện, mất khoảng 20-30 phút mỗi bệnh nhân. |
| 6 | Vinpearl | AI-upgrade | Tổng hợp review từ nhiều kênh, phát hiện phàn nàn khẩn cấp và gửi đúng bộ phận thay vì để quản lý đọc thủ công từng review. |

### Tiêu chí chọn top 3

Tôi chọn các bài toán #1, #2 và #3 vì chúng có dữ liệu đầu vào tương đối rõ, workflow lặp lại với điểm handoff cụ thể, và có thể triển khai AI ở vai trò hỗ trợ phân loại hoặc soạn nháp. Cả ba đều giữ được human-in-the-loop trước khi gửi phản hồi hoặc thay đổi trạng thái vận hành.

## Phase 2 - QUICK-ASSESS

### Quick Problem Card #1 - Phân loại phản ánh cư dân

**Bài toán (1 câu):** Phản ánh cư dân gửi qua App Vinhomes Resident đang được đọc và chuyển thủ công, làm chậm việc đưa yêu cầu đến đúng đội xử lý.

**Công ty thành viên:** [x] Vinhomes

**Ai đang đau (Actor):** Cư dân phải chờ cập nhật; nhân viên CSKH và ban quản lý tòa nhà phải đọc, phân loại và chuyển từng yêu cầu.

**Workflow thủ công hiện tại:**

1. Cư dân gửi phản ánh kèm mô tả/hình ảnh qua ứng dụng.
2. Nhân viên CSKH mở ticket và đọc nội dung.
3. Nhân viên gán thủ công nhóm sự cố, mức độ ưu tiên và tòa nhà.
4. Nhân viên chuyển ticket cho kỹ thuật, an ninh hoặc ban quản lý phù hợp.
5. Bộ phận nhận việc xác nhận và cập nhật trạng thái cho cư dân.

**Bước tốn thời gian/lỗi nhất:** Bước 3-4, khoảng 6 phút/ticket; dễ gán nhầm nhóm khi mô tả dùng từ địa phương hoặc thiếu thông tin.

**AI có thể hỗ trợ ở bước:** Bước 3: trích xuất tòa nhà, loại sự cố, mức độ ưu tiên và đề xuất queue xử lý từ nội dung ticket/hình ảnh mô tả. Bước 4 vẫn cần nhân viên xác nhận trong giai đoạn đầu.

**Metric thành công:** 90% ticket được đề xuất đúng queue trong dưới 10 giây; giảm thời gian phân loại từ 6 phút xuống dưới 1 phút; giảm tỷ lệ chuyển nhầm queue từ 12% xuống dưới 4%.

**Quick Architecture:** [x] Rule + LLM Feature  [ ] Agent

**Lý do:** Rule xử lý các trường xác định như mã tòa nhà và SLA; LLM chỉ chuẩn hóa ngôn ngữ tự nhiên và đề xuất nhãn. Agent tự trị là chưa cần thiết vì mỗi ticket chỉ cần một lần phân loại và route.

---

### Quick Problem Card #2 - Draft phản hồi CSKH Vinhomes

**Bài toán (1 câu):** Nhân viên CSKH mất nhiều thời gian tra cứu quy định và soạn phản hồi nhất quán cho các yêu cầu thường gặp của cư dân.

**Công ty thành viên:** [x] Vinhomes

**Ai đang đau (Actor):** Nhân viên CSKH bị quá tải vào giờ cao điểm; cư dân phải chờ lâu cho những câu hỏi về phí, tiện ích, đăng ký thi công và gửi xe.

**Workflow thủ công hiện tại:**

1. Nhân viên mở ticket và đọc toàn bộ lịch sử trao đổi.
2. Nhân viên tìm quy định mới nhất trong thư mục nội bộ hoặc hỏi ban quản lý.
3. Nhân viên tự viết câu trả lời, kiểm tra cách xưng hô và thông tin áp dụng cho từng dự án.
4. Trưởng ca hoặc nhân viên phụ trách duyệt các case nhạy cảm.
5. Nhân viên gửi phản hồi và ghi chú kết quả vào CRM.

**Bước tốn thời gian/lỗi nhất:** Bước 2-3, khoảng 12 phút/ticket; lỗi thường gặp là dùng nhầm phiên bản quy định hoặc bỏ sót điều kiện áp dụng.

**AI có thể hỗ trợ ở bước:** Bước 2-3: tìm đoạn quy định liên quan trong kho tài liệu đã duyệt và tạo bản nháp có trích dẫn nguồn, không tự gửi.

**Metric thành công:** Giảm thời gian soạn phản hồi từ 12 phút xuống dưới 3 phút; 95% bản nháp có trích dẫn đúng tài liệu; 100% case liên quan phí, pháp lý hoặc tranh chấp được đánh dấu để người có thẩm quyền duyệt.

**Quick Architecture:** [ ] Rule  [x] LLM Feature  [ ] Agent

**Lý do:** LLM phù hợp để hiểu câu hỏi và chuyển nội dung quy định thành lời đáp dễ hiểu. Kho tài liệu được kiểm soát và các rule về chủ đề nhạy cảm giới hạn rủi ro; không cho phép agent tự trao đổi nhiều bước với cư dân.

---

### Quick Problem Card #3 - Phân tích lý do hủy chuyến Xanh SM

**Bài toán (1 câu):** Dữ liệu lý do khách hủy chuyến nằm rải rác trong cuộc gọi, tin nhắn và ghi chú tài xế nên đội vận hành khó phát hiện pattern để giảm hủy chuyến.

**Công ty thành viên:** [x] Xanh SM (GSM)

**Ai đang đau (Actor):** Nhân viên vận hành và đội sản phẩm cần báo cáo nguyên nhân; khách hàng chịu trải nghiệm đặt xe không ổn định; tài xế phải giải thích lại sự cố nhiều lần.

**Workflow thủ công hiện tại:**

1. Hệ thống gom danh sách các chuyến bị hủy trong ngày.
2. Nhân viên nghe mẫu cuộc gọi, đọc tin nhắn và ghi chú của tài xế.
3. Nhân viên gán một trong các nhóm nguyên nhân bằng bảng tính.
4. Trưởng nhóm kiểm tra mẫu và tổng hợp báo cáo theo khu vực/khung giờ.
5. Đội vận hành đề xuất thay đổi và theo dõi tuần tiếp theo.

**Bước tốn thời gian/lỗi nhất:** Bước 2-3, khoảng 8 phút/chuyến được chọn để kiểm tra; cách gán nhãn không đồng nhất khiến báo cáo giữa các ca khó so sánh.

**AI có thể hỗ trợ ở bước:** Bước 2-3: chuyển lời nói thành văn bản, trích xuất nguyên nhân và gán nhãn theo taxonomy đã khóa; nhân viên kiểm tra các mẫu độ tin cậy thấp.

**Metric thành công:** Tự động xử lý 85% bản ghi trong dưới 30 giây; macro-F1 của 10 nhãn nguyên nhân đạt tối thiểu 0.85; giảm thời gian lập báo cáo hằng ngày từ 4 giờ xuống dưới 45 phút.

**Quick Architecture:** [x] Rule + LLM Feature  [ ] Agent

**Lý do:** LLM tốt ở việc hiểu cách diễn đạt khác nhau trong cuộc gọi, còn rule đảm bảo chỉ được chọn nhãn trong taxonomy đã phê duyệt. Đây là quy trình phân tích batch, không cần agent tự thực hiện hành động trên hệ thống điều vận.

## Kết luận Phase 2

Trong ba card, bài toán **Phân loại phản ánh cư dân** có phạm vi prototype nhỏ nhất và metric dễ đo nhất. Đây là ứng viên ưu tiên để đi tiếp vào Deep-Dive: AI chỉ đề xuất nhãn/queue, nhân viên vẫn duyệt trước khi ticket được chuyển, và có thể fallback về quy trình phân loại thủ công khi độ tin cậy thấp hoặc thiếu dữ liệu.