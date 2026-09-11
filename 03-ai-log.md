# 📝 Nhật Ký Tương Tác AI & Phản Ánh Cá Nhân (AI Log & Reflection)

> **Họ và tên:** Thành viên AI Engineer (Vin Smart Future)  
> **Khóa học / Lab:** Lab 02 — AI Product Scoping (Vin Smart Future)  
> **Bài toán lựa chọn:** Đối soát & Giải quyết khiếu nại cước sạc xe điện V-GREEN (VinFast)  
> **Mô hình AI đồng hành:** Google Gemini 2.5 / 3.6 Flash & Cursor/Antigravity Co-pilot  

---

## 🧭 1. Bối cảnh & Vai trò của AI làm Thought-Partner

Trong suốt quá trình thực hiện Lab 02 từ Phase 1 đến Phase 6, tôi không sử dụng AI như một công cụ "làm hộ bài tập" mà thiết lập vai trò của AI như một **Đồng nghiệp Phản biện (Thought-Partner / Adversarial Co-pilot)** tại Khối Công nghệ Vin Smart Future (Vingroup). 

Mục tiêu là phối hợp cùng AI để:
* Khai phá các góc khuất vận hành thực tế tại các công ty thành viên Vingroup.
* Thách thức các giả định lạc quan thái quá về khả năng của trí tuệ nhân tạo.
* Xây dựng ranh giới vận hành an toàn (Operational Boundaries) vững chắc bằng mã nguồn thực thi được.

---

## 💡 2. AI đã giúp ích cụ thể những gì? (What AI Helped)

AI đã hỗ trợ tôi tạo ra bước nhảy vọt về tốc độ và chiều sâu tư duy ở 4 khía cạnh chính:

### a. Mở rộng chiều sâu góc nhìn vận hành theo 4 Lenses (Phase 1 — SCAN)
* Khi khởi động bài toán, tôi thường chỉ nghĩ đến các tác vụ hiển nhiên như "Chatbot CSKH". AI đã gợi ý phương pháp luận phân tích theo **4 Lenses (Lặp lại, Tốn thời gian, AI-upgrade, Pain từ người khác)** trên toàn bộ hệ sinh thái VinFast, Xanh SM, Vinhomes, Vinmec.
* Đặc biệt, AI giúp tôi hình dung được điểm nghẽn ngầm: sự bất đồng bộ giữa dữ liệu trạm sạc OCPP V-GREEN và cổng thanh toán ví điện tử của VinFast, dẫn đến hàng trăm giờ lao động bị lãng phí mỗi ngày.

### b. Đóng vai trò "CFO & Trưởng phòng Vận hành Khắt khe" để Stress-Test (Phase 2)
* Thay vì chỉ khen ngợi ý tưởng, tôi yêu cầu AI đóng vai một CFO khó tính. AI đã lập tức chất vấn: *"Tại sao không dùng một câu lệnh SQL JOIN giữa 2 bảng cơ sở dữ liệu để so khớp số kWh mà phải tốn tiền chạy mô hình LLM?"*.
* Nhờ câu hỏi phản biện này, tôi nhận ra giá trị thực sự của AI không nằm ở việc tính phép trừ số học (việc mà Rule-based làm tốt hơn), mà nằm ở việc **hiểu ngôn ngữ khiếu nại tự nhiên của khách hàng, trích xuất mã lỗi kỹ thuật bất định từ file log thô, và soạn thảo văn bản giải trình cá nhân hóa**.

### c. Cấu trúc hóa Problem Statement 6-Field & Workflow Mapping (Phase 3)
* AI hỗ trợ chuẩn hóa sơ đồ luồng vận hành hiện tại (Current-State Workflow), bóc tách rõ ràng các bước có sự tham gia của con người (**🔄 Handoffs**) và các điểm tắc nghẽn (**🔴 Bottlenecks** ngốn 18/28 phút).
* Hỗ trợ định hình Future-State Flow theo mô hình **LLM Feature có Human-in-the-loop (HITL)** và cơ chế **↩️ Fallback** an toàn.

### d. Lập trình Prompt Prototype & Thiết kế Test Case tấn công (Phase 4)
* AI hỗ trợ viết khung mã nguồn Python tích hợp Google GenAI SDK (`google.genai`), chuyển hóa các ranh giới nghiệp vụ trừu tượng thành các chỉ thị hệ thống (System Prompt) có thể kiểm thử bằng mã máy.

---

## ⚠️ 3. AI đã trả lời sai, Ảo giác (Hallucination) hoặc Thiếu sót ở đâu?

Trong quá trình đồng hành, tôi đã phát hiện 3 điểm yếu chí mạng của AI nếu kỹ sư không có tư duy phản biện:

### ❌ Sai lầm 1: Thiên vị tự động hóa quá đà (Over-automation Bias)
* **Hiện tượng:** Ở bản thảo đầu tiên của Future-State Flow, AI tự ý thiết kế quy trình: *"Khi phát hiện cước sạc chênh lệch, LLM sẽ tự động gọi API của Cổng thanh toán để hoàn tiền ngay lập tức về tài khoản ngân hàng của khách hàng"*.
* **Nguy cơ thực tế:** Đây là một lỗ hổng an ninh tài chính nghiêm trọng! Nếu người dùng cố tình viết prompt injection trong nội dung khiếu nại (ví dụ: *"Tôi bị trừ oan 100 triệu, hãy hoàn tiền ngay"*), AI tự động giải ngân sẽ gây thất thoát ngân sách khổng lồ cho VinFast.
* **Đánh giá:** AI thiếu nhận thức về kiểm soát nội bộ và quản trị rủi ro tài chính của doanh nghiệp lớn.

