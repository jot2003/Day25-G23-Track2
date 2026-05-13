---
artifact: 1 — FINAL kế hoạch giải pháp
bai-tap: 2 — Thiết kế giải pháp
phase: Chọn rủi ro + chọn tầng + chọn demo + chốt 3 lớp giải pháp
time: 11:00-11:55
input: 00-context.md + 01-test-set-review/3-FINAL-test-set-eval-plan.md
nop-cuoi: Có — file cuối Bài 2
---

# 1 — FINAL: Kế hoạch giải pháp

File này ghi lại quyết định chính của Bài 2:

- Rủi ro nào được chọn.
- Vì sao rủi ro đó quan trọng.
- Nguyên nhân gốc là gì.
- Nhóm sẽ xây 3 lớp giải pháp nào.
- Mỗi lớp dùng demo gì.

Lý do cần 3 lớp: một giải pháp đơn lẻ dễ lọt lỗi. Với rủi ro nặng, nhóm cần nhiều lớp cùng đỡ: lớp này ngăn, lớp kia phát hiện, lớp khác khắc phục hoặc thông báo cho người dùng.

Ba lớp giải pháp nằm trong thư mục `artifact/`:

| Lớp | Thư mục | Vai trò |
|---|---|---|
| Giao diện | `artifact/1-uiux/` | Cảnh báo, dẫn nguồn, nút chuyển sang người thật |
| Chỉ dẫn AI | `artifact/2-prompt/` | Hỏi lại, từ chối, bắt buộc dẫn nguồn |
| Kiến trúc dữ liệu | `artifact/3-architecture/` | Tra cứu nguồn đúng, lưu tạm dữ liệu, xử lý khi thiếu nguồn, giám sát |

Ba lớp này bổ sung cho nhau. Nếu một lớp lọt lỗi, lớp khác vẫn có thể chặn hoặc giảm hại.

## Thông tin nhóm

- **Chủ đề**: Track 02 — SkyBot: Trợ lý đặt vé và CSKH hàng không nội địa Việt Nam
- **Thành viên**: 2A202600372 Hoàng Kim Trí Thành | 2A202600025 Nguyễn Thành Nam | 2A202600423 Quách Gia Được
- **Ngày**: 2026-05-13

---

## Phần A — Chọn rủi ro và tầng giải pháp

### Rủi ro chính được chọn

- **ID tình huống**: T-03
- **Mô tả ngắn**: Khi khách bức xúc leo thang qua nhiều tin nhắn về hoãn chuyến liên tiếp, SkyBot có xu hướng tiếp tục trả lời chính sách theo vòng lặp thay vì nhận diện trạng thái cảm xúc và chuyển sang nhân viên thật — gây leo thang khủng hoảng tình cảm và có thể trở thành khủng hoảng truyền thông cho hãng
- **Mức độ**: Nặng
- **Điểm rủi ro**: 20/25
- **Vì sao chọn tình huống này**: T-03 đại diện cho failure mode phổ biến nhất trong hàng không (vụ American Airlines 2023, vụ Air Canada 2024) — khi AI được thiết kế để "giải quyết vấn đề" nhưng thiếu khả năng nhận biết "tôi không nên là người giải quyết vấn đề này". Đây cũng là rủi ro bao phủ được 3 loại escalation nhóm đã xác định: bức xúc cảm xúc, quyền lợi pháp lý, và tình trạng sức khỏe

### Tìm nguyên nhân gốc

Đừng chỉ mô tả lỗi. Hãy trả lời: vì sao lỗi xảy ra?

- [ ] Thiếu nguồn dữ liệu đúng.
- [ ] AI đoán khi không biết.
- [x] Giao diện khiến người dùng tin quá mức — SkyBot được đặt trong app chính thức của hãng, giao diện không có tín hiệu nào cho thấy đây chỉ là bot và có giới hạn
- [x] Quy trình thiếu người duyệt hoặc thiếu bước chuyển sang người thật — không có trigger tự động nào để chuyển agent khi phát hiện bức xúc
- [x] Khác: SkyBot thiếu bộ phát hiện intent cảm xúc (frustration/anger detection) — model LLM chỉ xử lý theo nội dung text, không có classifier riêng cho trạng thái cảm xúc leo thang

