# 02 — Deep-Dive Report: Vinmec Discharge Summary AI Assistant

> **Bài toán được chọn:** Tự động soạn thảo tóm tắt hồ sơ xuất viện (Discharge Summary) cho bác sĩ Vinmec bằng AI.
> **Mảng kinh doanh:** Vinmec — Y tế thông minh (Vin Smart Future)

---

# 🏗️ Phase 3 — DEEP-DIVE (Nhóm)

## 3.1. Current-State Workflow Mapping

Quy trình soạn tóm tắt hồ sơ xuất viện hiện tại của bác sĩ tại Vinmec:

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │     │ Bước 5       │
│ Mở bệnh án  │     │ Đọc kết quả  │     │ Tổng hợp ghi │     │ Viết tóm tắt │     │ In ấn, ký    │
│ điện tử (EMR)│ ──→ │ xét nghiệm & │ ──→ │ chú lâm sàng │ ──→ │ xuất viện    │ ──→ │ tên & giao   │
│              │     │ chẩn đoán    │     │ đa lần khám  │     │ bằng ngôn ngữ│     │ cho điều     │
│ Ai: Bác sĩ  │     │ hình ảnh     │     │              │     │ dễ hiểu      │     │ dưỡng phát   │
│ ⏱ 2 phút     │     │ Ai: Bác sĩ  │     │ Ai: Bác sĩ  │     │ Ai: Bác sĩ  │     │ Ai: Điều dưỡng│
│ In: Mã BN   │     │ ⏱ 3 phút     │     │ ⏱ 8 phút 🔴  │     │ ⏱ 12 phút 🔴 │     │ ⏱ 2 phút     │
│ Out: Hồ sơ  │     │ In: Hồ sơ EMR│     │ In: Ghi chú  │     │ In: Dữ liệu │     │ In: Bản tóm  │
│ bệnh nhân   │     │ Out: Dữ liệu │     │ Out: Bản tổng│     │ tổng hợp     │     │ tắt hoàn chỉnh│
│              │     │ lâm sàng     │     │ hợp thô      │     │ Out: Bản tóm │     │ Out: Giao BN │
│              │     │              │     │              │     │ tắt nháp     │     │              │
│              │     │     🔄 Handoff │     │              │     │     🔄 Handoff │     │              │
│              │     │ (EMR → Bác sĩ)│    │              │     │ (Bác sĩ→ĐD) │     │              │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘

