# 01 — Problem Scan & Quick Cards (Vin Smart Future)

> **Mảng kinh doanh khảo sát chính:** Vinmec (Y tế thông minh) kết hợp khảo sát đa mảng Vingroup.

---

# 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội (Cá nhân)

Sử dụng **4 Lenses** quét qua hoạt động vận hành của các công ty thành viên Vingroup. Ghi lại **ít nhất 5 bài toán/bottleneck** thực tế.

### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | **Vinmec** | Tốn thời gian | Bác sĩ mất 20–30 phút/bệnh nhân để soạn tóm tắt hồ sơ xuất viện (Discharge Summary) thủ công từ bệnh án điện tử, kết quả xét nghiệm và ghi chú lâm sàng. |
| 2 | **Vinmec** | Pain từ người khác | Bệnh nhân phàn nàn vì phải chờ đợi lâu để nhận giấy xuất viện; bác sĩ bị quá tải hành chính, giảm thời gian khám trực tiếp. |
| 3 | **Vinhomes** | Lặp lại | Nhân viên ban quản lý tòa nhà phân loại thủ công hàng trăm phản ánh cư dân mỗi ngày (mất nước, hỏng đèn, ồn ào) trên App Vinhomes Resident để điều hướng đến đúng bộ phận xử lý. |
| 4 | **VinFast** | AI có thể tốt hơn | Khách hàng mô tả lỗi xe bằng tiếng Việt tự nhiên (ví dụ: "xe qua gờ giảm tốc kêu cụp cụp"), nhân viên CSKH phải tra cứu thủ công để ánh xạ sang mã lỗi kỹ thuật — chậm và hay sai chuyên khoa. |
| 5 | **Xanh SM** | Tốn thời gian | Điều phối viên mất 12–15 phút/lượt xử lý thủ công sự cố tài xế báo hết pin giữa đường: tra cứu vị trí GPS, tìm trạm sạc trống, soạn tin nhắn chỉ dẫn. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Cá nhân)

Chọn **top 3 bài toán** từ danh sách trên: **#1 (Vinmec Discharge Summary), #3 (Vinhomes CSKH), #4 (VinFast Chẩn đoán lỗi xe).**

---

## Card #1 — Vinmec: Soạn thảo tóm tắt hồ sơ xuất viện (Discharge Summary)

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Bác sĩ Vinmec mất quá nhiều thời gian soạn thảo  │
│ tóm tắt hồ sơ xuất viện thủ công cho từng bệnh nhân.        │
│ Công ty thành viên: [x] Vinmec                              │
│                                                             │
│ Ai đang đau? Bác sĩ điều trị (quá tải hành chính),          │
│ Bệnh nhân (chờ đợi lâu), Phòng Hành chính Y tế (tồn đọng)  │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Bác sĩ mở bệnh án điện tử (EMR) tra cứu lịch sử      │
│   → 2. Đọc toàn bộ kết quả xét nghiệm, chẩn đoán hình ảnh │
│   → 3. Tổng hợp thủ công các ghi chú lâm sàng qua nhiều    │
│        lần khám                                              │
│   → 4. Viết tay bản tóm tắt xuất viện bằng ngôn ngữ dễ hiểu│
│        cho bệnh nhân                                         │
│   → 5. In ấn, ký tên, giao cho điều dưỡng phát cho bệnh nhân│
│                                                             │
│ Bước nào tốn nhất? Bước 3-4 (⏱ 20 phút/lượt)               │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4             │
│ (Tự động trích xuất dữ liệu EMR → Soạn draft tóm tắt)      │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian soạn tóm tắt từ 25 phút ──> dưới 5 phút.    │
│ Tỉ lệ draft chính xác (không cần sửa lớn) đạt ≥ 90%.       │
│                                                             │
│ Quick Architecture: [x] LLM Feature (Auto-draft tóm tắt)    │
└─────────────────────────────────────────────────────────────┘
```

---

## Card #2 — Vinhomes: Phân loại & Điều hướng phản ánh cư dân

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Phân loại tự động các phản ánh/khiếu nại cư dân  │
│ gửi qua App Vinhomes Resident để điều hướng đến đúng bộ phận│
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau? Nhân viên CSKH ban quản lý (xử lý thủ công),  │
│ Cư dân (chờ phản hồi 12 tiếng)                              │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Cư dân gửi phản ánh qua App (text tự do)               │
│   → 2. Nhân viên CSKH đọc từng phản ánh, phân loại thủ công│
│   → 3. Chuyển tiếp đến đúng ban kỹ thuật/bảo vệ/vệ sinh    │
│   → 4. Soạn phản hồi xác nhận gửi lại cư dân               │
│                                                             │
│ Bước nào tốn nhất? Bước 2-3 (⏱ 8 phút/lượt)                │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3             │
│ (Auto-classify → Auto-route đến đúng đội)                    │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian phản hồi từ 12 giờ ──> dưới 1 giờ.           │
│ Tỉ lệ phân loại đúng đạt ≥ 95%.                             │
│                                                             │
│ Quick Architecture: [x] LLM Feature (Text classification)   │
└─────────────────────────────────────────────────────────────┘
```

