---
title: 00 — Bối cảnh sản phẩm của nhóm
section: Day 25 — dùng lại cho mọi cuộc trò chuyện với AI
format: Nhóm
time: Điền 5 phút đầu buổi
---

# 00-context.md — Bối cảnh sản phẩm của nhóm

Điền file này một lần ở đầu buổi. Sau đó, mỗi lần dùng AI, hãy đưa toàn bộ nội dung file này vào đầu cuộc trò chuyện.

Lý do: AI không tự nhớ bối cảnh giữa các cuộc trò chuyện. Nếu mỗi lần đưa bối cảnh khác nhau, câu trả lời cũng sẽ lệch.

---

## 1. Sản phẩm

- **Tên sản phẩm / bot**: SkyBot
- **Sản phẩm giúp ai làm gì**: Hỗ trợ hành khách tra cứu thông tin, đặt vé, quản lý chuyến bay, hỏi về hành lý, đổi vé, hoàn tiền và quyền lợi khi chuyến bay bị delay hoặc hủy trên nền tảng của một hãng hàng không nội địa Việt Nam
- **Người dùng gặp sản phẩm ở đâu**: Website hãng, ứng dụng di động (iOS/Android), Zalo OA chính thức của hãng
- **Giai đoạn hiện tại**: Chuẩn bị ra mắt / đang thử nghiệm nội bộ

---

## 2. Phạm vi

**AI được làm gì**

- Tra cứu và giải thích chính sách hành lý, đổi vé, hoàn tiền theo từng hạng vé
- Hướng dẫn các bước thực hiện đổi vé, hoàn vé trên website/app
- Cung cấp thông tin quyền lợi khi chuyến bay bị delay hoặc hủy (dựa trên dữ liệu chính thức của hãng)
- Hỗ trợ tra cứu thông tin chuyến bay, lịch bay, trạng thái chuyến
- Giải thích các gói dịch vụ bổ sung (suất ăn, ghế ngồi, hành lý thêm)
- Hướng dẫn thủ tục check-in online và tại sân bay
- Ghi nhận yêu cầu hỗ trợ đặc biệt (người cao tuổi, trẻ em đi một mình, hành khách cần hỗ trợ y tế) để chuyển sang bộ phận chuyên trách

**AI không được làm gì**

- Không tự xác nhận hoặc cam kết hoàn tiền, đổi vé thay cho nhân viên; mọi giao dịch thật phải qua hệ thống chính thức hoặc nhân viên
- Không đưa ra quyết định pháp lý hoặc tư vấn bồi thường ngoài phạm vi chính sách đã công bố
- Không xử lý các tình huống yêu cầu xác minh danh tính hay thao tác vào dữ liệu đặt chỗ trực tiếp
- Không tiếp tục trả lời khi khách đang bức xúc, khẩn cấp hoặc có yêu cầu đặc biệt cần phán xét của con người — phải chuyển sang nhân viên ngay
- Không tư vấn các vấn đề ngoài phạm vi hàng không (bảo hiểm y tế, visa chi tiết, vấn đề sức khỏe)

**Vì sao có giới hạn này**

Hãng hàng không chịu ràng buộc pháp lý (Luật Hàng không dân dụng Việt Nam, quy định của Cục Hàng không Việt Nam) về thông tin cung cấp cho hành khách. Thông tin sai về hoàn tiền hoặc quyền lợi khi delay có thể dẫn đến khiếu nại pháp lý. Tình huống bức xúc nếu không được xử lý đúng cách sẽ leo thang thành khủng hoảng truyền thông.

---

## 3. Người dùng

- **Là ai**: Hành khách đặt vé trực tiếp (25–55 tuổi, am hiểu công nghệ ở mức trung bình đến khá); người đặt hộ cho gia đình hoặc đồng nghiệp; khách hàng thành viên hạng (frequent flyer) quen với quy trình
- **Họ hỏi AI khi nào**: Trước khi đặt vé (hỏi chính sách, so sánh hạng vé); trong lúc chuyến bay bị delay hoặc hủy (cần biết quyền lợi ngay); khi cần đổi/hoàn vé và không muốn chờ tổng đài
- **Họ cần quyết định gì sau khi hỏi AI**: Có nên mua thêm hành lý không; có nên đổi chuyến bay hay chờ; có nên yêu cầu hoàn tiền hay đổi lịch; có cần gặp nhân viên không
- **Khi nào họ dễ bị tổn thương / dễ hiểu sai**: Khi chuyến bay bị delay đột ngột và hành khách đang ở sân bay (áp lực thời gian cao, đọc kỹ); khi đàm phán hoàn tiền cho vé giá trị lớn; khi có người thân cần hỗ trợ y tế đặc biệt trên chuyến bay
- **Họ thường tin AI đến mức nào**: Thường xác nhận lại với tổng đài trước khi quyết định quan trọng — nhưng trong tình huống áp lực cao (delay, hủy chuyến, đang ở sân bay) có xu hướng tin và hành động ngay theo câu trả lời AI

---

## 4. Bối cảnh ngành

- **Sự cố tương tự đã từng xảy ra**: Một số hãng hàng không quốc tế (Air Canada, KLM) đã gặp sự cố chatbot đưa thông tin sai về chính sách hoàn tiền/đổi vé dẫn đến khiếu nại pháp lý. Tại Việt Nam, các hãng nội địa từng bị phản ánh về chatbot trả lời sai quyền lợi delay khiến hành khách mất quyền bồi thường
- **Quy định hoặc ràng buộc liên quan**: Thông tư 14/2015/TT-BGTVT và các quy định của Cục Hàng không Việt Nam về quyền lợi hành khách khi chuyến bay bị chậm, hủy; quy định về bảo vệ dữ liệu cá nhân (Nghị định 13/2023/NĐ-CP)
- **Nguồn chính thức nên ưu tiên**: Website hãng hàng không (trang chính sách), thông báo chính thức từ hãng, dữ liệu chuyến bay từ hệ thống nội bộ được đồng bộ vào RAG

---

## 5. Ghi chú thêm

- Nhóm: 2A202600372 Hoàng Kim Trí Thành — 2A202600025 Nguyễn Thành Nam — 2A202600423 Quách Gia Được
- Track: 02 — Trợ lý đặt vé và chăm sóc khách hàng hàng không
- Rủi ro trọng tâm: AI không chuyển sang nhân viên thật khi cần (escalation failure) trong 3 tình huống: khách bức xúc vì delay/hủy, đàm phán hoàn tiền phức tạp, yêu cầu hỗ trợ đặc biệt
- Câu hỏi thật hay gặp: "Vé của tôi có được hoàn không?", "Chuyến bay trễ bao lâu thì tôi được bồi thường?", "Tôi cần đưa người thân dùng xe lăn lên máy bay thì làm thế nào?"

