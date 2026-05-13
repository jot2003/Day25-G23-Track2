---
artifact: 2 — Lớp chỉ dẫn AI
bai-tap: 2 — Thiết kế giải pháp
demo: ./demo.md
---

# card.md — Lớp chỉ dẫn AI

**Tình huống xử lý**: T-03 (chính) + T-02, T-08 (bổ trợ)  
Xem `../../1-map-and-format.md` Phần A.

---

## 1. Rủi ro xử lý

- **ID tình huống**: T-03
- **Mẫu lỗi**: SkyBot tiếp tục trả lời chính sách theo vòng lặp khi khách bức xúc leo thang, không nhận diện được tín hiệu cần chuyển nhân viên trong nội dung tin nhắn
- **Hậu quả**: Khách không được xử lý đúng loại tình huống → bức xúc tăng thêm vì cảm giác "nói chuyện với tường" → leo thang khủng hoảng

---

## 2. Giải pháp là gì?

System prompt SkyBot được bổ sung 3 bộ rule:

1. **Rule phát hiện bức xúc** — 5 tín hiệu trong nội dung tin nhắn, khi nhận ≥2 tín hiệu → bắt buộc dừng loop và trigger handoff
2. **Rule từ chối cam kết ngoài thẩm quyền** — AI không được dùng từ "chắc chắn", "đảm bảo", "xác nhận" với thông tin tài chính/pháp lý chưa qua hệ thống
3. **Rule không chiều theo áp lực giảm chính xác** — khi người dùng yêu cầu AI bỏ qua độ chính xác, AI phải giải thích lý do và không chiều

---

## 3. Vì sao sửa ở lớp chỉ dẫn AI?

- AI cần luật rõ: khi nào trả lời thông tin, khi nào dừng và chuyển người thật — không thể chỉ dựa vào classifier (Lớp 3) vì classifier có thể bị miss ở một số cách diễn đạt bức xúc không điển hình
- Lớp chỉ dẫn AI là lớp dự phòng thứ hai: classifier không bắt được → prompt rule bắt lại
- Có thể cập nhật nhanh khi phát hiện pattern mới qua log — không cần thay đổi kiến trúc hệ thống

**Hành động phòng vệ chính**:

- [x] Ngăn câu trả lời sai ngay từ đầu (rule từ chối cam kết ngoài thẩm quyền)
- [ ] Bắt buộc nêu nguồn khi nói về thông tin quan trọng
- [ ] Từ chối trả lời khi thiếu căn cứ
- [x] Chuyển người thật khi vượt phạm vi (rule phát hiện bức xúc → handoff)

---

## 4. Tầng giải pháp

- Lớp này sửa phần "AI không có rule nào để biết mình cần dừng và nhường người thật"
- Phù hợp vì: ngay cả LLM thông minh cũng cần hướng dẫn rõ ràng về ranh giới — nếu không có rule, mô hình mặc định sẽ tiếp tục "giải quyết vấn đề" theo cách được huấn luyện
- Lớp này cần phối hợp với Lớp 1 (UI banner khi AI trigger handoff) và Lớp 3 (classifier cung cấp tín hiệu bức xúc đã được xử lý trước)

---

## 5. Bản demo

- **File demo**: [`demo.md`](./demo.md)
- **Định dạng demo**: System prompt đầy đủ trong Markdown + 3 ví dụ hội thoại mẫu (đúng / sai / biên)
- **Người phản biện cần nhìn thấy**: Rule phát hiện bức xúc hoạt động thế nào, trigger handoff được viết ra sao, ví dụ AI phản hồi đúng vs. sai
- **Thành phần chính**:
  - IDENTITY & SCOPE — định nghĩa SkyBot là gì và không phải là gì
  - FRUSTRATION DETECTION RULES — 5 tín hiệu + ngưỡng kích hoạt
  - HANDOFF PROTOCOL — kịch bản dừng và chuyển
  - BOUNDARY RULES — từ chối cam kết + không chiều áp lực
  - CONVERSATION EXAMPLES — 3 kịch bản hội thoại test

---

## 6. Tác dụng phụ và cách giảm

- **Tác dụng phụ 1**: Rule phát hiện bức xúc có thể quá nhạy → false positive → chuyển nhân viên khi không cần thiết → tăng tải call center → **Cách giảm**: ngưỡng ≥2 tín hiệu, không phải 1; thêm bước SkyBot hỏi xác nhận "Bạn có muốn tôi kết nối nhân viên ngay không?" trước khi trigger hoàn toàn
- **Tác dụng phụ 2**: Câu trả lời handoff có thể nghe cứng nhắc nếu viết không khéo → khách cảm thấy bị đẩy đi → **Cách giảm**: mẫu câu handoff phải bắt đầu bằng xác nhận cảm xúc, sau đó mới đề nghị chuyển
- **Tác dụng phụ 3**: Rule từ chối cam kết có thể khiến AI nghe thiếu quyết đoán với thông tin đơn giản → **Cách giảm**: chỉ áp rule này với thông tin tài chính, pháp lý, y tế — không áp cho thông tin hành lý thông thường

---

## 7. Hành động phòng vệ

- [x] Ngăn — rule từ chối cam kết ngoài thẩm quyền từ đầu
- [x] Phát hiện — rule nhận diện 5 tín hiệu bức xúc trong nội dung
- [x] Khắc phục — trigger handoff bắt buộc khi đủ ngưỡng
- [ ] Thông báo

---

## 8. Checklist trước khi nộp

- [x] Rule viết đủ cụ thể để AI làm theo.
- [x] Có mẫu câu handoff khi AI không nên tiếp tục.
- [x] Có ví dụ cho tình huống dễ sai (leo thang bức xúc, ép bỏ độ chính xác).
- [x] Có thử lại bằng tình huống T-03, T-02, T-08 từ Bài 1.
- [x] Không dùng prompt như cách duy nhất — phối hợp với Lớp 1 và Lớp 3.

**Người phụ trách**: Nguyễn Thành Nam (2A202600025)
