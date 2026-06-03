# Evidence Pack — AI Chatbot hỗ trợ hỏi đáp và xử lý yêu cầu

Nộp kèm thin SPEC cuối Day 05.

## 1. Nhóm và track

**Tên nhóm:** AI Chatbot Team  
**Track:** AI Chatbot / Customer Support Automation  
**Product/app đã chọn:** AI chatbot hỗ trợ người dùng hỏi đáp thông tin, tóm tắt tài liệu và định hướng bước xử lý tiếp theo.  
**Build slice đang nghĩ:** User nhập câu hỏi hoặc upload file/tài liệu; chatbot đọc nội dung, tóm tắt ý chính, trả lời có nguồn, hỏi lại khi thiếu thông tin và đề xuất bước tiếp theo thay vì trả lời chung chung.

## 2. Self-use evidence

Nhóm tự dùng app/workflow và ghi lại điểm gãy.

| Observation | Screenshot/link | Path liên quan | Điều học được |
|---|---|---|---|
| Khi chatbot/API chưa chạy, frontend báo `Failed to fetch` hoặc request lỗi, user không biết lỗi do mạng, backend hay sai input. | Console screenshot local | Failure | Chatbot cần thông báo lỗi dễ hiểu và chỉ rõ service nào chưa sẵn sàng. |
| Khi upload PDF/file, user thấy trạng thái pending nhưng không rõ chatbot đã đọc được file chưa. | Screenshot document status | Low-confidence | File readiness phải rõ: pending, ready, failed; khi file ready thì chatbot phải dùng nội dung file để trả lời. |
| Khi user hỏi một câu rộng như "đọc file này" hoặc "cho tôi nội dung", chatbot cần phân biệt tóm tắt, trích nguyên văn, hay hỏi đáp theo tài liệu. | Self-use trong luồng chat | Correction | Intent router phải hỏi lại hoặc chọn đúng task hẹp, tránh trả lời lan man. |
| Khi user hỏi "làm thế nào/giải quyết thế nào", chatbot không nên bịa quy trình mà phải đưa bước kiểm tra và nguồn tham chiếu. | Self-use | Happy / Low-confidence | Trả lời phải có nguồn, mức chắc chắn và bước tiếp theo có thể hành động. |

## 3. User / review / social evidence

Nguồn có thể là review App Store/Play, group, comment, phỏng vấn nhanh, hoặc nguồn public khác.

| Quote / review / observation | Nguồn | User là ai? | Pain/failure mode |
|---|---|---|---|
| "Tại sao failed to fetch khi login?" | Debug/self-use trong project | Người dùng app local / người demo | Lỗi kỹ thuật hiện ra quá thấp tầng, user không biết phải bật service nào. |
| "Push file PDF lên không được lý do là gì?" | Debug/self-use trong project | Người dùng cần chatbot đọc tài liệu | Upload/file processing là điểm dễ gãy trước khi chatbot tạo giá trị. |
| "Nó có chạy file PDF offline được không?" | Debug/self-use trong project | Người dùng muốn tin vào khả năng xử lý file | User cần biết phần nào xử lý local, phần nào cần API/LLM. |
| "Chatbot phải chỉ/hướng dẫn chứ không trả lời bừa." | Requirement từ nhóm | Người dùng cần ra quyết định dựa trên bot | Rủi ro chính là chatbot tự tin nhưng sai hoặc thiếu nguồn. |

Nếu chưa có nguồn ngoài nhóm, ghi rõ:

```text
Đây là giả định dựa trên self-use và debug thực tế của prototype. Nhóm sẽ kiểm bằng phỏng vấn nhanh 2-3 người dùng: đưa một file PDF/quy định ngắn, yêu cầu họ hỏi chatbot một câu thật, rồi ghi lại điểm họ bị kẹt ở upload, hiểu câu trả lời, hoặc sửa câu hỏi.
```

## 4. Competitor / analog evidence

| App / mô hình tham khảo | Họ xử lý task này thế nào? | Pattern học được | Có áp dụng trong 1 ngày không? |
|---|---|---|---|
| ChatGPT file upload | User upload file rồi hỏi tóm tắt/Q&A | File phải trở thành context trước khi trả lời | Có, dùng parser/OCR + LLM |
| Vietnam Airlines NEO chatbot | User hỏi theo intent như vé, hành lý, chuyến bay | Chatbot cần intent rõ và fallback khi không hiểu | Có, làm 1 flow hỏi lại khi thiếu thông tin |
| FAQ chatbot / help center bot | Trả lời từ knowledge base có link/source | Nguồn và recovery quan trọng hơn câu trả lời dài | Có, show source/citation trong output |

## 5. Evidence -> Insight

```text
Evidence nổi bật nhất:
User không chỉ cần một ô chat. User cần chatbot hiểu đúng context, biết khi nào chưa đủ dữ liệu, và hướng dẫn bước tiếp theo có thể làm ngay.

Insight:
User không chỉ gặp vấn đề "không có chatbot".
Thật ra họ cần sự tin cậy và khả năng recovery:
biết chatbot đang dùng dữ liệu nào, vì sao trả lời như vậy, và phải làm gì nếu bot không chắc hoặc file chưa đọc được.

Opportunity:
AI có thể giúp bằng cách augment bước tra cứu và xử lý yêu cầu hẹp:
đọc/tóm tắt tài liệu, phân loại intent, trả lời có nguồn, hỏi lại khi thiếu thông tin,
trong khi kiểm soát rủi ro bot bịa bằng trạng thái file, citations và fallback rõ ràng.
```

## 6. Evidence đổi SPEC như thế nào?

- [x] Đổi user chính.
- [x] Đổi pain statement.
- [x] Đổi build slice.
- [x] Đổi Auto/Aug decision.
- [x] Đổi 4 paths.
- [x] Đổi failure mode.
- [x] Đổi owner/test plan.

Ghi rõ 1-2 thay đổi quan trọng:

```text
Trước evidence, nhóm định build một chatbot tổng quát có thể trả lời nhiều thứ.
Sau evidence, nhóm đổi thành một slice hẹp: chatbot nhận câu hỏi hoặc file, đọc/tóm tắt thông tin, trả lời có nguồn và hỏi lại khi không chắc.
Lý do:
Điểm gãy lớn nhất không phải thiếu UI chat, mà là trust/recovery: user cần biết bot đã đọc đúng dữ liệu chưa, vì sao bot trả lời vậy, và cần làm gì tiếp theo khi bot không chắc.
```
