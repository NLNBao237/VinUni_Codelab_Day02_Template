# 📄 02-deep-dive-report.md — Báo cáo Phân tích sâu

**Dự án AI được chọn:** Tự động hóa xử lý yêu cầu bảo hiểm (BHYT & bảo hiểm tư nhân) bằng OCR kết hợp AI
**Công ty thành viên:** Vinmec

---

## 🔬 Phase 3 — DEEP-DIVE

### 1. Problem Statement (6-field)

| # | Trường thông tin | Nội dung |
|---|---|---|
| 1 | **Vấn đề (What)** | Nhân viên phòng bảo hiểm/thu ngân phải đối chiếu thủ công thông tin giữa hồ sơ bệnh án, hóa đơn viện phí, kết quả cận lâm sàng và giấy tờ bảo hiểm (BHYT & bảo hiểm tư nhân) để xác định mức chi trả, gây mất thời gian và dễ sai sót. |
| 2 | **Đối tượng chịu ảnh hưởng (Who)** | Trực tiếp: nhân viên xử lý claim (claims staff), thu ngân. Gián tiếp: bệnh nhân (chờ duyệt lâu, dễ bị từ chối claim do sai sót giấy tờ), bộ phận kế toán (đối soát công nợ bảo hiểm chậm). |
| 3 | **Nguyên nhân gốc rễ (Why/Root cause)** | Giấy tờ đến từ nhiều nguồn khác nhau (bệnh viện, công ty bảo hiểm, BHXH) với định dạng không đồng nhất; hệ thống nội bộ chưa tự động trích xuất và đối chiếu dữ liệu; quy định chi trả của mỗi gói bảo hiểm khác nhau nên nhân viên phải nhớ/tra cứu thủ công. |
| 4 | **Tần suất & Quy mô (When/How often & Scale)** | Diễn ra hàng ngày, với khối lượng lớn (hàng trăm hồ sơ claim/ngày ở các bệnh viện lớn); tăng đột biến vào cuối tháng/cuối quý khi đối soát công nợ với công ty bảo hiểm. |
| 5 | **Tác động hiện tại (Impact)** | Mỗi hồ sơ mất trung bình 15-20 phút xử lý thủ công; tỷ lệ sai sót đối chiếu ước tính ~10% dẫn đến claim bị từ chối hoặc phải làm lại; kéo dài thời gian chờ duyệt của bệnh nhân, ảnh hưởng trải nghiệm dịch vụ. |
| 6 | **Mục tiêu mong muốn (Desired outcome)** | Rút ngắn thời gian xử lý 1 hồ sơ xuống dưới 5 phút, giảm tỷ lệ sai sót đối chiếu xuống dưới 2%, tăng tỷ lệ claim được duyệt đúng lần đầu (first-time-right). |

---

### 2. Future-State Flow & AI Fit

**Mô tả quy trình tương lai (có tích hợp AI):**

```
1. Tiếp nhận hồ sơ (scan/upload giấy tờ, hóa đơn, kết quả khám)
        │
        ▼
2. [RULE] Kiểm tra định dạng & tính đầy đủ hồ sơ đầu vào
   (đủ chữ ký, đủ trang, đúng loại giấy tờ theo checklist)
        │
        ▼
3. [OCR + LLM] Trích xuất dữ liệu tự động
   - OCR số hóa văn bản/hình ảnh
   - LLM đọc hiểu ngữ cảnh, chuẩn hóa dữ liệu
     (ngày tháng, mã ICD, chi phí, tên bệnh nhân, mã thẻ BHYT)
        │
        ▼
4. [RULE + LLM] Đối chiếu & xác thực chéo
   - So khớp giữa hóa đơn, kết quả khám, và điều khoản của
     gói bảo hiểm (rule-based cho quy định cứng: hạn mức,
     danh mục loại trừ; LLM cho các trường hợp mô tả tự do)
        │
        ▼
5. [AGENTIC LOOP] Xử lý ngoại lệ tự động
   - Agent tự phát hiện điểm bất thường (thiếu chứng từ, số
     liệu lệch >X%, mã bệnh không khớp điều khoản)
   - Agent tự động yêu cầu bổ sung giấy tờ qua email/app cho
     bệnh nhân/nhân viên khi thiếu thông tin, sau đó tái xử lý
     tự động khi có bổ sung
        │
        ▼
6. [HUMAN-IN-THE-LOOP] Nhân viên xét duyệt
   - Hồ sơ có độ tin cậy cao (confidence score cao, không có
     cờ cảnh báo) → hiển thị cho nhân viên duyệt nhanh (1 click)
   - Hồ sơ có cờ cảnh báo/độ tin cậy thấp → chuyển nhân viên
     xử lý chuyên sâu, kèm giải thích lý do AI gắn cờ
        │
        ▼
7. Phê duyệt / Từ chối → cập nhật hệ thống, thông báo kết quả
```

**Vai trò của từng loại kiến trúc AI:**
- **Rule-based:** áp dụng cho các quy định cố định, có tính pháp lý cao (hạn mức chi trả, danh mục loại trừ BHYT) — cần độ chính xác tuyệt đối, không để LLM "suy diễn".
- **LLM:** dùng cho các tác vụ ngôn ngữ tự nhiên/OCR-hậu xử lý — đọc hiểu, chuẩn hóa, tóm tắt, giải thích lý do gắn cờ cho nhân viên.
- **Agentic Loop:** dùng cho các bước cần chủ động hành động nhiều bước (phát hiện thiếu sót → yêu cầu bổ sung → tái kiểm tra) mà không cần con người can thiệp ở mỗi bước.