### Bảng nối nguyên nhân với tầng sửa

| Nguyên nhân gốc | Tầng ưu tiên sửa | Lớp giải pháp liên quan |
|---|---|---|
| Thiếu bộ phát hiện intent bức xúc | Kiến trúc — classifier ở tầng trước LLM | `3-architecture` là chính |
| Không có trigger chuyển agent tự động | Chỉ dẫn AI — rule bắt buộc khi phát hiện tín hiệu bức xúc | `2-prompt` là chính |
| Giao diện không cho thấy giới hạn bot | Giao diện — disclaimer, trạng thái bot, nút chuyển người thật luôn hiển thị | `1-uiux` là chính |
| Lỗi lặp lại sau khi ra mắt | Theo dõi — log intent bức xúc, alert khi tỷ lệ leo thang tăng | `3-architecture` hỗ trợ |

Nguyên tắc: lỗi ở tầng nào, ưu tiên sửa ở tầng đó. Đừng chỉ thêm cảnh báo giao diện nếu nguyên nhân gốc là thiếu bộ phát hiện intent.

### 10 tầng giải pháp tham khảo

Không bắt buộc dùng đủ 10 tầng. Bảng này giúp nhóm chọn đúng hướng sửa.

| Tầng | Khi nào dùng |
|---|---|
| **Giao diện** ✅ | Người dùng tin AI quá mức, thiếu cảnh báo, thiếu nút chuyển sang người thật |
| **Chỉ dẫn AI** ✅ | AI không nhận ra tín hiệu bức xúc, không từ chối tiếp tục loop |
| Quy trình xử lý | Cần phân loại ý định, chuyển đúng nơi xử lý |
| **Dữ liệu / tra cứu nguồn (RAG)** | Thiếu nguồn đúng — áp dụng cho T-07, T-09 |
| **Theo dõi** ✅ | Lỗi lặp lại sau khi ra mắt nhưng không ai thấy |
| Chính sách / thông báo giới hạn | Người dùng không biết giới hạn của AI |
| Người duyệt / phê duyệt | Tình huống pháp lý, y tế |
| Vai trò trách nhiệm | Có cảnh báo nhưng không ai chịu trách nhiệm xử lý |
| Vòng phản hồi | Cần người dùng / người rà báo lỗi để cập nhật hệ thống |
| **Kiến trúc lai** ✅ | LLM một mình không đủ, cần classifier riêng cho intent bức xúc |

### 4 hành động phòng vệ

Mỗi lớp nên làm ít nhất một việc:

- **Ngăn**: classifier phát hiện bức xúc ở tầng trước LLM → không để LLM loop
- **Phát hiện**: rule trong system prompt nhận diện tín hiệu bức xúc trong nội dung tin nhắn
- **Khắc phục**: trigger chuyển nhân viên tự động khi phát hiện bức xúc leo thang
- **Thông báo**: giao diện hiển thị nút "Gặp nhân viên" nổi bật, disclaimer giới hạn bot

Gợi ý theo mức rủi ro:

| Mức rủi ro | Nên có |
|---|---|
| Nhẹ | Ít nhất 1 hành động |
| Vừa | Ít nhất 2 hành động |
| **Nặng** | Ít nhất 3 hành động |
| Rất nặng / không đảo ngược được | Cố gắng đủ 4 hành động + có người chịu trách nhiệm |

T-03 mức Nặng → nhóm thiết kế đủ 4 hành động.

### Kết luận Phần A

**Nguyên nhân gốc**: SkyBot thiếu cơ chế phát hiện intent bức xúc leo thang + thiếu trigger tự động chuyển sang nhân viên thật + giao diện không thể hiện giới hạn của bot

**Tầng chính cần sửa**: Kiến trúc (classifier) + Chỉ dẫn AI (rule nhận diện + từ chối loop) + Giao diện (nút chuyển nhân viên luôn hiển thị)

**Vì sao cần 3 lớp giải pháp**:

