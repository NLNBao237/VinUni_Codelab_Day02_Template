# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

[](https://github.com/long2110d/VinUni_Codelab_Day02_Template/blob/main/01-worksheet.md#-phase-1--scan-c%C3%A1-nh%C3%A2n-20-min)

Hãy sử dụng **4 Lenses** dưới đây để quét qua hoạt động vận hành của các công ty thành viên Vingroup. Ghi lại **ít nhất 5 bài toán/bottleneck** thực tế.

### 4 Lenses tìm bài toán AI cho Vingroup:

[](https://github.com/long2110d/VinUni_Codelab_Day02_Template/blob/main/01-worksheet.md#4-lenses-t%C3%ACm-b%C3%A0i-to%C3%A1n-ai-cho-vingroup)

1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày. (Ví dụ: So khớp hóa đơn sạc điện tại VinFast, route lại chuyến taxi tại Xanh SM).
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên. (Ví dụ: Soạn thảo phản hồi đánh giá 1-star của cư dân Vinhomes).
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng hiện tại còn chậm hoặc phản hồi rập khuôn. (Ví dụ: Chatbot CSKH Vinpearl hỗ trợ đặt vé vui chơi).
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng hoặc nhân viên thực địa phàn nàn. (Ví dụ: Tài xế Xanh SM phàn nàn về việc hệ thống gợi ý điểm đón khách không chính xác).
5. ### 📝 List bài toán của tôi:

List bài toán của tôi 

| #   | Subsidiary (VinFast/Xanh SM...)                                    | Lens                                                                                                     | Mô tả ngắn bài toán                                                                                                                                                                   |     |
| --- | ------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
| 1   | Đặt lịch khám & nhắc lịch hẹn                                      | **Lặp lại (Repetitive)**                                                                                 | tổng đài viên gọi xác nhận thủ công, không tích hợp real-time với hệ thống EMR, bệnh nhân tự hủy/không đến mà không báo trước.                                                        |     |
| 2   | Thông tin bệnh nhân nằm rải rác ở nhiều hệ thống                   | **AI có thể tốt hơn (AI-upgrade)**                                                                       | Thông tin cá nhân bệnh án của bệnh nhân ở nhiều nơi khám nhiều chỗ gây khó khăn cho bác sỹ xác định các bệnh tiền sử khó chuẩn đoán và họ cũng không nhớ được, Đề xuất xây 1 hồ sơ ag |     |
| 3   |                                                                    |                                                                                                          |                                                                                                                                                                                       |     |
| 4   | Xử lý yêu cầu bảo hiểm (BHYT & bảo hiểm tư nhân/claims processing) | **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên.                  | Xử lý bảo hiểm nhân viên phải tự đối chiếu các thông tin từ ngày tháng gây mất thời gian có thể xây OCR tích hợp NLP để so khớp                                                       |     |
| 5   | Quản lý tồn kho dược phẩm & vật tư y tế                            | **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng hoặc nhân viên thực địa phàn nàn. | kiểm kê thủ công theo chu kỳ, dự báo nhu cầu dựa trên kinh nghiệm thay vì dữ liệu lịch sử.                                                                                            |     |

# 🎯 Phase 2 — Quick Problem Cards (Vinmec)

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                        │
│                                                               │
│ Bài toán (1 câu): Hồ sơ/tiền sử bệnh của bệnh nhân nằm rải   │
│ rác ở nhiều hệ thống/cơ sở khám khác nhau, khiến bác sĩ khó  │
│ nắm đầy đủ thông tin để chẩn đoán.                            │
│                                                               │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes   │
│                     [x] Vinmec   [ ] Khác ____________        │
│                                                               │
│ Ai đang đau (Actor)? Bác sĩ khám bệnh (và bệnh nhân khám ở    │
│ nhiều cơ sở/nhiều lần)                                        │
│                                                               │
│ Workflow thủ công hiện tại (3-5 bước):                        │
│   1. Bệnh nhân đến khám tại cơ sở mới                         │
│   ──> 2. Bác sĩ hỏi trực tiếp về tiền sử bệnh, thuốc đang dùng│
│   ──> 3. Bệnh nhân quên/không nhớ chính xác lịch sử khám      │
│   ──> 4. Bác sĩ/điều dưỡng tra cứu thủ công qua nhiều hệ      │
│         thống nội bộ hoặc gọi điện sang cơ sở khám trước      │
│   ──> 5. Ra quyết định chẩn đoán/kê đơn dựa trên thông tin    │
│         chưa đầy đủ                                           │
│                                                               │
│ Bước nào tốn thời gian/lỗi nhất? Bước 4 (⏱ ước tính 10-15    │
│ phút/lượt tra cứu, chưa kể rủi ro bỏ sót thông tin)           │
│                                                               │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-4: dùng NLP để   │
│ tự động tổng hợp & tóm tắt hồ sơ bệnh án từ nhiều nguồn thành │
│ 1 bản tóm tắt tiền sử duy nhất (unified patient profile),     │
│ cảnh báo tương tác thuốc/bệnh nền cho bác sĩ trước khi khám   │
│                                                               │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian tra cứu │
│ hồ sơ từ 10-15 phút ──> dưới 2 phút; tăng tỷ lệ hồ sơ đầy đủ  │
│ tiền sử khi khám từ ~60% ──> trên 90%                         │
│                                                               │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent  │
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                         │
│                                                               │
│ Bài toán (1 câu): Nhân viên xử lý yêu cầu bảo hiểm (BHYT &   │
│ bảo hiểm tư nhân) phải đối chiếu thủ công nhiều loại giấy tờ, │
│ gây mất thời gian và dễ sai sót.                              │
│                                                               │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes   │
│                     [x] Vinmec   [ ] Khác ____________        │
│                                                               │
│ Ai đang đau (Actor)? Nhân viên phòng bảo hiểm/thu ngân (claims│
│ staff) và bệnh nhân chờ duyệt hồ sơ                           │
│                                                               │
│ Workflow thủ công hiện tại (3-5 bước):                        │
│   1. Nhận hồ sơ yêu cầu bảo hiểm (giấy tờ, hóa đơn, kết quả   │
│      khám)                                                    │
│   ──> 2. Nhân viên đối chiếu thủ công thông tin ngày tháng,   │
│         mã bệnh, chi phí giữa các loại giấy tờ                │
│   ──> 3. Kiểm tra tính hợp lệ theo quy định của từng loại     │
│         bảo hiểm                                              │
│   ──> 4. Nhập liệu thủ công vào hệ thống quản lý bảo hiểm     │
│   ──> 5. Phê duyệt hoặc trả lại yêu cầu bổ sung               │
│                                                               │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 (⏱ ước tính 15-20    │
│ phút/hồ sơ, dễ sai lệch số liệu do nhập tay)                  │
│                                                               │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1-2: dùng OCR để   │
│ số hóa giấy tờ, kết hợp NLP để tự động trích xuất & so khớp   │
│ thông tin giữa các nguồn, gắn cờ (flag) các hồ sơ có sai lệch │
│ để nhân viên chỉ cần kiểm tra lại                             │
│                                                               │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian xử lý 1 │
│ hồ sơ từ 15-20 phút ──> dưới 5 phút; giảm tỷ lệ lỗi đối chiếu │
│ thủ công từ ~10% ──> dưới 2%                                  │
│                                                               │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent  │
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                         │
│                                                               │
│ Bài toán (1 câu): Việc đặt lịch khám & nhắc lịch hẹn hiện do  │
│ tổng đài viên gọi xác nhận thủ công, không tích hợp real-time │
│ với EMR, dẫn đến bệnh nhân tự hủy/không đến mà không báo.     │
│                                                               │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes   │
│                     [x] Vinmec   [ ] Khác ____________        │
│                                                               │
│ Ai đang đau (Actor)? Tổng đài viên đặt lịch và bác sĩ/phòng   │
│ khám bị trống lịch do bệnh nhân không đến                     │
│                                                               │
│ Workflow thủ công hiện tại (3-5 bước):                        │
│   1. Bệnh nhân gọi điện/nhắn tin đặt lịch khám                │
│   ──> 2. Tổng đài viên kiểm tra lịch trống thủ công và ghi    │
│         nhận vào hệ thống                                     │
│   ──> 3. Tổng đài viên gọi điện xác nhận lại trước ngày hẹn   │
│   ──> 4. Không có cơ chế nhắc tự động sát giờ hẹn             │
│   ──> 5. Bệnh nhân tự hủy/không đến mà không báo trước, để    │
│         trống slot khám                                       │
│                                                               │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2-3 (⏱ ước tính 5-7    │
│ phút/cuộc gọi xác nhận, nhân với hàng trăm lượt/ngày)         │
│                                                               │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-4: chatbot/      │
│ voicebot tự động xác nhận lịch hẹn, tích hợp real-time với    │
│ EMR để tránh trùng lịch, tự động nhắc hẹn qua tin nhắn/gọi    │
│ thoại trước 24h và cho phép đổi/hủy lịch ngay trên hệ thống   │
│                                                               │
│ Đo thành công bằng gì (Metric có số)? Giảm tỷ lệ no-show từ   │
│ ~20% ──> dưới 8%; giảm thời gian tổng đài viên xử lý đặt lịch │
│ từ 5-7 phút ──> dưới 1 phút/cuộc gọi                          │
│                                                               │
│ Quick Architecture: [ ] No AI  [ ] Rule  [ ] LLM  [x] Agent  │
└─────────────────────────────────────────────────────────────┘
```