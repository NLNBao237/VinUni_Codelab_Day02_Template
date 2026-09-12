Công cụ sử dụng: Claude (Anthropic) Bối cảnh: Hoàn thiện worksheet bài tập tìm & phân tích bài toán AI cho các công ty thành viên Vingroup (Vinmec), gồm Phase 1 (Scan), Phase 2 (Quick Problem Cards), Phase 3 (Deep-dive) và Phase 5 (Evaluate).

1. AI đã giúp tôi những gì?

Trong suốt quá trình làm bài, tôi dùng Claude như một trợ lý đồng hành để chuyển hóa các ghi chú rời rạc, viết tắt của mình thành văn bản hoàn chỉnh, đúng format. Cụ thể:

Từ bảng liệt kê 5 bài toán ở Phase 1 (viết khá vắn tắt, có chỗ còn thiếu cột), Claude giúp tôi chọn ra và diễn giải lại thành câu văn mạch lạc cho 3 Quick Problem Card ở Phase 2, đúng cấu trúc khung (Actor, Workflow 3-5 bước, bottleneck, metric, kiến trúc AI).
Ở Phase 3 và Phase 5, Claude giúp tôi hệ thống hóa bài toán "Xử lý yêu cầu bảo hiểm bằng OCR + AI" thành: Problem Statement 6 trường, sơ đồ luồng tương lai (Future-State Flow) có phân tách rõ vai trò của Rule-based / LLM / Agentic Loop, cơ chế Human-in-the-loop và Fallback, và một bảng Checklist đánh giá độ sẵn sàng để đi đến quyết định GO/NOT YET/NO-GO.
AI cũng giúp tôi tiết kiệm thời gian trình bày: tự động format lại thành bảng markdown, khung ASCII-art card, xuất ra file .md để nộp — thay vì tôi phải tự gõ tay từng dòng.

Nhìn chung, AI đóng vai trò như một "người biên tập kiêm cố vấn cấu trúc" — giúp tôi không bị sa đà vào việc trình bày mà tập trung vào nội dung bài toán.
2. AI đã sai/"ảo giác" (hallucination) ở đâu?

Có hai điểm tôi nhận thấy rõ ràng nhất:

a) Bịa số liệu nghe có vẻ hợp lý nhưng không có căn cứ thực tế. Khi làm Phase 2 và Phase 3, Claude tự đưa ra các con số như "15-20 phút/hồ sơ", "tỷ lệ sai sót ~10%", "no-show ~20%". Đây hoàn toàn là số liệu ước lượng do AI tự suy diễn để minh họa cho format, không phải dữ liệu thật của Vinmec. Nếu tôi không tinh ý và nộp thẳng, đây sẽ là một dạng hallucination nguy hiểm — trông rất "có số" nên dễ khiến người đọc tưởng là dữ liệu thật.

Chỉ định rõ thứ tự lựa chọn (top 3 bài toán) và định dạng output mong muốn ("theo format phase 2", "output là file md") để AI không tự ý đổi cấu trúc trình bày.