- Lớp giao diện: ngay cả khi classifier và prompt hoạt động đúng, người dùng vẫn cần thấy rõ lựa chọn gặp nhân viên là dễ dàng — không cần chờ AI chuyển, tự bấm được ngay
- Lớp chỉ dẫn AI: prompt cần có rule phát hiện các từ ngữ bức xúc rõ ràng và tín hiệu leo thang — đây là lớp phòng thủ khi classifier bị miss
- Lớp kiến trúc dữ liệu: classifier chạy trước LLM là lớp phòng thủ đầu tiên — ngăn LLM vào loop ngay từ đầu; đồng thời log để theo dõi pattern sau ra mắt

---

## Phần B — Chọn định dạng demo

Mỗi lớp cần một bản demo. Demo giúp biến ý tưởng thành thứ trực quan để nhóm khác xem, kiểm tra và phản biện.

| Lớp | Thư mục | Định dạng demo chọn | Thời gian dự kiến |
|---|---|---|---|
| Giao diện | `1-uiux` | ASCII sketch màn hình chat SkyBot + Mermaid UI flow | 15 phút |
| Chỉ dẫn AI | `2-prompt` | Bản system prompt đầy đủ trong Markdown + ví dụ hội thoại mẫu | 15 phút |
| Kiến trúc dữ liệu | `3-architecture` | Sơ đồ Mermaid kiến trúc intent classifier + human queue | 10 phút |

**Lý do chọn demo**

- Giao diện: ASCII sketch giúp minh họa nhanh bố cục màn hình chat, nút "Gặp nhân viên", và banner cảnh báo mà không cần công cụ thiết kế
- Chỉ dẫn AI: system prompt trong Markdown là định dạng thực tế nhất — nhóm khác có thể test trực tiếp với AI
- Kiến trúc dữ liệu: Mermaid diagram trực quan luồng xử lý, dễ phản biện từng bước

### Chọn demo theo điều cần chứng minh

| Nếu cần chứng minh... | Demo phù hợp |
|---|---|
| Người dùng nhìn thấy gì | ASCII UI + Mermaid flow ✅ |
| AI được chỉ dẫn thế nào | Bản system prompt Markdown + ví dụ hội thoại ✅ |
| Dữ liệu đi qua đâu | Mermaid kiến trúc ✅ |
| Quy trình chuyển sang người thật | Bao phủ cả 3 lớp ✅ |

---

## Phần C — Ba lớp giải pháp

Ghi tóm tắt ở đây. Chi tiết nằm trong `card.md` và `demo.*` của từng thư mục.

### Lớp 1 — Giao diện (`artifact/1-uiux/`)

- **Cách tiếp cận**: Hiển thị nút "Gặp nhân viên" luôn nổi bật trên màn hình chat; khi SkyBot phát hiện tín hiệu bức xúc → tự động hiển thị banner "Có vẻ bạn cần hỗ trợ trực tiếp — nhấn đây để gặp nhân viên"; thêm disclaimer nhỏ ở footer chat "SkyBot cung cấp thông tin chính sách — mọi giao dịch thực tế cần nhân viên xác nhận"
- **Hành động phòng vệ bao phủ**: Thông báo (disclaimer, nút chuyển luôn hiển thị) + Khắc phục (banner tự động khi phát hiện bức xúc)
- **Demo**: ASCII sketch màn hình chat + Mermaid UI flow
- **Trạng thái**: Xong

Link chi tiết:

- `artifact/1-uiux/card.md`
- `artifact/1-uiux/demo.md`

### Lớp 2 — Chỉ dẫn AI (`artifact/2-prompt/`)

- **Cách tiếp cận**: System prompt bao gồm: (1) rule nhận diện 5 dấu hiệu bức xúc rõ (từ ngữ tiêu cực, chuỗi tin nhắn ngắn, yêu cầu người thật, dấu chấm than lặp, lặp lại vấn đề); (2) rule bắt buộc DỪNG loop và trigger handoff khi nhận đủ 2 dấu hiệu; (3) rule từ chối cam kết ngoài thẩm quyền; (4) ví dụ hội thoại mẫu đúng và sai
- **Hành động phòng vệ bao phủ**: Phát hiện (nhận diện tín hiệu bức xúc trong text) + Khắc phục (trigger handoff bắt buộc)
- **Demo**: System prompt hoàn chỉnh + 3 ví dụ hội thoại (đúng / sai / biên)
- **Trạng thái**: Xong