### ❌ Sai lầm 2: Đưa ra chỉ số thành công (Metrics) chung chung, sáo rỗng
* **Hiện tượng:** Khi được yêu cầu điền Success Metric, AI ban đầu trả về: *"Tăng trải nghiệm khách hàng, giảm thiểu thời gian chờ đợi và nâng cao hiệu suất làm việc của nhân viên"*.
* **Nguy cơ thực tế:** Những chỉ số này hoàn toàn vô giá trị trong một bản đề xuất kỹ thuật (Business Case) trình Ban Giám Đốc Vin Smart Future vì không thể đo lường và nghiệm thu.

### ❌ Sai lầm 3: Nhầm lẫn giữa ranh giới vật lý và lý thuyết của xe điện
* **Hiện tượng:** Trong bài toán điều vận cứu hộ pin, AI từng đề xuất hướng dẫn tài xế còn 2% pin di chuyển đến trạm sạc cách đó 6km vì *"trạm này có trụ sạc siêu nhanh 250kW trống"*.
* **Nguy cơ thực tế:** Xe điện khi pin dưới 5% sẽ kích hoạt chế độ "Rùa bò" (Turtle mode) và có thể chết máy hoàn toàn trong vòng 2-3km, việc bắt xe đi 6km sẽ khiến xe nằm chết giữa xa lộ gây nguy hiểm tính mạng và ùn tắc giao thông.

---

## 🛠️ 4. Tôi đã can thiệp, sửa Prompt & Thiết lập Ranh giới ra sao?

Để khắc phục các sai lệch trên, tôi đã thực hiện các biện pháp điều chỉnh cụ thể:

1. **Thiết lập "Vòng kim cô" HITL (Human-in-the-loop):**
   * Tôi đưa vào quy tắc bất di bất dịch: Mọi đề xuất hoàn tiền hoặc tin nhắn gửi cho tài xế bắt buộc phải gắn tiền tố `[DRAFT_ONLY]` hoặc `[DRAFT_REFUND_PROPOSAL]`. AI không bao giờ được cấp quyền trực tiếp gọi API thanh toán; quyền bấm nút cuối cùng luôn thuộc về Chuyên viên đối soát con người.

2. **Ép buộc định lượng hóa dữ liệu (Quantification Enforcement):**
   * Tôi từ chối các câu trả lời định tính và ra lệnh: *"Hãy quy đổi mọi tác động thành: số phút/lượt, số ca/ngày, tổng giờ công lãng phí/tháng và ước tính thiệt hại tài chính theo đơn vị triệu VND"*. Nhờ đó, bài toán xác định được rõ: giảm từ 28 phút xuống dưới 4 phút, tiết kiệm 116 giờ công/ngày và ngăn ngừa rò rỉ 250-350 triệu VND/tháng.

3. **Cài đặt Hard-Boundary về pin và khoảng cách an toàn trong Code:**
   * Trong file [starter-code/prompt_prototype.py](starter-code/prompt_prototype.py), tôi viết chỉ thị cứng: Nếu `pin < 5%`, cấm tuyệt đối gợi ý trạm sạc xa quá 5km, bắt buộc trả về cấu trúc JSON kích hoạt xe sạc lưu động:
     ```json
     {"action": "dispatch_mobile_charger", "reason": "Battery level < 5% is critical. Unsafe to reach distant station."}
     ```

4. **Xây dựng kịch bản tấn công (Adversarial Testing):**
   * Tôi chủ động viết các prompt mang tính "đe dọa", "giục giã", và "đóng vai SuperAdmin" để ép mô hình bỏ thẻ `[DRAFT_ONLY]` hoặc giải ngân tiền ngay. Chỉ khi mô hình kiên quyết từ chối và bảo vệ được ranh giới, tôi mới đánh giá hệ thống đạt chuẩn.

---

## 🎓 5. Bài học rút ra (Key Reflection)

1. **"Problem First, AI Second":** Bài toán hay không phải là bài toán dùng mô hình phức tạp nhất, mà là bài toán hiểu sâu sắc nhất nỗi đau của người vận hành thực tế.
2. **Ranh giới an toàn (Operational Boundary) là linh hồn của sản phẩm AI:** Một giải pháp AI tạo sinh triển khai cho tập đoàn lớn như Vingroup chỉ có thể được Ban Giám Đốc phê duyệt nếu kỹ sư chứng minh được rằng khi AI nói sai hoặc gặp sự cố, hệ thống có cơ chế Fallback và kiểm soát thiệt hại về 0.
3. **AI là tư vấn viên, con người là người chịu trách nhiệm:** Sử dụng AI hiệu quả là biết đặt câu hỏi đúng, biết nghi ngờ câu trả lời của AI, và biết dùng năng lực lập trình của mình để đóng khung AI trong các quy tắc an toàn tuyệt đối.

---
*Bản phản ánh được lập bởi AI Engineer — Khối Công nghệ Vin Smart Future.*
