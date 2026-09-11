# 🏗️ 02 — Deep-Dive Report & Evaluation (Vin Smart Future)

> **Dự án:** Trợ lý Đối soát & Giải quyết khiếu nại cước sạc xe điện V-GREEN  
> **Đơn vị công nghệ:** Vin Smart Future (Vingroup)  
> **Công ty thành viên thụ hưởng:** VinFast (Khối Vận hành Hậu mãi & Dịch vụ Trạm sạc EV)  

---

## 🏛️ 1. Bối cảnh & Quyết định Lựa chọn Bài toán

Nhóm AI Engineer tại **Vin Smart Future** quyết định lựa chọn bài toán:  
**"Đối soát & Giải quyết khiếu nại sai lệch cước sạc xe điện giữa trụ sạc V-GREEN và App VinFast"** để thực hiện báo cáo phân tích sâu (Deep-Dive).

### ⚖️ Lý do lựa chọn và Đánh giá Đánh đổi (Trade-off Matrix):
* **Lý do chọn VinFast V-GREEN:**
  * *Tính cấp thiết & Quy mô:* Mạng lưới trụ sạc V-GREEN đang bùng nổ trên 63 tỉnh thành; sự cố ngắt sạc bất thường và trừ sai cước là điểm nghẽn nghiêm trọng nhất ảnh hưởng đến lòng tin người tiêu dùng xe điện.
  * *Dữ liệu sẵn sàng & Cấu trúc chuẩn:* Dữ liệu sự cố chuẩn giao thức OCPP từ trụ sạc và log giao dịch từ Cổng thanh toán VinFast E-Wallet đều là dữ liệu số hóa có cấu trúc (JSON/CSV), cực kỳ lý tưởng để AI xử lý tự động.
  * *Kiểm soát an toàn tuyệt đối:* Là bài toán back-office, hoàn toàn áp dụng được kiến trúc Human-in-the-loop (HITL) để khóa chặt 100% rủi ro giải ngân tài chính.
* **Lý do loại bỏ các phương án khác:**
  * *Vinhomes CSKH Ticket:* Dữ liệu hình ảnh hiện trường cư dân gửi lên có độ nhiễu cao (thiếu sáng, mờ, góc khuất) và yêu cầu tích hợp phức tạp với hệ thống camera tòa nhà.
  * *Vinmec Tóm tắt bệnh án xuất viện:* Quy chuẩn an toàn y tế quốc tế JCI và dữ liệu sức khỏe cá nhân (HIPAA) có rủi ro pháp lý cao, chu kỳ thử nghiệm lâm sàng kéo dài, chưa phù hợp cho dự án MVP 3-6 tháng.

---

## 🔄 2. Phân tích Quy trình Hiện tại (Current-State Workflow Mapping)

Quy trình thủ công hiện tại khi tiếp nhận và giải quyết một khiếu nại cước sạc pin:

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │     │ Bước 5       │
│ Tiếp nhận    │ 🔄  │ Tra cứu log  │ 🔄  │ Đối soát trừ │     │ Soạn giải    │ 🔄  │ Trưởng phòng │
│ khiếu nại    │Handoff│ OCPP trụ sạc │Handoff│ tiền E-Wallet│ ──> │ trình & đề   │Handoff│ phê duyệt &  │
│ từ App       │  1  │ V-GREEN      │  2  │ Cổng thanh   │     │ xuất hoàn tiền│  3  │ giải ngân    │
│              │     │              │     │ toán         │     │              │     │              │
│ Ai: CSKH     │     │ Ai: Tech FSE │     │ Ai: Đối soát │     │ Ai: Đối soát │     │ Ai: Manager  │
│ ⏱ 3 phút     │     │ ⏱ 10 phút 🔴 │     │ ⏱ 8 phút 🔴  │     │ ⏱ 5 phút     │     │ ⏱ 2 phút     │
│ In: Ticket   │     │ In: Trụ ID,  │     │ In: Mã GD,   │     │ In: Kết quả  │     │ In: Phiếu    │
│ Out: Info GD │     │     Timestamp│     │     Số kWh   │     │ Out: Draft   │     │ Out: Lệnh    │
│              │     │ Out: Mã lỗi  │     │ Out: Tiền    │     │      Email & │     │      hoàn    │
│              │     │      dừng sạc│     │      chênh   │     │      Phiếu đề│     │      tiền    │
│              │     │              │     │      lệch    │     │      xuất    │     │              │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘

🔴 = Bottlenecks (Bước 2 & Bước 3 chiếm 18 phút do dữ liệu rời rạc giữa 3 hệ thống và log thô khó đọc)
🔄 = Handoffs (Chuyển giao thông tin qua email/ticket nội bộ giữa CSKH ──> Kỹ thuật ──> Tài chính ──> Trưởng phòng)
⏱ Tổng thời gian vận hành trung bình: 28 phút/lượt.
```

---

## 🎯 3. Problem Statement 6-Field (Chuẩn Vin Smart Future)

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Chuyên viên Đối soát Dịch vụ Sạc pin (EV Charging Reconciliation Specialist) thuộc Khối Vận hành Hậu mãi VinFast. |
| **2. Current Workflow** | Khi khách báo lỗi sạc trừ tiền sai: CSKH nhận ticket $\rightarrow$ chuyển Chuyên viên Kỹ thuật mở OCPP Server lọc file raw log $\rightarrow$ chuyển Chuyên viên Đối soát mở Cổng thanh toán VinFast E-Wallet kiểm tra số tiền đã trừ $\rightarrow$ tính chênh lệch và soạn thảo phiếu giải trình $\rightarrow$ trình Trưởng phòng duyệt hoàn tiền. Toàn bộ qua 5 bước thủ công, 3 lần handoff, mất 28 phút/case. |
| **3. Bottleneck** | **Bước 2 & 3 (mất 18 phút):** Đọc và phân tích hàng nghìn dòng raw log kỹ thuật OCPP (nhận diện các mã lỗi `UnderVoltage`, `CableTamper`, `EVDisconnected` hay do người dùng tự ngắt) và đối chiếu thủ công với số tiền trừ trên cổng ví điện tử. |
| **4. Business Impact** | Toàn quốc phát sinh ~250 khiếu nại cước sạc/ngày. Gây lãng phí **~116 giờ làm việc/ngày** của đội ngũ vận hành. Khách hàng phải chờ 3–5 ngày làm việc để được hoàn tiền, làm sụt giảm 18% chỉ số CSAT trạm sạc VinFast. Nguy cơ bồi hoàn sai do áp lực giải tỏa hồ sơ gây thất thoát tài chính ước tính **250 – 350 triệu VND/tháng**. |
| **5. Success Metric** | 1. **Thời gian xử lý (Efficiency):** Giảm thời gian xử lý khiếu nại từ 28 phút xuống dưới 4 phút/case (giảm 85%).<br>2. **Độ chính xác (Accuracy):** Tỷ lệ phân tích đúng nguyên nhân ngắt sạc và tính đúng số tiền chênh lệch đạt $\ge$ 98%.<br>3. **SLA khách hàng:** 90% trường hợp khiếu nại hợp lệ được giải quyết và gửi thông báo hoàn tiền trong vòng 2 giờ. |
| **6. Operational Boundary** | **AI ĐƯỢC PHÉP:** Truy xuất log OCPP, parse mã lỗi sự cố, đối soát số kWh đã nạp với số tiền bị trừ, và tự động soạn thảo bản nháp phiếu giải trình lý do kỹ thuật kèm đề xuất hoàn tiền.<br>**CẤM TUYỆT ĐỐI:**<br>1. AI không được tự động kích hoạt API giải ngân hoàn tiền trực tiếp mà không có sự kiểm tra và bấm duyệt của con người (Bắt buộc Human-in-the-loop).<br>2. AI không được đề xuất hoàn tiền nếu log chứng minh lỗi xuất phát từ phía người dùng cố tình tự giật rút súng sạc (`UserManualStop`).<br>3. Mọi văn bản do AI sinh ra bắt buộc phải có tiền tố `[DRAFT_REFUND_PROPOSAL]`. |

---

## 🚀 4. Future-State Flow & Kiến Trúc AI Fit

### 💡 Lựa chọn Kiến trúc: [x] LLM Feature (Human-in-the-loop)
* **Rule-based thuần:** Thất bại trước các biến thể mã lỗi phức tạp của nhiều đời trụ sạc khác nhau và không hiểu được ngôn ngữ khiếu nại tự do của khách hàng.
* **Agentic Loop (Tự trị):** Rủi ro thất thoát ngân sách nghiêm trọng nếu agent tự động kích hoạt API giải ngân tài chính.
* **LLM Feature (HITL):** Giải pháp tối ưu tuyệt đối. AI phân tích log và soạn thảo hồ sơ giải trình trong 30 giây, chuyên viên chỉ cần đọc lướt và bấm nút phê duyệt.

### 📐 Sơ đồ Quy trình Tương lai:

```text
┌──────────────┐     ┌──────────────────────┐     ┌──────────────────────┐     ┌──────────────────────┐
│ Bước 1       │     │ Bước 2 (🔵 AI Step)   │     │ Bước 3 (🔵 AI Step)   │     │ Bước 4 (🟢 HITL)      │
│ Tiếp nhận    │     │ Script tự động kéo   │     │ Gemini phân tích log,│     │ Đối soát viên kiểm   │
│ khiếu nại    │ ──> │ log OCPP & giao dịch │ ──> │ tính kWh chênh lệch  │ ──> │ tra nhanh văn bản    │
│ trên App     │     │ ví điện tử theo mã GD│     │ & draft văn bản hoàn │     │ draft và click DUYỆT │
│              │     │                      │     │ tiền                 │     │ lệnh hoàn tiền       │
└──────────────┘     └──────────────────────┘     └──────────────────────┘     └──────────────────────┘
                                                                                          │
                                                                                          ▼
                                                                                   ↩️ Fallback:
                                                                                   Nếu log bị hỏng
                                                                                   hoặc LLM báo độ tin cậy
                                                                                   thấp (< 85%), hệ thống
                                                                                   tự chuyển ticket về
                                                                                   quy trình thủ công cũ.
