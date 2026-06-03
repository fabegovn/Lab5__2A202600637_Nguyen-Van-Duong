# Thin SPEC Cuối Day 05 — AI Chatbot hỗ trợ hỏi đáp có nguồn

Thin SPEC không phải PRD đầy đủ. Đây là bản cam kết đủ rõ để sáng Day 06 nhóm build ngay.

## 1. Track, product/app và user

**Track:** AI Chatbot / Customer Support Automation  
**Product/app thật:** AI chatbot hỗ trợ người dùng hỏi đáp, tóm tắt tài liệu và định hướng bước xử lý tiếp theo.  
**User cụ thể:** Người dùng hoặc nhân viên support mới cần tra cứu nhanh thông tin từ tài liệu/quy trình nhưng không muốn đọc toàn bộ file.  
**Nhóm có phải user thật không? Nếu không, khác ở đâu?** Có một phần. Nhóm là người trực tiếp test chatbot, gặp lỗi service, upload file và hỏi bot đọc nội dung. Tuy nhiên nhóm chưa đại diện đầy đủ cho khách hàng thật, nên cần test thêm với 2-3 user ngoài nhóm.

## 2. Evidence summary

| Evidence | Nguồn | User/pain nói lên điều gì? | SPEC phải đổi gì? |
|---|---|---|---|
| Khi login/chat lỗi, console báo `Failed to fetch`, `connection refused`, `502`, user không biết API hay agent chưa chạy. | Self-use/debug local | Lỗi kỹ thuật cần được diễn giải thành hành động rõ cho user. | Thêm failure path service not ready và thông báo recovery. |
| Sau khi upload PDF/file, user hỏi file có pending không và có chạy offline được không. | Self-use/debug file upload | File readiness là điểm gãy lớn trước khi chatbot trả lời có giá trị. | Chatbot phải hiển thị/trả lời theo trạng thái pending/ready/failed và dùng file khi đã ready. |
| User muốn chatbot trả lời dựa trên file, tóm tắt nội dung và không bịa. | Requirement từ nhóm | Trust quan trọng hơn câu trả lời dài. | Output phải có source/citation hoặc báo không đủ dữ liệu. |
| User hỏi câu mơ hồ như "đọc file", "cho tôi nội dung", "xử lý thế nào". | Self-use trong chat | Intent không rõ nếu bot không hỏi lại hoặc phân loại sai. | AI decision đầu tiên là phân loại intent: summary, Q&A, troubleshooting, fallback. |

## 3. Pain statement

```text
User cần hỏi chatbot về một tài liệu hoặc một vấn đề cụ thể đang gặp khó ở bước biến câu hỏi mơ hồ thành câu trả lời đáng tin,
vì chatbot có thể chưa đọc được file, thiếu context, lỗi service hoặc trả lời không dẫn nguồn,
dẫn tới user không biết bot có hiểu đúng không và không biết bước tiếp theo phải làm gì.
Bằng chứng chính là self-use/debug: failed to fetch khi login/chat, file PDF pending, câu hỏi "push file PDF lên không được lý do là gì", và nhu cầu chatbot trả lời dựa trên nội dung file.
```

## 4. Build slice

```text
Cho người dùng đang hỏi chatbot về một tài liệu hoặc một lỗi/quy trình cụ thể,
prototype sẽ dùng AI để phân loại intent, đọc/tóm tắt context liên quan, trả lời có nguồn và đề xuất bước tiếp theo,
tạo ra câu trả lời ngắn, có citation/trạng thái dữ liệu, kèm câu hỏi làm rõ nếu thiếu thông tin,
và xử lý failure mode bot không chắc/file chưa ready/service chưa chạy bằng thông báo recovery rõ ràng thay vì trả lời bừa.
```

## 5. Auto/Aug decision

Chọn một:

- [x] **Augmentation:** AI gợi ý/draft/phân loại, user quyết cuối.
- [ ] **Conditional automation:** AI tự làm trong case hẹp; case mơ hồ/rủi ro chuyển người.
- [ ] **Automation:** AI tự quyết và tự hành động.

**Lý do chọn:** Chatbot có thể hỗ trợ đọc, tóm tắt và định hướng, nhưng user vẫn phải quyết định bước cuối vì câu trả lời có thể phụ thuộc vào context, file, trạng thái hệ thống hoặc quy trình thật.  
**Human role:** decider / rescuer / reviewer

## 6. Four paths

| Path | Prototype phải thể hiện gì? |
|---|---|
| Happy | User hỏi câu rõ hoặc upload file ready; chatbot phân loại đúng intent, trả lời ngắn có nguồn và bước tiếp theo. |
| Low-confidence | Câu hỏi thiếu thông tin hoặc file chưa được đọc đầy đủ; chatbot hỏi lại 1-2 câu hoặc báo phần chưa chắc. |
| Failure | API/agent chưa chạy, file quá lớn, parser fail; chatbot/UI báo lỗi cụ thể và hướng dẫn cách recover. |
| Correction | User nói "ý này sai", "đọc lại file", "tóm tắt ngắn hơn"; chatbot cập nhật câu trả lời và ghi nhận correction trong session. |

## 7. Failure mode nguy hiểm nhất

```text
Nếu user hỏi một câu cần thông tin từ file/quy trình nhưng file chưa ready hoặc context thiếu,
AI có thể tự bịa câu trả lời nghe có vẻ đúng,
hậu quả là user làm sai bước xử lý hoặc mất niềm tin vào chatbot.
Prototype sẽ xử lý bằng show source/citation, hiển thị trạng thái pending/ready/failed, hỏi lại khi thiếu dữ liệu và fallback sang thông báo service/file recovery.
Owner kiểm thử path này là Tuan.
```

## 8. Owner plan cho sáng Day 06

| Thành viên | Việc phụ trách | Bằng chứng cần có trong repo |
|---|---|---|
| Tuan | Research / evidence | Screenshot lỗi fetch, file pending, câu hỏi chatbot thực tế |
| Tuan | SPEC | Thin SPEC đã điền + evidence pack |
| Tuan | Prototype | Flow chat: hỏi câu -> phân loại intent -> trả lời có nguồn |
| Tuan | Test / failure path | Test service not ready, file pending, câu hỏi thiếu thông tin |
| Tuan | Demo script / repo | Script demo 3-5 phút + README lệnh chạy local |
