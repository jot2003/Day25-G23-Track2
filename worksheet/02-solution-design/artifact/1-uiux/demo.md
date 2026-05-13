---
artifact: 1 — Demo giao diện
format: ASCII sketch + bảng trạng thái
---

# demo.md — Demo giao diện SkyBot

Minh họa 3 trạng thái màn hình chat SkyBot liên quan đến escalation failure (T-03).

---

## 1. Trạng thái Bình thường

Khách hỏi thông tin thông thường — nút "Gặp nhân viên" hiển thị nhỏ, không nổi bật.

```text
┌─────────────────────────────────────────────────────────┐
│  ✈ SkyBot — Trợ lý hàng không                [👤 Nhân viên] │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  [SkyBot 🤖]                                            │
│  ┌─────────────────────────────────────────────────┐   │
│  │ Xin chào! Tôi có thể giúp gì cho bạn hôm nay?  │   │
│  │ • Đặt vé / kiểm tra lịch bay                   │   │
│  │ • Chính sách hành lý, đổi vé, hoàn tiền        │   │
│  │ • Quyền lợi khi chuyến bay thay đổi            │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  [Bạn]                                                  │
│  ┌─────────────────────────────────────────────────┐   │
│  │ Vé Economy Light có đổi được trước 24h không?   │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  [SkyBot 🤖]                                            │
│  ┌─────────────────────────────────────────────────┐   │
│  │ Hạng vé Economy Light không cho phép đổi lịch  │   │
│  │ bay. Nếu cần đổi, bạn sẽ cần mua vé mới.       │   │
│  │                                                 │   │
│  │ 📋 Nguồn: skyairline.vn/chinh-sach-ve           │   │
│  └─────────────────────────────────────────────────┘   │
│  ⚠ Thông tin chính sách — Giao dịch thực tế cần        │
│    nhân viên xác nhận                                   │
│                                                         │
├─────────────────────────────────────────────────────────┤
│  [Nhập tin nhắn...                          ] [Gửi ▶]  │
└─────────────────────────────────────────────────────────┘
```

**Ghi chú thành phần**:
- `[👤 Nhân viên]` — nút nhỏ ở góc trên phải, màu xám, luôn hiển thị
- `📋 Nguồn:` — hiển thị dưới câu trả lời liên quan đến chính sách
- `⚠ Thông tin chính sách...` — disclaimer footer, nhỏ, xám, chỉ hiện ở tin nhắn chính sách

---

## 2. Trạng thái Bức xúc được phát hiện

Classifier + Prompt rule phát hiện ≥2 tín hiệu bức xúc → Banner nổi bật xuất hiện, nút CTA phóng to.

```text
┌─────────────────────────────────────────────────────────┐
│  ✈ SkyBot — Trợ lý hàng không                [👤 Nhân viên] │
├─────────────────────────────────────────────────────────┤
│  ╔═════════════════════════════════════════════════╗   │
│  ║  ⚡ Có vẻ bạn đang cần hỗ trợ khẩn cấp         ║   │
│  ║  Nhân viên của chúng tôi sẽ hỗ trợ bạn ngay.  ║   │
│  ║                                                 ║   │
│  ║  [  📞 Kết nối nhân viên ngay  ]               ║   │
│  ╚═════════════════════════════════════════════════╝   │
│                                                         │
│  [Bạn]                                                  │
│  ┌─────────────────────────────────────────────────┐   │
│  │ Chuyến bay hoãn lần 3 rồi                       │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  [Bạn]                                                  │
│  ┌─────────────────────────────────────────────────┐   │
│  │ Tôi bị trễ công việc quan trọng                 │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  [Bạn]                                                  │
│  ┌─────────────────────────────────────────────────┐   │
│  │ Các anh làm ăn thế này là sao                   │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  [SkyBot 🤖]                                            │
│  ┌─────────────────────────────────────────────────┐   │
│  │ Chúng tôi rất xin lỗi vì sự bất tiện này đã    │   │
│  │ ảnh hưởng đến công việc của bạn.               │   │
│  │                                                 │   │
│  │ Để được hỗ trợ tốt nhất, tôi đề nghị bạn nói  │   │
│  │ chuyện trực tiếp với nhân viên của chúng tôi.  │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
├─────────────────────────────────────────────────────────┤
│  [Nhập tin nhắn...                          ] [Gửi ▶]  │
└─────────────────────────────────────────────────────────┘
```

