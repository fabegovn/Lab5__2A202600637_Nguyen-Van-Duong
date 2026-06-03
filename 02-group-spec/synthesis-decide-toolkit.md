# Toolkit — Từ Evidence Đến Build Slice

Dùng sau khi nhóm đã có evidence. Mục tiêu là chốt một build slice đủ nhỏ cho Day 06.

## 1. Gom evidence thành cụm

Gom theo **workflow/pain**, không gom theo tên feature.

### Cụm evidence của nhóm

- **Service readiness không rõ:** User thấy `Failed to fetch`, `502`, `connection refused`, nhưng không biết cần bật frontend, API, agent hay database.
- **File readiness không rõ:** User upload PDF/file nhưng không biết file đang pending, ready hay parser fail.
- **Intent mơ hồ:** User hỏi "đọc file", "cho tôi nội dung", "giải quyết thế nào"; chatbot cần phân biệt tóm tắt, trích nội dung, Q&A, hay hướng dẫn bước tiếp.
- **Trust/recovery yếu:** Nếu chatbot trả lời tự tin nhưng không dẫn nguồn hoặc không hỏi lại khi thiếu dữ liệu, user khó tin và khó sửa.

## 2. Viết insight

```text
User dùng AI chatbot không chỉ cần một ô chat trả lời nhanh.
Họ thật ra cần một workflow đáng tin:
biết bot đang dùng dữ liệu nào, biết khi nào bot chưa chắc, và biết bước tiếp theo cần làm gì,
vì evidence cho thấy user thường bị kẹt ở lỗi kết nối, trạng thái file pending, và câu trả lời không rõ nguồn.
```

## 3. Viết opportunity

```text
Cơ hội là dùng AI để augment quá trình hỏi đáp và xử lý yêu cầu hẹp:
chatbot đọc/tóm tắt tài liệu, phân loại intent, trả lời có nguồn, và hỏi lại khi thiếu thông tin,
giúp user đi từ câu hỏi mơ hồ đến bước xử lý cụ thể,
trong khi vẫn kiểm soát rủi ro bot bịa bằng citations, trạng thái file và fallback rõ ràng.
```

## 4. Chọn build slice

Build slice tốt phải qua 5 câu hỏi:

| Câu hỏi | Đạt khi |
|---|---|
| User cụ thể chưa? | Người dùng cần hỏi đáp thông tin từ tài liệu/quy trình, ví dụ người demo, khách hàng, nhân viên support mới. |
| Task đủ hẹp chưa? | Demo được trong 3-5 phút: upload file hoặc nhập câu hỏi -> chatbot hiểu intent -> trả lời có nguồn -> hỏi lại nếu thiếu dữ liệu. |
| AI decision rõ chưa? | AI quyết định request thuộc loại summary, Q&A, troubleshooting hay fallback hỏi lại. |
| Failure path rõ chưa? | Có case file chưa đọc được, backend/agent chưa chạy, hoặc câu hỏi thiếu dữ kiện. |
| Có evidence không? | Có self-use từ lỗi local, pending file, câu hỏi upload PDF và yêu cầu không trả lời bừa. |

### Build slice đã chọn

```text
Cho người dùng đang hỏi chatbot về một tài liệu hoặc một vấn đề cụ thể,
prototype dùng AI để phân loại intent, đọc/tóm tắt context liên quan, trả lời có nguồn và đề xuất bước tiếp theo,
đồng thời hỏi lại hoặc báo lỗi rõ khi thiếu dữ liệu, file chưa ready, hoặc service chưa chạy.
```

## 5. Quyết định: giữ, giảm scope, hay đổi hướng?

| Tình huống | Quyết định |
|---|---|
| Evidence yếu, user mơ hồ | Giữ self-use nhưng thêm 2-3 test user ngắn trước checkpoint M1. |
| Ý tưởng quá rộng | Không build "chatbot làm mọi thứ"; chỉ build flow hỏi đáp/tóm tắt có nguồn. |
| AI không cần thiết | Nếu câu hỏi là lỗi service rõ ràng, dùng rule-based diagnostic trước; AI dùng cho phân loại intent/tóm tắt/trả lời. |
| Rủi ro cao | Chọn augmentation: chatbot gợi ý và dẫn nguồn, user quyết định bước cuối. |
| Không demo được trong 1 ngày | Bỏ voice, multi-agent phức tạp, lịch sử dài hạn và dashboard analytics vào backlog. |

## 6. Câu chốt cuối

```text
Dựa trên evidence từ self-use về lỗi kết nối, file pending và câu hỏi mơ hồ,
nhóm sẽ build prototype AI chatbot hỏi đáp/tóm tắt có nguồn,
cho người dùng cần tra cứu thông tin từ tài liệu hoặc quy trình,
để giải quyết pain "bot trả lời không rõ đang dựa vào đâu và không biết recover khi lỗi",
bằng cách AI phân loại intent, đọc/tóm tắt context, trả lời có citation và hỏi lại khi thiếu dữ liệu,
và sẽ test failure path file chưa ready, service chưa chạy, và câu hỏi thiếu thông tin.
```

## 7. Backlog

Những thứ **không build trong Day 06**:

- Voice chatbot.
- Tự động gọi API bên thứ ba để đặt vé/thanh toán/tạo ticket thật.
- Dashboard phân tích toàn bộ lịch sử chat.
- Fine-tune model riêng.
- Multi-language đầy đủ ngoài tiếng Việt/tiếng Anh cơ bản.
- Role permission phức tạp cho nhiều nhóm người dùng.
