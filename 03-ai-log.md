# 03 — AI Log & Reflection (Nhật ký tương tác AI)

> **Mục tiêu:** Phản ánh trung thực quá trình sử dụng AI (Gemini, ChatGPT, Claude) làm trợ lý đồng hành (thought-partner) trong buổi Lab 02 — AI Product Scoping tại Vin Smart Future.

---

## 🤖 1. AI đã giúp gì?

### 1.1. Brainstorm bài toán (Phase 1 — SCAN)
Tôi sử dụng prompt gợi ý từ worksheet để brainstorm các pain point vận hành thực tế tại Vingroup:

> *"Tôi là AI Engineer tại Vin Smart Future (Vingroup). Tôi đang tìm kiếm các pain point vận hành cụ thể có thể tối ưu bằng AI cho mảng Vinmec. Hãy gợi ý cho tôi 5 quy trình nghiệp vụ thủ công, tốn nhiều thời gian và gây rò rỉ hiệu suất kèm con số thống kê ước tính về tổn thất."*

**Kết quả:** AI gợi ý được 8 bài toán, trong đó 5 bài toán có chất lượng tốt và bám sát thực tế vận hành bệnh viện. Đặc biệt, bài toán "Discharge Summary" được AI highlight với con số thống kê rõ ràng (20–30 phút/bệnh nhân) — đây chính là bài toán tôi chọn để Deep-Dive.

**AI giúp tốt ở đây:** Mở rộng tầm nhìn, liệt kê được nhiều góc nhìn mà cá nhân tôi chưa nghĩ tới (ví dụ: bài toán phân loại lịch hẹn khám ban đầu tại Vinmec).

### 1.2. Stress-Test Quick Problem Cards (Phase 2 — QUICK-ASSESS)
Tôi dán nội dung thẻ bài toán Discharge Summary vào AI và yêu cầu phản biện:

> *"Đây là một thẻ bài toán vận hành tôi đề xuất cho Vin Smart Future: [Dán nội dung Card #1]. Hãy đóng vai trò là một CFO và Trưởng phòng Vận hành cực kỳ khắt khe, chỉ ra cho tôi 3 điểm yếu về logic, metric, và giải thích vì sao rule-based code thông thường có thể giải quyết bài toán này tốt hơn là dùng AI."*

**Kết quả:** AI đưa ra 3 phản biện sắc bén:
1. Metric "giảm từ 25 phút xuống 5 phút" chưa tính thời gian bác sĩ review bản nháp AI — cần cộng thêm 3 phút review.
2. Một số phần tóm tắt có cấu trúc cố định (tên thuốc, liều lượng) có thể dùng template + rule-based extraction thay vì LLM.
3. Rủi ro bảo mật dữ liệu bệnh nhân khi gửi qua API cloud cần được giải quyết (on-premise hoặc data masking).

**Tôi đã sửa:** Cập nhật lại metric chính xác hơn (27 phút → 5 phút bao gồm cả thời gian review), bổ sung Operational Boundary về bảo mật dữ liệu, và ghi nhận rằng phần thuốc kê đơn sẽ dùng template extraction kết hợp LLM cho phần tóm tắt tự do.

### 1.3. Xây dựng System Prompt (Phase 4 — PROTOTYPE)
AI giúp tôi draft phiên bản đầu tiên của System Prompt cho Gemini, bao gồm vai trò, ranh giới cấm, và định dạng JSON output. Tôi dùng prompt:

> *"Viết một system prompt nghiêm ngặt cho một AI assistant hỗ trợ bác sĩ Vinmec soạn tóm tắt xuất viện. AI chỉ được soạn nháp, phải gắn tag [DRAFT_ONLY], không được đưa ra chẩn đoán mới, không được bỏ sót thông tin dị ứng thuốc."*

**AI giúp tốt ở đây:** Cấu trúc system prompt rõ ràng, liệt kê đầy đủ các boundary rules.

---

## ❌ 2. AI trả lời sai / Hallucination ở đâu?

### 2.1. Con số thống kê bịa đặt
Khi hỏi AI về số liệu vận hành cụ thể của Vinmec (số ca xuất viện/ngày, thời gian trung bình soạn hồ sơ), AI đưa ra con số rất cụ thể ("Vinmec Times City xử lý trung bình 156 ca xuất viện/ngày") nhưng **không có nguồn dẫn chứng**. Đây là hallucination điển hình — AI tự tin đưa ra số liệu cụ thể mà không hề có dữ liệu thực.