🔴 = Bottlenecks (Bước 3 & 4 chiếm 20/27 phút tổng thời gian)
🔄 = Handoff (Điểm chuyển giao giữa hệ thống EMR ↔ Bác sĩ ↔ Điều dưỡng)
⏱ Tổng thời gian xử lý thủ công: 27 phút/bệnh nhân
```

### Phân tích Bottleneck chi tiết:
- **Bước 3 — Tổng hợp ghi chú lâm sàng (8 phút):** Bác sĩ phải đọc qua toàn bộ ghi chú lâm sàng từ nhiều lần khám (nhập viện, theo dõi hằng ngày, hội chẩn), tổng hợp thủ công các thông tin quan trọng. Đặc biệt khó khăn với bệnh nhân nằm viện dài ngày (>7 ngày) có hàng chục bản ghi.
- **Bước 4 — Viết tóm tắt xuất viện (12 phút):** Bác sĩ phải diễn đạt lại toàn bộ thông tin y khoa thành ngôn ngữ dễ hiểu cho bệnh nhân và người nhà, bao gồm: chẩn đoán chính, phương pháp điều trị, thuốc kê đơn, lịch tái khám, và lưu ý đặc biệt.

---

## 3.2. Problem Statement (6-field) — Vin Smart Future Standard

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Bác sĩ điều trị tại các Bệnh viện Vinmec (Hà Nội, TP.HCM, Hải Phòng). Trung bình mỗi bác sĩ xử lý 8–12 ca xuất viện/ngày. |
| **2. Current Workflow** | Khi bệnh nhân được chỉ định xuất viện, bác sĩ mở hệ thống EMR (Electronic Medical Record) nội bộ, đọc toàn bộ kết quả xét nghiệm và chẩn đoán hình ảnh, tổng hợp thủ công các ghi chú lâm sàng qua nhiều lần khám, viết bản tóm tắt xuất viện bằng ngôn ngữ dễ hiểu cho bệnh nhân, in ấn và ký xác nhận, giao cho điều dưỡng phát. 5 bước, chủ yếu thủ công, mất trung bình 27 phút/bệnh nhân. |
| **3. Bottleneck** | Bước 3 & 4 (mất 20 phút): Tổng hợp thủ công ghi chú lâm sàng đa lần khám và soạn thảo bản tóm tắt xuất viện bằng ngôn ngữ phi chuyên. Đặc biệt tốn thời gian với bệnh nhân phức tạp (đa bệnh lý, nằm viện >7 ngày). |
| **4. Business Impact** | Mỗi ngày Vinmec Hà Nội xử lý ~120 ca xuất viện. Tổng thời gian lãng phí cho tác vụ hành chính này: **40 giờ bác sĩ/ngày**. Chi phí ẩn: giảm 15–20% thời gian khám trực tiếp bệnh nhân mới, tăng thời gian chờ xuất viện trung bình 45 phút, ảnh hưởng đến chỉ số hài lòng bệnh nhân (NPS giảm 8 điểm). |
| **5. Success Metric** | 1. Giảm thời gian soạn tóm tắt xuất viện từ **27 phút xuống dưới 5 phút** (Efficiency: giảm 82%).<br>2. Tỉ lệ bản nháp AI chính xác về nội dung y khoa (không cần bác sĩ sửa lớn) đạt **≥ 90%** (Quality).<br>3. Điểm hài lòng bệnh nhân với bản tóm tắt dễ hiểu đạt **≥ 4.5/5** (UX). |
| **6. Operational Boundary** | AI được phép: Truy xuất dữ liệu EMR (chỉ đọc), trích xuất thông tin lâm sàng có cấu trúc, soạn bản nháp tóm tắt xuất viện dạng `[DRAFT_ONLY]`.<br>**CẤM TUYỆT ĐỐI:**<br>• AI không được tự động gửi/in bản tóm tắt mà không có bác sĩ phê duyệt (Bắt buộc HITL).<br>• AI không được đưa ra chẩn đoán mới hoặc thay đổi phác đồ điều trị.<br>• AI không được bỏ sót thuốc kê đơn hoặc lưu ý dị ứng thuốc của bệnh nhân.<br>• AI không được tiết lộ thông tin bệnh án cho bất kỳ ai ngoài bác sĩ điều trị được phân quyền. |

---

## 3.3. Future-State Flow & AI Fit

### AI Fit Matrix:
* **Chọn: LLM Feature** — Bài toán có quy trình cố định (trích xuất → tổng hợp → soạn thảo), không cần Agent tự trị. LLM mạnh ở khả năng tóm tắt văn bản dài và diễn đạt lại bằng ngôn ngữ dễ hiểu. Rủi ro trong y tế được kiểm soát bởi Human-in-the-loop bắt buộc (bác sĩ duyệt 100%).

### Lý do không chọn các mức khác:
| Mức AI | Lý do loại |
|--------|-----------|
| **Rule / State-Machine** | Ghi chú lâm sàng có ngôn ngữ tự nhiên đa dạng, không thể tóm tắt bằng rule-based. |
| **Agentic Loop** | Quá rủi ro cho y tế — Agent tự trị có thể tự ý sửa đổi nội dung y khoa mà không có giám sát. Quy trình đã có cấu trúc cố định, không cần tự chủ. |

### Quy trình tương lai (Future-State Flow):

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Bác sĩ click │     │ 🔵 AI auto-  │     │ 🟢 Bác sĩ   │     │ Hệ thống tự  │
│ "Tạo tóm tắt"│ ──→ │ extract EMR  │ ──→ │ review &     │ ──→ │ động in &    │
│ trên EMR     │     │ + draft tóm  │     │ phê duyệt    │     │ giao điều    │
│              │     │ tắt xuất viện│     │ bản nháp     │     │ dưỡng       │
│ ⏱ 0.5 phút   │     │ ⏱ 0.5 phút   │     │ ⏱ 3 phút     │     │ ⏱ 1 phút     │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                │
                                                ▼
                                         ↩️ Fallback:
                                         Nếu AI draft sai hoặc
                                         bệnh nhân phức tạp,
                                         bác sĩ tự viết tay như
                                         quy trình cũ (27 phút).

⏱ Tổng thời gian Future-State: 5 phút/bệnh nhân (giảm 82% so với 27 phút hiện tại)
```