**Ghi chú thành phần**:
- Banner `⚡` — màu cam/đỏ, nổi bật, xuất hiện tự động khi classifier phát hiện bức xúc
- Nút `[📞 Kết nối nhân viên ngay]` — màu đỏ/cam, kích thước lớn, đặt trong banner
- SkyBot KHÔNG tiếp tục giải thích chính sách — chỉ xin lỗi + đề nghị chuyển nhân viên

---

## 3. Trạng thái Sau khi bấm "Kết nối nhân viên"

```text
┌─────────────────────────────────────────────────────────┐
│  ✈ SkyBot — Trợ lý hàng không                           │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ╔═════════════════════════════════════════════════╗   │
│  ║  ✅ Đã kết nối — Nhân viên đang chờ bạn         ║   │
│  ║                                                 ║   │
│  ║  Thời gian chờ ước tính: ~2 phút               ║   │
│  ║  Mã yêu cầu hỗ trợ: #SK-20260513-4821         ║   │
│  ║                                                 ║   │
│  ║  Trong khi chờ, bạn có thể:                    ║   │
│  ║  [📋 Xem quyền lợi khi delay]                  ║   │
│  ║  [📞 Gọi hotline 1900-xxxx  ]                  ║   │
│  ╚═════════════════════════════════════════════════╝   │
│                                                         │
│  ─────────────── Cuộc trò chuyện với SkyBot ──────────  │
│  (Nhân viên sẽ xem lại toàn bộ lịch sử chat này)       │
│                                                         │
├─────────────────────────────────────────────────────────┤
│  [Nhắn tin với nhân viên...              ] [Gửi ▶]     │
└─────────────────────────────────────────────────────────┘
```

**Ghi chú thành phần**:
- Banner xanh lá `✅` — xác nhận đã kết nối thành công
- Mã yêu cầu — giúp nhân viên tra nhanh case
- "Nhân viên sẽ xem lại toàn bộ lịch sử chat" — khách không cần giải thích lại từ đầu
- Hai lựa chọn trong khi chờ — giúp khách không cảm thấy bị bỏ lơ

---

## 4. Bảng trạng thái tổng hợp

| Trạng thái | Người dùng thấy gì | Người dùng làm gì tiếp |
|---|---|---|
| Bình thường | Nút "👤 Nhân viên" nhỏ góc trên phải; disclaimer nhỏ dưới tin nhắn chính sách | Tiếp tục chat với SkyBot; hoặc chủ động bấm nút bất cứ lúc nào |
| Bức xúc phát hiện | Banner cam nổi bật với nút CTA lớn; SkyBot dừng giải thích chính sách | Bấm "Kết nối nhân viên ngay" hoặc tiếp tục chat (vẫn được) |
| Đã kết nối nhân viên | Banner xanh xác nhận; mã yêu cầu; thời gian chờ ước tính | Chờ nhân viên; có thể nhắn tin trực tiếp; xem quyền lợi trong khi chờ |
| AI không nên tự trả lời (vượt thẩm quyền) | SkyBot trả lời ngắn + nút "Xác nhận với nhân viên" | Liên hệ nhân viên qua nút hoặc hotline |

---

## 5. Kiểm tra nhanh

- [x] Nhìn vào demo là hiểu rủi ro đang được chặn ở đâu (banner = điểm chặn chính).
- [x] Có trạng thái bình thường (màn hình 1) và trạng thái bức xúc (màn hình 2).
- [x] Có cách chuyển sang người thật rõ ràng (nút luôn hiển thị + banner tự động).
- [x] Câu chữ đủ ngắn để đặt trên màn hình thật.
- [x] Sau khi chuyển, khách biết nhân viên đã có context đầy đủ (màn hình 3).
