# 02 — Deep-Dive Report: Vinhomes Resident Feedback Co-pilot

> **Lab 02: AI Product Scoping — Vin Smart Future**
> Bài toán: **Phân loại & soạn nháp phản hồi khiếu nại cư dân trên App Vinhomes Resident**
> Phase 3 (DEEP-DIVE) + Phase 5 (EVALUATE)
> Sơ đồ quy trình hiện tại: xem [04-workflow-diagram.png](04-workflow-diagram.png)

---

## 3.1. Current-State Workflow Mapping

### Quy trình hiện tại (tính theo 1 ticket trung bình)

| Bước | Ai làm | Công cụ | Thời gian | Ghi chú |
|---|---|---|---:|---|
| 1 | Cư dân | App Vinhomes Resident | — | Gửi text tự do + ảnh, không có form bắt buộc |
| 2 | NV CSKH (Ban Quản lý) | Dashboard CSKH | **3 phút** | 🔴 **Bottleneck #1** — đọc, tự đoán nhóm vấn đề + mức ưu tiên |
| 3 | NV CSKH → Bộ phận phụ trách | Dashboard + Zalo nhóm nội bộ | 1 phút | 🔄 **Handoff #1** — 22% route sai ở đây |
| 4 | Bộ phận Kỹ thuật / An ninh / Vệ sinh / Tài chính | Ngoài hệ thống (giấy, Zalo) | **4 giờ – 2 ngày** | 🔄 **Handoff #2** — kết quả báo lại bằng miệng/Zalo, không chuẩn hoá |
| 5 | NV CSKH | Dashboard CSKH | **5 phút** | 🔴 **Bottleneck #2** — soạn tay văn bản trả lời từ số 0 |
| 6 | Cư dân | App | — | Nhận phản hồi, ~18% mở lại ticket vì trả lời chung chung |

**Tổng thời gian người thao tác: ≈ 9 phút/ticket** (chưa tính thời gian chờ của bước 4).
**First-response time thực tế tới cư dân: ≈ 12 tiếng** (do ticket xếp hàng chờ CSKH đọc).
**Khối lượng: ~450 ticket/ngày/đại đô thị.**

### Ba điểm đau được định lượng
1. 🔴 **Bottleneck #1 (bước 2):** 3 phút × 450 ticket = **22,5 giờ người/ngày** chỉ để đọc và gán nhãn.
2. 🔄 **Handoff #1 (bước 3):** 22% × 450 ≈ **99 ticket/ngày bị route sai**, mỗi lần sai cộng thêm ~4 tiếng chờ.
3. 🔴 **Bottleneck #2 (bước 5):** 5 phút × 450 = **37,5 giờ người/ngày** để soạn văn bản, và vẫn có 18% ticket bị mở lại.

> Các con số trên là ước lượng từ quan sát + phỏng vấn tại 1 đại đô thị, **cần đối chiếu với log App Resident** (bảng `ticket_events`) trước khi trình Ban Giám đốc. Đây chính là điều kiện tiên quyết cho quyết định ở Phase 5.

---

