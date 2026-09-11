# 🔍 01 — Problem Scan & Quick Problem Cards (Vin Smart Future)

> **Nhóm thực hiện:** AI Product Engineering Team (Vin Smart Future)  
> **Đơn vị:** Vin Smart Future — Tập đoàn Vingroup  
> **Phạm vi bài toán:** Quét các điểm nghẽn vận hành trên toàn bộ các công ty thành viên (VinFast, Xanh SM, Vinhomes, Vinmec).  

---

# 🔍 Phase 1 — SCAN (Tìm kiếm cơ hội)

Sử dụng **4 Lenses** (Lặp lại, Tốn thời gian, AI-upgrade, Pain từ người khác) để quét qua toàn bộ hệ sinh thái vận hành của Vingroup.

### 📝 Bảng tổng hợp 5 bài toán vận hành thực tế:

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | **VinFast** | **Lặp lại** | Đối soát & phân loại khiếu nại sai lệch cước sạc xe điện giữa trụ sạc V-GREEN và App VinFast.|
| 2 | **VinFast** | **Tốn thời gian** | Triage & tóm tắt log chẩn đoán lỗi ECU/CAN-bus từ xa trước khi tiếp nhận xe vào Xưởng dịch vụ. |
| 3 | **Xanh SM** | **AI-upgrade** | Tự động phân loại, trích xuất thực thể và định tuyến khiếu nại 1-3 sao từ khách hàng về đội xe thực địa. |
| 4 | **Vinhomes** | **Stakeholder Pain** | Tự động phân loại và route ticket phản ánh cư dân trên Vinhomes Resident App đến đúng tổ vận hành (Kỹ thuật/An ninh/Vệ sinh). |
| 5 | **Vinmec** | **Tốn thời gian** | Tự động dự thảo biên bản Tóm tắt hồ sơ bệnh án xuất viện (Discharge Summary) song ngữ từ hệ thống HIS/EMR. |

---

# 🃏 Phase 2 — QUICK-ASSESS (Hoàn thiện 3 Quick Problem Cards)

Chọn **Top 3 bài toán** tiềm năng nhất để hoàn thiện thẻ phân tích nhanh:
* **Card #1:** **VinFast** — Đối soát & giải quyết khiếu nại cước sạc xe điện V-GREEN.
* **Card #4:** **Vinhomes** — Tự động phân loại, gán nhãn và điều phối phản ánh cư dân trên App Resident.
* **Card #5:** **Vinmec** — Dự thảo biên bản Tóm tắt bệnh án xuất viện (Discharge Summary) song ngữ từ EMR.

---

### 🎴 THẺ BÀI TOÁN 1: VINFAST (Đối soát cước trạm sạc V-GREEN)

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Chủ xe điện VinFast khiếu nại bị trừ tiền │
│ ví nhưng trụ sạc ngắt giữa chừng, cần đối soát log trạm     │
│ V-GREEN và tự động tạo hồ sơ đề xuất hoàn tiền.             │
│ Công ty thành viên: [x] VinFast                             │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│ - Chủ xe (bực mình vì mất tiền mà xe chưa đủ pin)           │
│ - Chuyên viên đối soát tài chính cước sạc (quá tải kiểm tra)│
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Tiếp nhận ticket khiếu nại & mã giao dịch từ App       │
│   ──> 2. Tra cứu thủ công log OCPP trụ sạc tìm lỗi ngắt     │
│   ──> 3. Tra cứu lịch sử trừ tiền cổng thanh toán E-Wallet   │
│   ──> 4. Tính chênh lệch, viết giải trình & tạo lệnh hoàn    │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 18 phút/lượt)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3 & 4         │
│ (AI parse log sự cố OCPP -> tính toán kWh chưa nhận ->      │
│  draft phiếu đề xuất hoàn tiền kèm lý do kỹ thuật)          │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ - Giảm thời gian xử lý khiếu nại: 25 min ──> dưới 4 min/case│
│ - Độ chính xác phát hiện nguyên nhân lỗi ngắt sạc: >= 98%    │
│                                                             │
│ Quick Architecture: [x] LLM Feature (kết hợp Rule parse log)│
└─────────────────────────────────────────────────────────────┘
```

---

### 🎴 THẺ BÀI TOÁN 2: VINHOMES (Điều phối phản ánh cư dân App Resident)

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #4                                       │
│                                                             │
│ Bài toán (1 câu): Phân loại, xác định mức khẩn cấp và      │
│ tự động điều phối phản ánh (kèm ảnh) của cư dân trên App    │
│ Vinhomes Resident đến đúng tổ vận hành (Kỹ thuật/An ninh).  │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│ - Cư dân (chờ giải quyết lâu, bức xúc khi ticket bị ngâm)   │
│ - Điều phối viên Ban Quản lý (ngợp trong hàng ngàn ticket)  │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Đọc văn bản mô tả sự cố + xem ảnh cư dân chụp         │
│   ──> 2. Nhận định phòng ban tiếp nhận & mức độ ưu tiên     │
│   ──> 3. Gán thẻ tag và điều phối nhân sự trực ca BMS       │
│   ──> 4. Soạn thông báo tiếp nhận & hẹn giờ xử lý (ETA)     │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 1 & 2 (⏱ 12 phút/lượt)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1, 2 & 4         │
│ (LLM hiểu ngữ cảnh văn bản tự do + phân loại ảnh -> gán     │
│  đúng tổ vận hành và tự động draft tin nhắn phản hồi cư dân)│
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ - Giảm thời gian định tuyến ticket: 15 min ──> dưới 30 giây │
│ - Tỉ lệ định tuyến chính xác bộ phận: >= 92%                │
│ - Giảm tỷ lệ ticket tồn đọng qua đêm từ 25% ──> dưới 3%     │
│                                                             │
│ Quick Architecture: [x] LLM Feature                         │
└─────────────────────────────────────────────────────────────┘
```

---

### 🎴 THẺ BÀI TOÁN 3: VINMEC (Tóm tắt hồ sơ bệnh án xuất viện EMR)

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #5                                       │
│                                                             │
│ Bài toán (1 câu): Bác sĩ mất nhiều thời gian rà soát EMR    │
│ để viết tay bản Tóm tắt bệnh án xuất viện (Discharge        │
│ Summary) song ngữ Việt - Anh cho bệnh nhân và bảo hiểm.     │
│ Công ty thành viên: [x] Vinmec                              │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│ - Bác sĩ điều trị (quá tải bàn giấy, giảm thời gian khám)   │
│ - Bệnh nhân / Bảo hiểm tư nhân (chờ 2-4 tiếng làm thủ tục)  │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Mở EMR đọc nhật ký diễn tiến, xét nghiệm, CĐHA đợt nằm │
│   ──> 2. Soạn đoạn văn tóm tắt lâm sàng theo chuẩn JCI      │
│   ──> 3. Dịch sang tiếng Anh cho bảo hiểm/khách quốc tế     │
│   ──> 4. Kê đơn thuốc xuất viện, dặn dò tái khám & ký duyệt │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 30 phút/lượt)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3            │
│ (LLM tổng hợp dữ liệu xét nghiệm + diễn tiến bệnh trong EMR │
│  thành văn bản tóm tắt song ngữ chuẩn mực để bác sĩ review) │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ - Giảm thời gian bác sĩ soạn tóm tắt: 35 min ──> dưới 5 min │
│ - Tiết kiệm ~60 giờ làm việc chuyên môn/ngày cho mỗi BV     │
│                                                             │
│ Quick Architecture: [x] LLM Feature (Bắt buộc HITL duyệt)   │
└─────────────────────────────────────────────────────────────┘
```
