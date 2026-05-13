---
artifact: 3 — Lớp kiến trúc dữ liệu
bai-tap: 2 — Thiết kế giải pháp
demo: ./demo.md
---

# card.md — Lớp kiến trúc dữ liệu

**Tình huống xử lý**: T-03 (chính) + T-07, T-09 (bổ trợ cho RAG)  
Xem `../../1-map-and-format.md` Phần A.

---

## 1. Rủi ro xử lý

- **ID tình huống**: T-03
- **Mẫu lỗi**: SkyBot không có bộ phát hiện intent bức xúc ở tầng kiến trúc — LLM xử lý mọi tin nhắn theo cùng một pipeline, không phân loại trước để biết "tin nhắn này cần chuyển người thật, không cần AI xử lý"
- **Hậu quả**: Mọi tin nhắn đều đi vào LLM → LLM luôn có xu hướng tạo ra câu trả lời → không dừng lại để trigger handoff dù người dùng đang bức xúc

---

## 2. Giải pháp là gì?

Thêm **Intent Classifier** chạy song song trước LLM, phân loại mỗi tin nhắn thành 3 nhãn:

- `NORMAL` → LLM xử lý bình thường
- `FRUSTRATED` → LLM xử lý nhưng giao diện kích hoạt banner cảnh báo (Lớp 1)
- `ESCALATE` → Không cho LLM xử lý, chuyển thẳng sang Human Agent Queue

Kết hợp với **Logging Pipeline** ghi lại mọi case FRUSTRATED và ESCALATE để phân tích pattern và cải thiện classifier sau ra mắt.

---

## 3. Vì sao sửa ở lớp kiến trúc dữ liệu?

- Prompt rule (Lớp 2) là lớp dự phòng tốt nhưng không đủ: LLM vẫn "đọc" toàn bộ tin nhắn và có thể bỏ sót tín hiệu bức xúc khi diễn đạt không điển hình
- Cần kiểm tra intent TRƯỚC khi câu trả lời được tạo ra — đây là điểm khác biệt của lớp kiến trúc so với lớp prompt
- Cần ghi lại lỗi để nhóm biết lỗi nào lặp lại nhiều sau ra mắt — prompt rule không làm được việc này

**Hành động phòng vệ chính**:

- [x] Ngăn lỗi — classifier phân loại ESCALATE trước khi LLM vào loop
- [x] Phát hiện — log pattern FRUSTRATED + ESCALATE, alert khi tỷ lệ bất thường
- [x] Khắc phục — Human Agent Queue tiếp nhận case ngay khi classifier phân loại ESCALATE
- [ ] Ghi lại lỗi để cải thiện sau

---

## 4. Tầng giải pháp

- Lớp này sửa trực tiếp nguyên nhân gốc: "thiếu bộ phát hiện intent bức xúc ở tầng kiến trúc"
- Phù hợp vì đây là lớp phòng thủ đầu tiên — ngăn vấn đề trước khi LLM có cơ hội tạo ra câu trả lời sai
- Lớp này cần phối hợp với Lớp 1 (nhận tín hiệu FRUSTRATED để hiển thị banner) và Lớp 2 (làm lớp dự phòng khi classifier bị miss)

---

## 5. Bản demo

- **File demo**: [`demo.md`](./demo.md)
- **Định dạng demo**: Sơ đồ Mermaid kiến trúc luồng xử lý + bảng routing + sơ đồ logging
- **Người phản biện cần nhìn thấy**: Classifier ở vị trí nào trong pipeline, ESCALATE đi đến đâu, FRUSTRATED kích hoạt gì trên UI, log được lưu như thế nào
- **Thành phần chính**:
  - Main Pipeline: User → Intent Classifier → Router → LLM / Human Queue
  - Human Queue: agent dashboard, notification, conversation context
  - Logging Pipeline: FRUSTRATED + ESCALATE → analytics → alert
  - Data Sources: RAG policy DB cho T-07, T-09

---

## 6. Tác dụng phụ và cách giảm

- **Tác dụng phụ 1**: Classifier thêm latency ~50-100ms mỗi tin nhắn → trải nghiệm chậm hơn → **Cách giảm**: classifier dùng model nhỏ, nhẹ (không cần LLM lớn) chỉ làm binary/multi-class classification; cache kết quả nếu tin nhắn quá giống tin trước
- **Tác dụng phụ 2**: Classifier có false positive → case NORMAL bị phân loại ESCALATE → tăng tải nhân viên → **Cách giảm**: theo dõi precision/recall của classifier; điều chỉnh ngưỡng dựa trên dữ liệu thực tế sau ra mắt
- **Tác dụng phụ 3**: Human Agent Queue nếu thiếu nhân viên → case ESCALATE bị chờ lâu → **Cách giảm**: tích hợp thông báo thời gian chờ ước tính vào giao diện (Lớp 1); có fallback về hotline nếu queue đầy
- **Tác dụng phụ 4**: Logging pipeline lưu nội dung hội thoại → vấn đề bảo mật/GDPR → **Cách giảm**: anonymize trước khi lưu, chỉ giữ metadata + nhãn intent, không giữ nội dung đầy đủ quá 30 ngày

---

## 7. Hành động phòng vệ

- [x] Ngăn — classifier phân loại ESCALATE, không cho LLM vào loop
- [x] Phát hiện — logging + alert khi tỷ lệ ESCALATE tăng bất thường (>threshold)
- [x] Khắc phục — Human Agent Queue tiếp nhận và xử lý
- [ ] Thông báo

---

## 8. Checklist trước khi nộp

- [x] Sơ đồ cho thấy dữ liệu đi từ đâu đến đâu (classifier → router → LLM / Human Queue).
- [x] Có bước kiểm tra intent trước khi AI trả lời (classifier là bước đó).
- [x] Có cách xử lý khi phân loại ESCALATE (Human Agent Queue).
- [x] Có cách chuyển sang người thật với tình huống rủi ro cao (Queue + dashboard).
- [x] Có cách biết lỗi này có đang lặp lại không (Logging Pipeline + alert).

**Người phụ trách**: Quách Gia Được (2A202600423)