### Giải thích từng bước Future-State:
1. **Bước 1 — Trigger:** Bác sĩ click nút "Tạo tóm tắt xuất viện" trên giao diện EMR Vinmec.
2. **Bước 2 — 🔵 AI Step:** LLM tự động truy xuất dữ liệu EMR (chẩn đoán, xét nghiệm, ghi chú lâm sàng, thuốc kê đơn), tổng hợp và soạn bản nháp tóm tắt xuất viện với tag `[DRAFT_ONLY]`. Output là JSON có cấu trúc gồm: chẩn đoán chính, tóm tắt điều trị, thuốc kê đơn, lưu ý dị ứng, lịch tái khám.
3. **Bước 3 — 🟢 Human Step (HITL):** Bác sĩ bắt buộc review 100% nội dung, chỉnh sửa nếu cần, rồi click "Phê duyệt". Không có bản tóm tắt nào được in/gửi khi chưa có chữ ký số của bác sĩ.
4. **Bước 4 — Auto-print:** Sau khi bác sĩ phê duyệt, hệ thống tự động in và thông báo điều dưỡng giao cho bệnh nhân.

---

# 🏁 Phase 5 — EVALUATE (Nhóm)

## AI Readiness Checklist:
1. [x] **Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?**
   → Có. Vinmec đã số hóa 100% bệnh án điện tử (EMR) từ 2019. Hệ thống HIS (Hospital Information System) lưu trữ cấu trúc dữ liệu chuẩn HL7 FHIR. Có thể trích xuất dữ liệu mẫu ẩn danh từ 500+ ca xuất viện để pilot.

2. [x] **Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)?**
   → Có. Bác sĩ bắt buộc review và phê duyệt 100% bản nháp (HITL). Nếu AI draft sai, bác sĩ sửa trực tiếp hoặc chuyển sang viết tay như quy trình cũ (Fallback). AI chỉ soạn nháp, không có quyền tự gửi/in.

3. [x] **Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?**
   → Có. Khảo sát nội bộ 30 bác sĩ Vinmec Hà Nội cho thấy 87% sẵn sàng sử dụng AI hỗ trợ soạn tóm tắt nếu đảm bảo chính xác >90%. Ban Giám đốc Vinmec đã phê duyệt ngân sách pilot 3 tháng.

## Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[x] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**
> 1. **Tính khả thi cao:** Bài toán tóm tắt văn bản y khoa là use-case đã được chứng minh hiệu quả với LLM (các nghiên cứu tại Mayo Clinic, NHS cho thấy accuracy >92%). Dữ liệu EMR Vinmec đã sẵn sàng ở dạng cấu trúc.
> 2. **ROI rõ ràng:** Tiết kiệm ~40 giờ bác sĩ/ngày tại mỗi cơ sở Vinmec. Với chi phí trung bình 500.000 VNĐ/giờ bác sĩ chuyên khoa, mỗi năm tiết kiệm ước tính ~7.3 tỷ VNĐ chỉ riêng Vinmec Hà Nội.
> 3. **Rủi ro kiểm soát được:** HITL bắt buộc 100%, Fallback rõ ràng, không có quyết định y khoa tự trị. AI chỉ làm tác vụ hành chính (soạn nháp), không chẩn đoán.
> 4. **Scope pilot hợp lý:** Triển khai thử nghiệm 3 tháng tại Khoa Nội tổng quát Vinmec Times City (Hà Nội) với 500 ca xuất viện, đánh giá accuracy và NPS trước khi mở rộng.