```

```text
🔵 AI Step: Hệ thống tự động truy xuất log và LLM trích xuất nguyên nhân + dự thảo phiếu hoàn tiền.
🟢 HITL: Chuyên viên đối soát chỉ cần 30 giây đọc lướt và nhấn "Phê duyệt" (không phải tự mở 3 màn hình).
↩️ Fallback: Cơ chế chuyển đổi dự phòng an toàn khi dữ liệu bất định.
⏱ Tổng thời gian xử lý tương lai: ~2.5 đến 3 phút/case (so với 28 phút trước đây).
```

---

## 🏁 5. Đánh Giá Độ Sẵn Sàng & Quyết Định (Phase 5 — EVALUATE)

### 📋 AI Readiness Checklist:
1. [x] **Dữ liệu sạch & Sẵn sàng:** Hệ thống trạm sạc V-GREEN lưu trữ log chuẩn OCPP 1.6J/2.0.1 dưới dạng JSON có cấu trúc; Cổng thanh toán lưu transaction log đầy đủ timestamp và mã tham chiếu.
2. [x] **Kiểm soát rủi ro (Risk Control):** 100% quyết định chuyển tiền tài chính phải qua bước bấm duyệt của chuyên viên đối soát (HITL), loại bỏ hoàn toàn nguy cơ rò rỉ ngân sách.
3. [x] **Sự sẵn sàng của Stakeholder:** Đội ngũ CSKH và Vận hành Hậu mãi VinFast cam kết đồng hành và thử nghiệm vì đang chịu áp lực quá tải 250 case/ngày.

### 🗳️ Quyết định của Ban Giám Đốc Vin Smart Future:
**[x] GO (Bắt đầu xây dựng Prototype cho giai đoạn thử nghiệm hẹp)**

### 💼 Lý giải quyết định (Justification):
1. **Lợi tức đầu tư (ROI) vượt trội:** Tiết kiệm hơn **116 giờ lao động/ngày**, giảm chi phí vận hành hàng tháng và ngăn ngừa thất thoát do hoàn tiền sai lệch ước tính **250 – 350 triệu VND/tháng**.
2. **Kỹ thuật khả thi & An toàn cao:** Thử nghiệm Prompt Prototype trên Gemini Flash cho thấy mô hình bám sát 100% ranh giới an toàn `[DRAFT_ONLY]`, phân tích chính xác mã lỗi kỹ thuật và chống chịu xuất sắc trước các kịch bản tấn công Prompt Injection / Jailbreak.
3. **Nâng cao năng lực cạnh tranh xe điện:** Rút ngắn thời gian xử lý từ 3-5 ngày xuống dưới 2 giờ giúp tăng vọt chỉ số hài lòng khách hàng (CSAT), củng cố niềm tin vào hệ sinh thái xe điện VinFast.