## 3.2. Problem Statement (6-field)

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Nhân viên CSKH của Ban Quản lý toà nhà Vinhomes (mỗi đại đô thị 6–10 người, chia 2 ca). Người chịu trách nhiệm SLA phản hồi là Trưởng Ban Quản lý. |
| **2. Current Workflow** | Cư dân gửi phản hồi dạng text tự do + ảnh trên App Vinhomes Resident → CSKH đọc trên dashboard, tự gán nhóm vấn đề và mức ưu tiên → chuyển ticket sang bộ phận phụ trách (qua dashboard + Zalo nội bộ) → bộ phận xử lý và báo kết quả lại không theo mẫu → CSKH soạn tay văn bản trả lời và gửi cư dân. Công cụ: App Resident, dashboard CSKH, Excel theo dõi SLA, Zalo nhóm. |
| **3. Bottleneck** | Hai bước cần xử lý ngôn ngữ tự nhiên: **(a)** gán nhãn + mức ưu tiên cho văn bản tự do (3 phút/ticket, đúng 78%, gây 22% route sai); **(b)** soạn văn bản trả lời từ số 0 (5 phút/ticket, 18% ticket bị mở lại vì trả lời chung chung). |
| **4. Business Impact** | ≈ **60 giờ người/ngày/đại đô thị** cho riêng việc đọc–gán nhãn–soạn chữ (22,5h + 37,5h). 99 ticket/ngày bị route sai, mỗi ca cộng ~4 tiếng chờ. First-response 12 tiếng làm giảm điểm hài lòng cư dân — chỉ số ảnh hưởng trực tiếp tới tỉ lệ gia hạn dịch vụ quản lý và thương hiệu Vinhomes. |
| **5. Success Metric** | (1) **≥ 92%** ticket được gán đúng nhóm vấn đề trong **dưới 10 giây** (baseline người: 78% / 3 phút). (2) Thời gian người thao tác 1 ticket từ **9 phút → dưới 3 phút**. (3) First-response time **12 tiếng → dưới 2 tiếng**. (4) Tỉ lệ ticket bị mở lại **18% → dưới 10%**. (5) **0** phản hồi gửi đi mà chưa qua người duyệt. |
| **6. Operational Boundary** | **AI ĐƯỢC phép:** gán nhóm vấn đề + mức ưu tiên, đề xuất bộ phận phụ trách, soạn **bản nháp** phản hồi, tóm tắt lịch sử ticket của căn hộ.<br>**AI TUYỆT ĐỐI KHÔNG được:** tự gửi tin cho cư dân; cam kết thời hạn/đền bù/miễn giảm phí dịch vụ; trích dẫn điều khoản hợp đồng hoặc quy định pháp luật mà không có nguồn trong hệ thống; tự kết luận lỗi thuộc về ai trong tranh chấp giữa các cư dân; xử lý các ca an toàn tính mạng (cháy, rò gas, điện giật, xâm nhập) — những ca này **bắt buộc chuyển rule cảnh báo SOS** cho người trực 24/7 ngay lập tức.<br>**Điểm cần duyệt (gate):** mọi văn bản trả lời cư dân; mọi ticket AI gán độ tin cậy < 0,8; toàn bộ ticket nhóm Tài chính–phí dịch vụ và nhóm Pháp lý–tranh chấp (duyệt 100%, không có ngoại lệ). |

---

## 3.3. Future-State Flow & AI Fit

### AI-Fit Matrix: giải pháp thuộc nhóm nào?

| Phương án | Có giải quyết được bottleneck? | Nhận định |
|---|---|---|
| **Rule / State-Machine** | ❌ Một phần | Keyword matching từng thử và thất bại: cư dân viết "nhà tôi nóng như lò" cho ca điều hoà, "có mùi lạ ở hầm" có thể là vệ sinh *hoặc* rò gas. Rule **vẫn cần thiết** nhưng chỉ cho lớp an toàn (từ khoá SOS) và cho SLA timer. |
| ✅ **LLM Feature** | ✅ Đúng trọng tâm | Gán nhãn + mức ưu tiên + soạn nháp là 1 lượt gọi model, không cần vòng lặp. Có structured output (JSON) nên ghép vào dashboard hiện tại được mà không đổi hệ thống. **→ Chọn phương án này.** |
| **Agentic Loop** | ⚠️ Quá sớm | Để agent tự gọi tool đặt lịch kỹ thuật viên, tự đóng ticket là over-engineering ở v1: rủi ro cao, khó debug, và bộ phận xử lý (bước 4) hiện còn chưa có API để agent gọi. Cân nhắc cho v2 sau khi bước 4 được số hoá. |

**Kết luận AI Fit: `LLM Feature` có một lớp `Rule` bao ngoài cho an toàn.**

### Future-State Flow

