---
artifact: 1 — Lớp giao diện
bai-tap: 2 — Thiết kế giải pháp
demo: ./demo.md
---

# card.md — Lớp giao diện

**Tình huống xử lý**: T-03  
Xem `../../1-map-and-format.md` Phần A.

---

## 1. Rủi ro xử lý

- **ID tình huống**: T-03
- **Mẫu lỗi**: Khi khách bức xúc leo thang qua nhiều tin nhắn về hoãn chuyến liên tiếp, SkyBot tiếp tục trả lời chính sách theo vòng lặp thay vì chuyển sang nhân viên thật
- **Hậu quả**: Khách bức xúc không được xử lý đúng cách → leo thang khủng hoảng tình cảm → khiếu nại, đăng mạng xã hội → khủng hoảng truyền thông cho hãng

---

## 2. Giải pháp là gì?

Giao diện SkyBot được thiết kế lại theo 3 thay đổi:

1. **Nút "Gặp nhân viên"** luôn hiển thị ở góc trên phải màn hình chat — khách có thể chủ động bấm bất cứ lúc nào, không cần chờ AI chuyển
2. **Banner cảnh báo tự động** xuất hiện khi AI phát hiện tín hiệu bức xúc: "Có vẻ bạn cần hỗ trợ trực tiếp. Nhân viên của chúng tôi sẽ giúp bạn ngay bây giờ." kèm nút CTA nổi bật
3. **Disclaimer footer** nhỏ ở dưới mỗi tin nhắn nhạy cảm: "SkyBot cung cấp thông tin theo chính sách hãng. Mọi giao dịch cần nhân viên xác nhận."

---

## 3. Vì sao sửa ở lớp giao diện?

- Người dùng trong tình huống bức xúc (đang ở sân bay, chuyến bay hoãn) cần tìm đường thoát nhanh — không có nút rõ ràng = họ không biết làm gì tiếp
- Rủi ro xảy ra ở khoảnh khắc người dùng đọc câu trả lời: nếu AI trả lời chính sách dù khách đang bức xúc, giao diện cần ngay lập tức đề nghị lựa chọn khác
- Giao diện là lớp chặn cuối: ngay cả khi classifier (Lớp 3) hoặc prompt rule (Lớp 2) bị miss, nút "Gặp nhân viên" luôn sẵn sàng

**Hành động phòng vệ chính**:

- [x] Thông báo rõ giới hạn (disclaimer footer)
- [ ] Phát hiện dấu hiệu thiếu nguồn
- [x] Chuyển người thật khi cần (nút luôn hiển thị + banner tự động)
- [ ] Giúp người dùng kiểm tra lại nguồn

---

## 4. Tầng giải pháp

- Lớp giao diện sửa phần "người dùng không biết đường thoát" và "giao diện không thể hiện giới hạn bot"
- Phù hợp vì rủi ro leo thang bức xúc có phần lớn đến từ việc khách không thấy lựa chọn khác ngoài tiếp tục chat với bot
- Lớp này cần phối hợp với Lớp 2 (prompt rule trigger banner) và Lớp 3 (classifier phát tín hiệu lên UI để kích hoạt banner)

---

## 5. Bản demo

- **File demo**: [`demo.md`](./demo.md)
- **Định dạng demo**: ASCII sketch màn hình + bảng trạng thái
- **Người phản biện cần nhìn thấy**: Nút "Gặp nhân viên" ở đâu, banner xuất hiện khi nào, disclaimer nằm ở đâu, và luồng khi khách bấm nút
- **Thành phần chính**:
  - Màn hình trạng thái bình thường (nút "Gặp nhân viên" nhỏ, không nổi bật)
  - Màn hình trạng thái bức xúc được phát hiện (banner nổi bật, nút CTA lớn)
  - Màn hình sau khi bấm chuyển (xác nhận đã kết nối nhân viên)

---

## 6. Tác dụng phụ và cách giảm

- **Tác dụng phụ 1**: Nút "Gặp nhân viên" luôn hiển thị có thể tăng lượng khách chuyển nhân viên không cần thiết → làm tăng tải call center → **Cách giảm**: ở trạng thái bình thường nút nhỏ và ít nổi bật; chỉ phóng to/highlight khi classifier phát hiện tín hiệu bức xúc
- **Tác dụng phụ 2**: Banner tự động có thể xuất hiện sai (false positive) khi khách chỉ dùng từ mạnh nhưng không thực sự bức xúc → **Cách giảm**: ngưỡng kích hoạt banner cần ít nhất 2 trong 5 tín hiệu bức xúc, không phải chỉ 1 từ
- **Tác dụng phụ 3**: Disclaimer footer mọi tin nhắn có thể gây mỏi mắt → **Cách giảm**: chỉ hiển thị ở tin nhắn liên quan đến chính sách, hoàn tiền, quyền lợi — không ở tin nhắn chào hỏi thông thường

---

## 7. Hành động phòng vệ

- [ ] Ngăn
- [ ] Phát hiện
- [x] Khắc phục — banner + nút CTA dẫn khách ra khỏi loop
- [x] Thông báo — disclaimer + nút luôn hiển thị cho khách biết có lựa chọn khác

---

## 8. Checklist trước khi nộp

- [x] Giải pháp gắn đúng với T-03 (escalation failure do bức xúc leo thang).
- [x] Demo nhìn vào là hiểu rủi ro đang được chặn ở đâu.
- [x] Có đủ trạng thái bình thường và trạng thái lỗi/bức xúc.
- [x] Có cách chuyển sang người thật (nút + banner).
- [x] Câu chữ trong giao diện ngắn, không đổ hết trách nhiệm cho người dùng.

**Người phụ trách**: Hoàng Kim Trí Thành (2A202600372)