---

## Card #3 — VinFast: Chẩn đoán lỗi xe từ mô tả tiếng Việt

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Khách hàng VinFast mô tả triệu chứng lỗi xe     │
│ bằng tiếng Việt tự nhiên, hệ thống cần ánh xạ sang mã lỗi  │
│ kỹ thuật để điều hướng đúng chuyên khoa sửa chữa.           │
│ Công ty thành viên: [x] VinFast                             │
│                                                             │
│ Ai đang đau? Nhân viên CSKH (tra cứu thủ công, hay sai),   │
│ Khách hàng (chờ lâu, bị chuyển sai bộ phận)                 │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Khách gọi hotline mô tả triệu chứng bằng tiếng Việt   │
│   → 2. Nhân viên CSKH nghe và ghi chú thủ công              │
│   → 3. Tra cứu bảng mã lỗi kỹ thuật để ánh xạ              │
│   → 4. Chuyển ticket đến đúng xưởng/chuyên khoa sửa chữa   │
│                                                             │
│ Bước nào tốn nhất? Bước 2-3 (⏱ 10 phút/lượt)               │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3             │
│ (NLP tiếng Việt → Auto-map mã lỗi kỹ thuật)                 │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian phân loại từ 10 phút ──> dưới 1 phút.        │
│ Tỉ lệ ánh xạ đúng mã lỗi đạt ≥ 88%.                        │
│                                                             │
│ Quick Architecture: [x] LLM Feature (NLP classification)    │
└─────────────────────────────────────────────────────────────┘
```

---

## Quyết định lựa chọn của nhóm:
Nhóm quyết định chọn bài toán **"Card #1 — Vinmec: Soạn thảo tóm tắt hồ sơ xuất viện (Discharge Summary)"** để thực hiện Deep-Dive.

### Lý do lựa chọn và loại bỏ các thẻ khác:
* **Card #2 (Vinhomes CSKH):** Bài toán text classification có thể giải quyết hiệu quả hơn bằng rule-based router kết hợp keyword matching. Không cần LLM nặng, tỉ lệ lỗi phân loại có thể gây ảnh hưởng đến uy tín ban quản lý nếu chuyển sai bộ phận.
* **Card #3 (VinFast Chẩn đoán lỗi):** Mặc dù hấp dẫn nhưng dữ liệu mô tả lỗi xe bằng tiếng Việt rất đa dạng và thiếu corpus huấn luyện chuẩn. Rủi ro chẩn đoán sai có thể dẫn đến sửa chữa sai bộ phận, gây nguy hiểm an toàn xe. Cần pilot nhỏ trước.