**Cơ chế Human-in-the-loop:**
- Nhân viên luôn là người ký duyệt cuối cùng đối với các hồ sơ có giá trị lớn hoặc bị agent gắn cờ bất thường.
- Giao diện hiển thị rõ: dữ liệu AI trích xuất, mức độ tin cậy (confidence score), và lý do nếu bị từ chối/gắn cờ, để nhân viên ra quyết định nhanh và có căn cứ.
- Nhân viên có thể chỉnh sửa/ghi đè kết quả AI; các lần chỉnh sửa này được ghi lại làm dữ liệu huấn luyện lại mô hình (feedback loop).

**Cơ chế Fallback:**
- Nếu OCR/LLM không đọc được hồ sơ (chất lượng ảnh kém, chữ viết tay khó nhận diện) → tự động chuyển sang luồng nhập liệu thủ công truyền thống, không chặn tiến trình xử lý.
- Nếu độ tin cậy trích xuất dưới ngưỡng đặt trước (ví dụ <85%) → bắt buộc qua nhân viên kiểm tra thay vì tự động duyệt.
- Có kênh báo lỗi/khiếu nại để nhân viên hoặc bệnh nhân phản hồi khi hệ thống xử lý sai, đảm bảo luôn có đường thoát về quy trình thủ công.

---

## ✅ Phase 5 — EVALUATE

### Checklist đánh giá độ sẵn sàng

| Tiêu chí | Trạng thái | Ghi chú |
|---|---|---|
| **Dữ liệu sẵn có** (đủ hồ sơ lịch sử để huấn luyện/kiểm thử) | 🟡 Một phần | Có dữ liệu claim lịch sử nhưng nằm rải rác nhiều hệ thống, cần chuẩn hóa trước khi dùng |
| **Chất lượng dữ liệu đầu vào** (giấy tờ scan, độ rõ nét) | 🟡 Một phần | Chứng từ giấy còn nhiều, chất lượng ảnh scan không đồng đều |
| **Tính khả thi kỹ thuật** (OCR + LLM cho tiếng Việt, thuật ngữ y tế) | 🟢 Sẵn sàng | Công nghệ OCR/LLM tiếng Việt đã trưởng thành, có thể fine-tune thuật ngữ y tế/bảo hiểm |
| **Tác động kinh doanh** (tiết kiệm thời gian, giảm sai sót) | 🟢 Sẵn sàng | Tác động rõ ràng, đo lường được (thời gian xử lý, tỷ lệ lỗi) |
| **Chi phí xây dựng & vận hành** | 🟡 Một phần | Cần đầu tư tích hợp hệ thống hiện có (EMR, hệ thống bảo hiểm), chi phí OCR/LLM theo lượng hồ sơ |
| **Rủi ro pháp lý/tuân thủ** (bảo mật dữ liệu y tế, quy định BHYT) | 🟡 Một phần | Dữ liệu y tế nhạy cảm, cần đánh giá tuân thủ trước khi triển khai diện rộng |
| **Sẵn sàng thay đổi/con người** (nhân viên chấp nhận quy trình mới) | 🟢 Sẵn sàng | Nhân viên đang chịu áp lực khối lượng công việc lớn nên có động lực tiếp nhận công cụ hỗ trợ |
| **Cơ chế Human-in-the-loop & Fallback rõ ràng** | 🟢 Sẵn sàng | Đã thiết kế luồng duyệt và fallback thủ công như Phase 3 |

**Chú thích:** 🟢 Sẵn sàng | 🟡 Cần chuẩn bị thêm | 🔴 Chưa sẵn sàng

### Quyết định

> ## 🟡 **NOT YET** — Cần chuẩn bị thêm trước khi triển khai chính thức

**Lý do:**
- Về mặt kỹ thuật và tác động kinh doanh, dự án có tính khả thi cao và đáng đầu tư (2 tiêu chí 🟢 rõ ràng nhất).
- Tuy nhiên, 3 điểm nghẽn cần xử lý trước khi "GO" toàn diện:
  1. **Chuẩn hóa & gom dữ liệu lịch sử** từ các hệ thống rời rạc để có bộ dữ liệu huấn luyện/kiểm thử đủ tin cậy.
  2. **Đánh giá tuân thủ bảo mật dữ liệu y tế** (theo quy định về dữ liệu sức khỏe cá nhân) trước khi đưa OCR/LLM xử lý chứng từ bệnh nhân.
  3. **Chạy pilot ở quy mô nhỏ** (1 khoa hoặc 1 loại bảo hiểm) để đo lường độ chính xác thực tế của OCR/LLM trước khi mở rộng, tránh rủi ro duyệt sai claim ở quy mô lớn.

**Đề xuất bước tiếp theo:** Triển khai pilot 4-6 tuần trên một nhóm claim đơn giản (ví dụ chỉ BHYT nội trú), đo lường độ chính xác trích xuất và tỷ lệ nhân viên phải can thiệp, sau đó ra quyết định GO chính thức cho toàn bộ quy trình.