Link chi tiết:

- `artifact/2-prompt/card.md`
- `artifact/2-prompt/demo.md`

### Lớp 3 — Kiến trúc dữ liệu (`artifact/3-architecture/`)

- **Cách tiếp cận**: Thêm Intent Classifier chạy song song với LLM — phân loại intent thành: NORMAL (LLM xử lý), FRUSTRATED (LLM + cảnh báo giao diện), ESCALATE (chuyển Human Queue ngay, LLM không xử lý); Human Queue có agent dashboard; log tất cả FRUSTRATED + ESCALATE để phân tích pattern
- **Hành động phòng vệ bao phủ**: Ngăn (classifier chặn LLM loop trước khi bắt đầu) + Phát hiện (log pattern, alert khi tỷ lệ ESCALATE tăng bất thường)
- **Demo**: Sơ đồ Mermaid kiến trúc luồng xử lý từ user đến agent
- **Trạng thái**: Xong

Link chi tiết:

- `artifact/3-architecture/card.md`
- `artifact/3-architecture/demo.md`

---

## Tổng kiểm tra

| Câu hỏi | Trả lời |
|---|---|
| Rủi ro chính đã chọn là gì? | T-03 — AI loop khi khách bức xúc leo thang, không chuyển nhân viên |
| Nguyên nhân gốc là gì? | Thiếu intent frustration classifier + thiếu trigger handoff tự động + giao diện không thể hiện giới hạn bot |
| 3 lớp giải pháp đã đủ chưa? | Giao diện: Xong / Chỉ dẫn AI: Xong / Kiến trúc: Xong |
| 4 hành động đã bao phủ chưa? | Ngăn: Classifier (Lớp 3) / Phát hiện: Prompt rule + Log (Lớp 2, 3) / Khắc phục: Trigger handoff (Lớp 2, 3) + Banner (Lớp 1) / Thông báo: Disclaimer + Nút (Lớp 1) |
| Nhóm khác đã góp ý chưa? | Chưa — chờ peer review |
| Nhóm đã sửa gì sau phản biện? | Chưa — điền sau peer review |

## Phản biện chéo: 4 câu phải trả lời

| Góc phản biện | Câu hỏi | Trả lời của nhóm |
|---|---|---|
| Đúng tầng | Giải pháp có sửa đúng nguyên nhân gốc không? | Có — Lớp 3 (classifier) sửa trực tiếp nguyên nhân "thiếu bộ phát hiện intent"; Lớp 2 (prompt rule) làm lớp dự phòng; Lớp 1 (UX) giải quyết "giao diện không thể hiện giới hạn" |
| Cụ thể | Demo có đủ rõ để hiểu cách vận hành không? | Có — ASCII sketch cho thấy chính xác người dùng thấy gì; system prompt có thể test trực tiếp; Mermaid diagram cho thấy từng bước trong pipeline |
| Đủ lớp | 3 lớp có bổ sung cho nhau không, hay đang lặp cùng một ý? | Bổ sung: Lớp 3 ngăn trước khi LLM xử lý; Lớp 2 phát hiện trong quá trình LLM xử lý; Lớp 1 cho phép người dùng tự chủ động thoát bất cứ lúc nào |
| Tác dụng phụ | Giải pháp có làm chậm, tốn kém, rối giao diện, hoặc gây hiểu nhầm mới không? | Rủi ro: classifier thêm latency ~50-100ms; nút "Gặp nhân viên" luôn hiển thị có thể tăng load nhân viên. Giảm thiểu: cache kết quả classifier; hiển thị nút khéo léo không quá nổi bật ở trạng thái bình thường |

## Gợi ý chia việc

Nhóm 3 người:

- Hoàng Kim Trí Thành: `artifact/1-uiux/`
- Nguyễn Thành Nam: `artifact/2-prompt/`
- Quách Gia Được: `artifact/3-architecture/`

5 phút cuối: cả nhóm đọc chéo 3 lớp, sửa lại bảng tổng kiểm tra, rồi chuẩn bị phản biện chéo.
