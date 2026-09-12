# 01-problem-scan.md — Problem Scan & Quick Problem Cards (Vin Smart Future)

**Học viên thực hiện:** Văn Ước
**Đơn vị:** Vin Smart Future (Vingroup)  
**Mảng trọng tâm:** Di chuyển xanh & Vận hành thông minh (GSM / Xanh SM & VinFast)

---

## 🏛️ Bối cảnh & Vai trò

Tôi là **Văn Ước**, AI Product Engineer tại **Vin Smart Future**. Nhiệm vụ của tôi trong bài lab hôm nay là ứng dụng công nghệ AI để giải quyết các nút thắt cổ chai vận hành (operational bottlenecks) tại các công ty thành viên thuộc Tập đoàn Vingroup, đặc biệt là nâng cao hiệu suất điều vận đội xe điện thông minh cho **Xanh SM (GSM)** và hạ tầng xe điện **VinFast**.

---

# 🔍 Phase 1 — SCAN: Quét cơ hội bằng 4 Lenses

Bằng cách áp dụng **4 Lenses** (*Lặp lại, Tốn thời gian, AI-upgrade, Pain từ stakeholder*), tôi đã xác định được 5 bài toán vận hành tiêu biểu trong hệ sinh thái Vingroup:

| # | Đơn vị thành viên | Lens áp dụng | Mô tả bài toán vận hành & Bottleneck hiện tại | Tác động kinh doanh ước tính |
|---|-------------------|--------------|-----------------------------------------------|------------------------------|
| **1** | **Xanh SM (GSM)** | **Tốn thời gian** | **Điều phối trạm sạc & Cứu hộ pin khẩn cấp:** Tài xế gọi tổng đài khi xe gần hết pin hoặc gặp sự cố. Điều phối viên phải tra cứu thủ công vị trí GPS, kiểm tra dashboard trụ sạc VinFast còn trống và tự soạn tin nhắn chỉ dẫn, tốn 12-15 phút/lượt. | Gây ùn tắc điều vận, nguy cơ xe chết máy giữa đường gây trễ chuyến đón khách và tăng chi phí cẩu xe. |
| **2** | **Vinhomes** | **Lặp lại** | **Phân loại & Định tuyến phản ánh cư dân:** Nhân viên CSKH phải đọc thủ công hàng nghìn ý kiến, khiếu nại (hỏng đèn sảnh, mất nước, tiếng ồn) trên App Vinhomes Resident để gán nhãn và chuyển tiếp về đúng ban quản lý từng tòa nhà. | Thời gian phản hồi chậm (từ 4 - 8 tiếng), lãng phí nhân lực làm việc văn phòng lặp đi lặp lại. |
| **3** | **VinFast** | **AI-upgrade** | **Chẩn đoán ban đầu lỗi xe từ mô tả tiếng Việt:** Khách hàng mang xe đến xưởng bảo dưỡng mô tả lỗi bằng ngôn ngữ tự nhiên (ví dụ: *"đi qua gờ giảm tốc bánh trước lục cục"*). Cố vấn dịch vụ phải tự tra cứu tài liệu kỹ thuật dài hàng trăm trang. | Kéo dài thời gian tiếp nhận xe tại xưởng từ 20 phút xuống còn dưới 5 phút nếu có AI gợi ý mã lỗi. |
| **4** | **Vinmec** | **Tốn thời gian** | **Soạn thảo tóm tắt hồ sơ xuất viện (Discharge Summary):** Bác sĩ mất nhiều thời gian đọc lại bệnh án điện tử, kết quả xét nghiệm để tóm tắt thành văn bản ngôn ngữ phổ thông cho bệnh nhân khi xuất viện (mất 20-30 phút/ca). | Bác sĩ quá tải việc hành chính giấy tờ, giảm thời gian khám và tư vấn trực tiếp cho bệnh nhân. |
| **5** | **VinFast** | **Lặp lại** | **Đối chiếu & So khớp dữ liệu sạc điện đối tác:** Kế toán phải kiểm tra, đối chiếu số liệu sạc điện hàng tuần giữa hàng ngàn trụ sạc nhượng quyền bên thứ ba với hệ thống thanh toán trung tâm VinFast. | Sai lệch dữ liệu phát hiện chậm, mất 2-3 ngày làm việc đối soát định kỳ hàng tuần. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Từ danh sách trên, tôi chọn **Top 3 bài toán tiềm năng nhất** để phân tích nhanh:
1. **Card #1:** Xanh SM — Trợ lý điều phối trạm sạc và kích hoạt xe sạc cứu hộ *(Bài toán lựa chọn làm Deep-Dive & Code Prototype)*.
2. **Card #2:** Vinhomes — Phân loại & Định tuyến khiếu nại cư dân thông minh.
3. **Card #3:** VinFast — Trợ lý tiếp nhận chẩn đoán mô tả lỗi kỹ thuật xe điện.