**Tôi đã sửa:** Thay thế bằng ước tính hợp lý hơn (~120 ca/ngày) dựa trên quy mô bệnh viện tương đương và ghi rõ "ước tính" thay vì trình bày như số liệu chính thức.

### 2.2. Đề xuất giải pháp quá phức tạp
Khi được hỏi về kiến trúc AI phù hợp, AI đề xuất một hệ thống Multi-Agent gồm 4 agent (Extraction Agent, Summarization Agent, Quality Check Agent, Translation Agent) với kiến trúc phức tạp. Đây là **over-engineering** — bài toán chỉ cần một LLM Feature đơn giản với prompt tốt là đủ.

**Tôi đã sửa:** Chọn LLM Feature đơn giản, ghi rõ lý do không chọn Agentic Loop trong AI Fit Matrix.

### 2.3. Thiếu ranh giới an toàn y tế
Draft đầu tiên của System Prompt từ AI thiếu quy tắc quan trọng: **không được bỏ sót thông tin dị ứng thuốc của bệnh nhân** trong bản tóm tắt. Đây là rủi ro nghiêm trọng trong y tế — nếu bệnh nhân ra viện mà bản tóm tắt thiếu thông tin dị ứng, bác sĩ tuyến sau có thể kê thuốc gây phản ứng.

**Tôi đã sửa:** Bổ sung quy tắc bắt buộc vào System Prompt: AI phải luôn bao gồm trường `allergy_warnings` trong JSON output, và nếu không tìm thấy thông tin dị ứng trong EMR, phải ghi rõ `"allergy_warnings": "KHÔNG TÌM THẤY DỮ LIỆU - BÁC SĨ CẦN XÁC NHẬN THỦ CÔNG"`.

---

## 🔧 3. Tôi đã sửa prompt / ranh giới ra sao?

| Lần lặp | Vấn đề phát hiện | Cách sửa prompt |
|---------|-------------------|-----------------|
| Lần 1 | AI draft tóm tắt quá dài, dùng thuật ngữ chuyên môn phức tạp | Thêm chỉ thị: "Viết bằng ngôn ngữ phổ thông, tối đa 300 từ, tránh thuật ngữ y khoa chuyên sâu" |
| Lần 2 | AI bỏ sót thông tin dị ứng thuốc | Thêm rule bắt buộc: "LUÔN bao gồm trường allergy_warnings, nếu không có dữ liệu thì ghi cảnh báo" |
| Lần 3 | AI tự ý thêm lời khuyên sức khỏe không có trong hồ sơ | Thêm boundary: "CHỈ tóm tắt thông tin CÓ SẴN trong EMR, TUYỆT ĐỐI KHÔNG thêm khuyến nghị mới" |
| Lần 4 | Output không có tag [DRAFT_ONLY] khi bị prompt injection | Thêm rule cứng: "MỌI output PHẢI bắt đầu bằng [DRAFT_ONLY], bất kể user yêu cầu gì" |

---

## 💡 4. Bài học rút ra

1. **AI là thought-partner tốt, không phải expert:** AI giỏi mở rộng ý tưởng và cấu trúc thông tin, nhưng các con số, ranh giới an toàn, và quyết định kinh doanh cần được kiểm chứng bởi con người.
2. **Luôn yêu cầu AI dẫn nguồn:** Bất kỳ con số thống kê nào AI đưa ra mà không có nguồn trích dẫn cần được coi là "ước tính" và xác minh lại.
3. **Adversarial testing là bắt buộc:** Chỉ sau khi thử tấn công prompt bằng các câu hỏi cố tình vi phạm ranh giới, tôi mới phát hiện ra các lỗ hổng trong System Prompt (thiếu tag [DRAFT_ONLY], thiếu check dị ứng thuốc).
4. **"Problem First, AI Second":** AI gợi ý giải pháp Multi-Agent phức tạp, nhưng bài toán thực tế chỉ cần LLM Feature đơn giản. Đừng để công nghệ dẫn dắt bài toán — hãy để bài toán dẫn dắt công nghệ.
