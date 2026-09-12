# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

Hãy sử dụng **4 Lenses** dưới đây để quét qua hoạt động vận hành của các công ty thành viên Vingroup. Ghi lại **ít nhất 5 bài toán/bottleneck** thực tế.

### 4 Lenses tìm bài toán AI cho Vingroup:
1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày. (Ví dụ: So khớp hóa đơn sạc điện tại VinFast, route lại chuyến taxi tại Xanh SM).
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên. (Ví dụ: Soạn thảo phản hồi đánh giá 1-star của cư dân Vinhomes).
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng hiện tại còn chậm hoặc phản hồi rập khuôn. (Ví dụ: Chatbot CSKH Vinpearl hỗ trợ đặt vé vui chơi).
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng hoặc nhân viên thực địa phàn nàn. (Ví dụ: Tài xế Xanh SM phàn nàn về việc hệ thống gợi ý điểm đón khách không chính xác).

### 📝 List bài toán của tôi:
| # | Subsidiary | Tên bài toán / Bottleneck | Lens | Mô tả ngắn |
|---|------------|---------------------------|------|------------|
| 1 | **VinFast** | Trợ lý hướng dẫn trạm sạc thông minh | AI có thể tốt hơn | Khi pin dưới 20%, tài xế vừa lái vừa phải dò trạm sạc trên app; dễ đến nhầm trạm đã kín trụ, trạm đang bảo trì hoặc trụ không tương thích công suất dòng xe. |
| 2 | **VinFast** | Đối chiếu hóa đơn sạc điện đối tác | Lặp lại | Mỗi tuần chuyên viên tài chính phải đối chiếu thủ công hàng trăm nghìn log sạc từ các trụ liên kết ngoài (đối tác) với số liệu thanh toán trên cổng ERP. |
| 3 | **Xanh SM** | Điều phối khẩn cấp sự cố pin/va chạm thực địa | Pain từ người khác | Tài xế gặp sự cố xe cạn pin hoặc va quẹt giữa đường; điều phối viên mất 15–20 phút để tra GPS, check trạm cứu hộ pin lưu động và soạn SMS chỉ dẫn. |
| 4 | **Xanh SM** | Hỗ trợ điều phối chuyến liên tỉnh/thuê ngày | AI có thể tốt hơn | Khách hàng nhắn tin qua fanpage/tổng đài hỏi lộ trình và giá thuê xe Xanh SM theo ngày hoặc đi liên tỉnh phức tạp; nhân viên tư vấn mất nhiều thời gian tra bảng cước và lịch xe trống. |
| 5 | **Xanh SM** | Tối ưu điểm đón thông minh (Smart Pickup) | Tốn thời gian | Khách ghi chú vị trí đón phức tạp (ví dụ: "cổng sau toà S2.05 đối diện tiệm trà"); tài xế không tìm được dẫn đến gọi đi gọi lại, gây trễ giờ và tắc nghẽn sảnh đón. |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên và hoàn thiện **3 Quick Problem Cards** dưới đây (10 phút/card).

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Trợ lý giọng nói/màn hình xe tự động đề   │
│ xuất trạm sạc còn trụ trống tương thích và định tuyến lại khi│
│ pin xe VinFast báo dưới 20%.                                │
│ Công ty thành viên: [x] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Tài xế lái xe điện VinFast             │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Màn hình xe/cụm đồng hồ báo pin yếu (< 20% SoC)        │
│   ──> 2. Tài xế dừng xe hoặc vừa lái vừa mở app tìm trạm gần│
│   ──> 3. Lọc trạm thủ công xem còn trụ trống và đúng công suất│
│   ──> 4. Nhập địa chỉ trạm vào bản đồ dẫn đường             │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 6-8 phút/lần,│
│ dễ dẫn tới trạm kín chỗ hoặc trụ hỏng)                       │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3 & 4: Tự động│
│ tổng hợp dữ liệu GPS + SoC + API trạm sạc để sinh lời thoại │
│ gợi ý và tạo sẵn lộ trình tối ưu 1-click.                   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Rút ngắn thời gian thao tác chọn trạm từ 7 phút ──> dưới 20s;│
│ Tỉ lệ xe tìm được trụ sạc thành công trong 1 lần đạt > 95%. │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Hỗ trợ điều phối viên Xanh SM tra cứu trạm│
│ sạc trống gần nhất hoặc điều xe cứu hộ pin khẩn cấp khi tài  │
│ xế báo cạn pin/va chạm thực địa.                            │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Điều phối viên (Dispatcher) & Tài xế SM│
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Nhận cuộc gọi khẩn cấp từ tài xế báo cạn kiệt pin      │
│   ──> 2. Mở dashboard bản đồ tra cứu tọa độ GPS của xe      │
│   ──> 3. Tra cứu dashboard VinFast tìm trạm có trụ trống    │
│   ──> 4. Soạn tin nhắn hướng dẫn đường đi gửi app tài xế    │
│   ──> 5. Gọi đội xe cứu hộ pin lưu động nếu pin dưới 5%     │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 10 phút/lượt) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 & 4: Tự động   │
│ pull dữ liệu vị trí, kiểm tra tình trạng trạm sạc, soạn sẵn │
│ bản nháp SMS chỉ dẫn và cảnh báo gọi cứu hộ khi pin < 5%.   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 3 phút;      │
│ 100% trường hợp pin < 5% được kích hoạt quy trình cứu hộ.   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Chuẩn hóa ghi chú đón khách phức tạp thành│
│ điểm đón chính xác kèm chỉ dẫn trực quan cho tài xế Xanh SM.│
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Tài xế Xanh SM & Khách hàng đặt xe     │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Khách nhập ghi chú vị trí đón bằng tiếng Việt tự do    │
│   ──> 2. Tài xế đến gần ghim GPS nhưng không thấy khách     │
│   ──> 3. Tài xế gọi điện cho khách để hỏi lại mốc nhận diện │
│   ──> 4. Tài xế vừa lái vừa tìm đường vòng đến điểm hẹn mới │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 4-6 phút/cuốc,│
│ gây tắc nghẽn sảnh chung cư/TTTM)                            │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1 & 2: Phân tích │
│ ngôn ngữ tự nhiên từ ghi chú, đối chiếu POI nội bộ để chuẩn │
│ hóa thành tọa độ cửa/sảnh cụ thể và sinh tóm tắt 1 dòng.    │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian chờ đón khách từ 6 phút ──> dưới 2 phút;     │
│ Giảm tỉ lệ tài xế phải gọi điện hỏi lại điểm đón xuống 60%. │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```