---

### 🎴 QUICK PROBLEM CARD #1 (Lựa chọn chính)

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                   │
│                                                                         │
│ Bài toán (1 câu): Hỗ trợ điều phối viên Xanh SM tra cứu trạm sạc        │
│ VinFast và tự động kích hoạt xe sạc cứu hộ khi pin EV dưới 5%.          │
│                                                                         │
│ Công ty thành viên: [x] Xanh SM (GSM)   [x] VinFast                     │
│                                                                         │
│ Ai đang đau (Actor)?                                                    │
│ - Điều phối viên (Dispatcher): Quá tải vào giờ cao điểm, áp lực xử lý.  │
│ - Tài xế Xanh SM: Lo lắng hết pin chết máy giữa đường, trễ chuyến.      │
│                                                                         │
│ Workflow thủ công hiện tại (5 bước):                                    │
│   1. Nhận cuộc gọi/tin nhắn báo nguy cơ hết pin từ tài xế                │
│   ──> 2. Tra cứu thủ công toạ độ GPS của xe trên phần mềm định vị       │
│   ──> 3. Mở hệ thống bản đồ trạm sạc VinFast kiểm tra trụ khả dụng      │
│   ──> 4. Soạn thảo tin nhắn hướng dẫn đường đi gửi cho tài xế           │
│   ──> 5. Gọi điện thoại điều xe sạc lưu động nếu pin kiệt (< 5%)        │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất?                                         │
│   Bước 3 và 4 (⏱ 10 - 12 phút/lượt) do phải so khớp bản đồ và soạn tin. │
│                                                                         │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                                   │
│   Bước 3, 4 và 5: Nhận diện dữ liệu telemetry (vị trí GPS, % pin)       │
│   để tự động tìm trạm tối ưu và draft sẵn tin nhắn hoặc kích hoạt cứu hộ.│
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                    │
│   - Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 2.5 phút/lượt.        │
│   - 100% cuốc xe pin < 5% được điều xe cứu hộ ngay lập tức, không để xe │
│     cạn pin giữa đường gây tắc nghẽn giao thông.                        │
│                                                                         │
│ Quick Architecture:                                                     │
│   [ ] No AI    [ ] Rule-only    [x] LLM Co-pilot    [ ] Full Agent      │
│   (Sử dụng LLM kết hợp Human-in-the-loop: Bắt buộc gắn tag [DRAFT_ONLY] │
│    để điều phối viên xác nhận trước khi phát lệnh).                     │
└─────────────────────────────────────────────────────────────────────────┘
```

---

### 🎴 QUICK PROBLEM CARD #2

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                   │
│                                                                         │
│ Bài toán (1 câu): Tự động phân loại và chuyển tiếp phản ánh của         │
│ cư dân Vinhomes về đúng bộ phận xử lý chuyên môn.                       │
│                                                                         │
│ Công ty thành viên: [x] Vinhomes                                        │
│                                                                         │
│ Ai đang đau (Actor)?                                                    │
│ - Nhân viên CSKH Ban Quản Lý: Mất hàng giờ đọc và gắn tag thủ công.     │
│ - Cư dân: Bức xúc vì phản ánh mãi không thấy nhân viên kỹ thuật tới.    │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Nhận ticket phản ánh của cư dân trên App Vinhomes Resident         │
│   ──> 2. CSKH mở ticket, đọc nội dung và phân loại hạng mục (Điện/Nước) │
│   ──> 3. Tra cứu danh sách kỹ thuật viên đang trực tại tòa nhà đó        │
│   ──> 4. Chuyển tiếp ticket và gửi thông báo xác nhận cho cư dân        │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất?                                         │
│   Bước 2 và 3 (⏱ 5 - 8 phút/ticket, dồn ứ hàng trăm ticket/ngày).       │
│                                                                         │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                                   │
│   Bước 2: Phân tích ngôn ngữ tự nhiên (NLP) để gắn nhãn danh mục, mức   │
│   độ khẩn cấp và đề xuất phân luồng tự động cho bộ phận kỹ thuật.       │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                    │
│   - Rút ngắn thời gian chuyển giao ticket từ 4 tiếng ──> dưới 1 phút.    │
│   - Độ chính xác phân loại danh mục đạt trên 92%.                       │
│                                                                         │
│ Quick Architecture:                                                     │
│   [ ] No AI    [x] Rule + LLM Classification    [ ] Full Agent          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

### 🎴 QUICK PROBLEM CARD #3

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                   │
│                                                                         │
│ Bài toán (1 câu): Trợ lý AI hỗ trợ cố vấn dịch vụ VinFast trích xuất    │
│ triệu chứng xe điện từ lời kể của khách để gợi ý mã kiểm tra sơ bộ.     │
│                                                                         │
│ Công ty thành viên: [x] VinFast                                         │
│                                                                         │
│ Ai đang đau (Actor)?                                                    │
│ - Cố vấn dịch vụ xưởng (Service Advisor): Áp lực giải thích cho khách.  │
│ - Kỹ thuật viên: Nhận phiếu sửa chữa ghi mô tả chung chung, khó tái lập │
│   lỗi trên xe điện VF e34 / VF8.                                        │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Khách hàng tới xưởng, nói miệng về hiện tượng lỗi xe               │
│   ──> 2. Cố vấn dịch vụ ghi chép chắp vá vào phiếu tiếp nhận            │
│   ──> 3. Cố vấn dịch vụ tra cứu sổ tay triệu chứng kỹ thuật             │
│   ──> 4. Bàn giao xe và phiếu tiếp nhận cho kỹ thuật viên chẩn đoán     │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất?                                         │
│   Bước 2 và 3 (⏱ 10 - 15 phút), dễ ghi thiếu dữ kiện môi trường xảy ra  │
│   lỗi (tốc độ xe, nhiệt độ pin, chế độ lái).                            │
│                                                                         │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                                   │
│   Bước 2 và 3: Hỏi khách theo mẫu thông minh có cấu trúc, chuẩn hóa     │
│   ngôn ngữ tự nhiên thành danh sách mã kiểm tra tiêu chuẩn OBD-II/EV.    │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                    │
│   - Giảm thời gian lập phiếu tiếp nhận từ 15 phút ──> dưới 4 phút.       │
│   - Tăng độ đầy đủ của mô tả kỹ thuật bàn giao lên 95%.                 │
│                                                                         │
│ Quick Architecture:                                                     │
│   [ ] No AI    [ ] Rule    [x] LLM Feature (Structured Output)          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 🎯 Quyết định chọn bài toán làm Deep-Dive & Lập trình Prototype:

Nhóm và tôi thống nhất chọn **Quick Problem Card #1: Trợ lý điều phối trạm sạc & Kích hoạt xe sạc cứu hộ khẩn cấp cho Xanh SM** vì:
1. **Tính cấp thiết vận hành thực tế:** Xe taxi điện hết pin giữa đường gây tắc đường, tài xế không có doanh thu và làm giảm chỉ số hài lòng khách hàng của Xanh SM.
2. **Ranh giới an toàn rõ ràng (Safety Operational Boundaries):**
   - Không để AI tự ý gửi chỉ đạo ra xe mà phải có tag `[DRAFT_ONLY]` để con người duyệt (Human-in-the-loop).
   - Ranh giới pin sống còn (< 5%): Cấm tuyệt đối chỉ định trạm sạc xa > 5km, bắt buộc kích hoạt giao thức xe sạc pin lưu động (`dispatch_mobile_charger`).
3. **Hoàn toàn khả thi để lập trình bản mẫu Prompt Prototype trên Gemini 2.5 Flash.**
