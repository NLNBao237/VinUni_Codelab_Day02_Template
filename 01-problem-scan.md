# 01 — Problem Scan & Quick Cards

> **Lab 02: AI Product Scoping — Vin Smart Future**
> Người thực hiện: **Nguyễn Lê Ngọc Bảo** (Trưởng nhóm) — branch `Bao`
> Phase 1 (SCAN) + Phase 2 (QUICK-ASSESS)

---

## 🏛️ Bối cảnh: Tôi là ai trong bài toán này?

Tôi là AI Product Engineer tại **Vin Smart Future**, được phân công làm việc cùng Khối Vận hành Dịch vụ Khách hàng của các công ty thành viên. Trong 2 tuần khảo sát, tôi tập trung quan sát những nơi mà **ngôn ngữ tự nhiên là nút cổ chai**: chỗ nào con người đang phải *đọc — hiểu — phân loại — soạn chữ* bằng tay, chỗ đó là ứng viên cho LLM. Ngược lại, chỗ nào chỉ cần so sánh số và tra bảng, tôi chủ động loại vì rule-based code rẻ và chắc hơn.

---

# 🔍 Phase 1 — SCAN: Bảng quét cơ hội

Quét qua vận hành các công ty thành viên Vingroup bằng **4 Lenses**. Các con số dưới đây là **ước lượng vận hành** thu được từ phỏng vấn/quan sát, cần đối chiếu lại với log hệ thống trước khi đưa vào business case chính thức.

| # | Subsidiary | Lens | Mô tả ngắn bài toán | Tổn thất ước tính |
|---|------------|------|---------------------|-------------------|
| 1 | **Vinhomes** | Tốn thời gian | Nhân viên CSKH đọc thủ công từng phản hồi/khiếu nại của cư dân trên App Vinhomes Resident, tự phân loại và tự soạn văn bản trả lời cho từng ca. | ~8 phút/ticket × ~450 ticket/ngày/đại đô thị |
| 2 | **Vinhomes** | Lặp lại | Định tuyến (routing) ticket tới đúng bộ phận (Kỹ thuật / An ninh / Vệ sinh / Tài chính–phí dịch vụ) phải qua 2–3 lần chuyển tay vì phân loại sai ở bước đầu. | ~22% ticket bị route sai, mỗi lần sai +4 tiếng chờ |
| 3 | **Xanh SM** | Pain từ người khác | Tài xế báo sự cố pin/hết pin giữa đường, điều phối viên tra cứu vị trí và trạm sạc trống thủ công rồi soạn chỉ dẫn. | ~12–15 phút/lượt, tài xế mất cuốc |
| 4 | **VinFast** | Lặp lại | Đối chiếu hóa đơn sạc điện với log sản lượng của trạm sạc đối tác hằng tuần. | ~6 giờ/tuần/nhân sự kế toán |
| 5 | **Vinmec** | Tốn thời gian | Bác sĩ soạn tóm tắt hồ sơ xuất viện (discharge summary) từ bệnh án dài. | ~20–30 phút/bệnh nhân |
| 6 | **Vinpearl / VinWonders** | AI-upgrade | Chatbot CSKH trả lời rập khuôn, không đọc được chính sách đổi/hủy vé theo mùa nên khách phải chờ người thật. | ~35% hội thoại phải escalate cho người |

### 🤖 Ghi chú dùng AI ở Phase này
Tôi dùng LLM để brainstorm danh sách ban đầu, nhưng **bỏ 3 ý tưởng AI gợi ý** vì chúng mô tả quy trình không tồn tại ở Vingroup (AI bịa ra một "trung tâm điều phối liên công ty" và một "hệ thống chấm điểm cư dân"). Chi tiết ở [03-ai-log.md](03-ai-log.md).

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Top 3 được chọn từ bảng SCAN: **#1+#2 (Vinhomes — khiếu nại cư dân)**, **#4 (VinFast — đối chiếu hóa đơn sạc)**, **#6 (Vinpearl — chatbot đổi/hủy vé)**.