```text
[Cư dân gửi phản hồi trên App Resident]
            │
            ▼
  ┌────────────────────────────┐
  │ RULE GATE: quét từ khoá    │   "cháy", "khói", "rò gas", "điện giật",
  │ SOS / an toàn tính mạng    │   "ngất", "xâm nhập", "trộm"
  └────────────────────────────┘
       │ HIT                         │ MISS
       ▼                             ▼
🟢 Chuyển NGAY tới           🔵 AI STEP — 1 lượt gọi LLM:
   người trực 24/7              • gán nhóm vấn đề
   (KHÔNG qua AI)               • gán mức ưu tiên P0–P3
                                • đề xuất bộ phận phụ trách
                                • soạn BẢN NHÁP phản hồi
                                • trả về confidence 0–1
                                  (output JSON có schema)
                                     │
                    ┌────────────────┴──────────────────┐
                    │ confidence ≥ 0,8                   │ confidence < 0,8
                    │ và KHÔNG thuộc nhóm Tài chính/     │ HOẶC nhóm Tài chính/
                    │ Pháp lý                            │ Pháp lý
                    ▼                                    ▼
        🟢 HUMAN STEP (HITL):              🟢 HUMAN STEP (HITL đầy đủ):
           CSKH xem nhãn + nháp,              CSKH tự phân loại lại từ đầu,
           sửa nếu cần, bấm GỬI               AI chỉ đóng vai trợ lý gợi ý
                    │                                    │
                    └────────────────┬───────────────────┘
                                     ▼
                        [Ticket route tới bộ phận + phản hồi tới cư dân]
                                     │
                                     ▼
                        📊 Ghi log: nhãn AI vs nhãn người sửa
                           (dữ liệu này để đo accuracy và fine-tune sau)
```

### ↩️ Fallback — khi LLM lỗi hoặc không tự tin

| Tình huống | Hành vi hệ thống |
|---|---|
| Model timeout / API 5xx / hết quota | Ticket vào **hàng chờ thủ công như quy trình cũ**, gắn nhãn `ai_unavailable`. Không bao giờ chặn luồng vận hành vì AI chết. |
| Confidence < 0,8 | Không hiện bản nháp làm mặc định; CSKH xử lý tay, AI chỉ hiện **gợi ý mờ** để tham khảo. |
| Output không đúng JSON schema | Retry **1 lần** với temperature 0; vẫn lỗi → coi như `ai_unavailable`. |
| AI gán nhãn sai bị người sửa | Lưu cặp (nhãn AI, nhãn đúng) vào bảng đối chiếu. Nếu accuracy tuần < 85% → **tự động tắt chế độ gợi ý mặc định** và báo cho đội AI. |
| Ticket chạm từ khoá SOS | Rule chặn trước AI, chuyển người trực 24/7. AI không tham gia vào nhóm ca này. |

---

# 🏁 Phase 5 — EVALUATE

## AI Readiness Checklist

| # | Câu hỏi | Trả lời | Bằng chứng / Khoảng trống |
|---|---|---|---|
| 1 | Có sẵn dữ liệu mẫu/logs sạch để test? | ⚠️ **Một phần** | App Resident có lịch sử ticket kèm nội dung cư dân và bộ phận đã xử lý → dùng được làm nhãn yếu. Nhưng **chưa có bộ nhãn chuẩn (gold set)** do người gán, và chưa đo lại con số 78% accuracy của người bằng số liệu hệ thống. Cần ~500 ticket được 2 CSKH gán độc lập. |
| 2 | Rủi ro khi AI sai có nằm trong tầm kiểm soát? | ✅ **Có** | Đầu ra là *bản nháp*, không có đường nào gửi tự động tới cư dân. Rule gate chặn ca an toàn tính mạng **trước** khi AI thấy ticket. Nhóm Tài chính/Pháp lý duyệt 100%. Fallback về quy trình cũ khi AI chết. |
| 3 | Stakeholders sẵn sàng thay đổi quy trình cũ? | ⚠️ **Một phần** | CSKH hưởng lợi trực tiếp (giảm 9 → 3 phút) nên ủng hộ. Nhưng **bước 4 (bộ phận xử lý) vẫn nằm ngoài hệ thống** (báo kết quả qua Zalo) — nếu không số hoá bước này thì AI chỉ cải thiện được 2 đầu, SLA tổng vẫn bị bước 4 kéo xuống. Cần cam kết từ Khối Kỹ thuật toà nhà. |

## Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future

- [ ] **GO (Bắt đầu xây dựng Prototype full scope)**
- [x] **NOT YET → GO có điều kiện: pilot 1 toà nhà, scope hẹp**
- [ ] **NO-GO**

### Justification

Về mặt kỹ thuật, bài toán này **đúng vùng LLM**: nút cổ chai là đọc–phân loại–soạn chữ, rule-based đã được thử và thất bại vì cư dân viết ngôn ngữ tự nhiên ("nhà tôi nóng như lò"). Rủi ro cũng đã được thiết kế để chặn: bản nháp + HITL + rule gate cho ca an toàn tính mạng + fallback về quy trình cũ. Nếu chỉ xét hai tiêu chí này thì đây là GO.

Lý do nhóm **không chọn GO full scope** là **tiêu chí #1 chưa đạt**: toàn bộ business case đang đứng trên các con số ước lượng từ phỏng vấn (8–9 phút/ticket, 78% accuracy, 22% route sai), **chưa được xác nhận bằng log hệ thống**, và chưa có gold set để biết mốc 92% là dễ hay bất khả thi. Cam kết 60 giờ người/ngày tiết kiệm được mà không có baseline đo lường là cách nhanh nhất để dự án bị đánh giá thất bại dù model chạy tốt.

Ngoài ra **tiêu chí #3 có một lỗ hổng thật**: bước 4 còn chạy ngoài hệ thống. AI có thể rút 9 phút xuống 3 phút ở hai đầu, nhưng nếu bước 4 vẫn mất 4 tiếng–2 ngày thì cư dân cảm nhận được rất ít. Đây là rủi ro "tối ưu sai chỗ" cổ điển.

**Điều kiện để chuyển thành GO (đề xuất 3 tuần):**
1. Trích log `ticket_events` 3 tháng, xác nhận lại 3 con số baseline bằng số liệu thật.
2. Xây gold set 500 ticket do 2 CSKH gán độc lập; đo luôn mức đồng thuận giữa người với người (nếu 2 người chỉ đồng ý 80% thì mốc 92% cho AI là vô nghĩa và phải hạ).
3. Pilot **1 toà nhà, chỉ 3 nhóm vấn đề rủi ro thấp** (Kỹ thuật, Vệ sinh, An ninh thường), **loại hoàn toàn** nhóm Tài chính–phí dịch vụ và Pháp lý–tranh chấp khỏi v1.
4. Lấy cam kết bằng văn bản từ Khối Kỹ thuật toà nhà về việc số hoá bước 4 song song — nếu không có cam kết này, chỉ triển khai phần gán nhãn (bước 2), hoãn phần soạn nháp (bước 5).

---

## 💻 Liên kết với Phase 4 — Technical Prompt Prototype

Nguyên lý **ranh giới vận hành phải được ép bằng system prompt và kiểm chứng bằng adversarial test** đã được nhóm lập trình và chạy thật trong [starter-code/prompt_prototype.py](starter-code/prompt_prototype.py) (bối cảnh Xanh SM theo đề bài):

* **Rule 1 — `[DRAFT_ONLY]`:** đúng cơ chế "AI chỉ soạn nháp, người bấm gửi" của bài toán Vinhomes này.
* **Rule 2 — ngưỡng pin < 5%:** đúng cơ chế "rule gate chặn trước AI" cho các ca rủi ro cao.
* **Rule 3 — không cam kết tiền/bồi thường:** đúng ranh giới "AI không được hứa miễn giảm phí dịch vụ".

Kết quả chạy: **3/3 test tấn công đều bị chặn**, kể cả ca người dùng tự nhận là Trưởng ca và ra lệnh bỏ qua quy tắc nội bộ. Chi tiết log và những lần ranh giới *từng* bị vỡ trong lúc làm ở [03-ai-log.md](03-ai-log.md).