## Card #1 — Vinhomes: Phân loại & soạn nháp phản hồi khiếu nại cư dân

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Mỗi phản hồi của cư dân trên App Vinhomes │
│ Resident đều phải được một nhân viên CSKH đọc, tự phân loại │
│ và tự soạn văn bản trả lời từ đầu.                          │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên CSKH Ban Quản lý toà nhà     │
│ (người chịu SLA), cư dân (người chờ), Trưởng BQL (người bị  │
│ escalate khi quá hạn).                                      │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Cư dân gửi phản hồi trên App (ảnh + text tự do)        │
│   → 2. CSKH đọc, tự gán nhóm vấn đề + mức ưu tiên           │
│   → 3. Chuyển ticket sang bộ phận phụ trách (🔄 handoff)     │
│   → 4. Bộ phận xử lý, báo kết quả lại cho CSKH (🔄 handoff)  │
│   → 5. CSKH soạn tay văn bản trả lời, gửi cư dân            │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 + Bước 5            │
│   (⏱ ~8 phút/ticket; 22% ticket bị phân loại & route sai)    │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 và Bước 5      │
│   (gán nhãn + mức ưu tiên, rồi soạn BẢN NHÁP phản hồi)      │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│   - Thời gian xử lý 1 ticket: 8 phút ──> dưới 3 phút        │
│   - Độ đúng của phân loại: 78% ──> ≥ 92%                    │
│   - First-response time: 12 tiếng ──> dưới 2 tiếng          │
│                                                             │
│ Quick Architecture: [x] LLM Feature (+ Rule cho ưu tiên SOS) │
└─────────────────────────────────────────────────────────────┘
```

## Card #2 — VinFast: Đối chiếu hóa đơn sạc điện với log trạm đối tác

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Kế toán phải so khớp thủ công từng dòng   │
│ hóa đơn của trạm sạc đối tác với log sản lượng sạc nội bộ.  │
│ Công ty thành viên: [x] VinFast                             │
│                                                             │
│ Ai đang đau (Actor)? Kế toán vận hành trạm sạc              │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Nhận file hóa đơn từ đối tác (Excel/PDF, mỗi nơi 1 mẫu)│
│   → 2. Export log sản lượng từ hệ thống nội bộ              │
│   → 3. So khớp từng dòng trên Excel (VLOOKUP + mắt thường)  │
│   → 4. Viết email truy vấn các dòng bị lệch                 │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 (⏱ ~6 giờ/tuần)     │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Chủ yếu là Bước 1     │
│   (đọc PDF/Excel nhiều định dạng → chuẩn hoá) và Bước 4     │
│   (soạn email truy vấn). Bước 3 nên là RULE, không dùng LLM.│
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│   Giảm thời gian đối chiếu từ 6 giờ ──> dưới 1 giờ/tuần,    │
│   sai số đối chiếu = 0 đồng (bắt buộc khớp tuyệt đối).      │
│                                                             │
│ Quick Architecture: [x] Rule (lõi) + [x] LLM (chỉ ở 2 rìa)  │
└─────────────────────────────────────────────────────────────┘
```

## Card #3 — Vinpearl / VinWonders: Chatbot xử lý đổi & hủy vé

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Chatbot CSKH trả lời rập khuôn, không tra │
│ được chính sách đổi/hủy vé theo từng mùa và từng loại combo,│
│ nên 35% hội thoại phải chuyển cho người thật.               │
│ Công ty thành viên: [x] Vinpearl / VinWonders               │
│                                                             │
│ Ai đang đau (Actor)? Khách du lịch + tổng đài viên          │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Khách hỏi chatbot về đổi/hủy vé                        │
│   → 2. Chatbot trả lời kịch bản cứng, khách không thoả mãn  │
│   → 3. Escalate sang tổng đài viên (🔄 handoff, chờ 5-20 min)│
│   → 4. Tổng đài viên mở file chính sách, tra tay rồi trả lời│
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3-4 (⏱ ~9 phút/ca)    │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 (RAG trên tài  │
│   liệu chính sách thay vì kịch bản cứng)                    │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│   Tỉ lệ escalate 35% ──> dưới 15%, và 0 trường hợp chatbot  │
│   tự ý xác nhận hoàn tiền.                                  │
│                                                             │
│ Quick Architecture: [x] LLM Feature (RAG) + HITL cho hoàn $  │
└─────────────────────────────────────────────────────────────┘
```

---

# 🗳️ Quyết định chọn bài toán cho Deep-Dive

Nhóm chọn **Card #1 — Vinhomes: Phân loại & soạn nháp phản hồi khiếu nại cư dân**.

**Lý do chọn:**
1. **Nút cổ chai đúng là ngôn ngữ tự nhiên.** Bước 2 và bước 5 là đọc–hiểu–viết, đúng vùng LLM mạnh; không có rule-based nào gán nhãn tốt cho văn bản tự do của cư dân kèm ảnh.
2. **Rủi ro có thể chặn được bằng thiết kế.** Đầu ra là *bản nháp*, luôn có nhân viên CSKH bấm gửi → AI sai thì người chặn được trước khi tới cư dân.
3. **Có baseline để đo.** App Resident đã log sẵn thời gian phản hồi và lịch sử route, nên đo "trước/sau" được bằng số thật chứ không phải cảm tính.

**Lý do loại 2 card còn lại:**
* **Card #2 (VinFast đối chiếu hóa đơn):** phần tốn thời gian nhất (so khớp số) là bài toán **deterministic** — dùng LLM ở đây vừa đắt vừa có nguy cơ sai số tiền. Đúng kỹ thuật thì nên viết rule/script đối chiếu trước, AI chỉ làm phần đọc file nhiều định dạng. Không đủ "chất AI" để làm deep-dive của lab.
* **Card #3 (Vinpearl chatbot):** hấp dẫn nhưng phụ thuộc vào việc bộ tài liệu chính sách đổi/hủy vé phải được chuẩn hoá và version hoá trước — đây là việc của Khối Pháp chế/Thương mại, nhóm kỹ thuật không kiểm soát được tiến độ. Để sau khi có corpus chính sách sạch.
