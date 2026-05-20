# Phân tích yêu cầu — vai Consumer

- Cặp đàm phán: Pair 01 — Camera Stream ↔ AI Vision
- Product: Smart Campus Operations Platform
- Consumer service: Camera Stream
- Provider service: AI Vision
- Người viết: Lưu Mạnh Đạt
- Ngày: 20/05/2026

---

## 1. Resource Consumer cần nhận/gửi

| Resource | Consumer dùng để làm gì? | Field bắt buộc với Consumer | Field có thể tùy chọn |
|---|---|---|---|
| DetectionRequest | Gửi frame ảnh sang AI để detect | cameraId, imageUrl | description |
| DetectionResult | Nhận kết quả detect từ AI | detectionId, detectionType, confidence | plateNumber, personName |
| Alert | Hiển thị cảnh báo trên dashboard | alertId, severity, timestamp | note |

---

## 2. API Consumer cần gọi

| Method | Path | Lúc nào gọi? | Kỳ vọng response |
|---|---|---|---|
| POST | `/detections` | Khi camera phát hiện motion | Trả về detectionId và status |
| GET | `/detections/{id}` | Khi cần kiểm tra kết quả detect | Trả về DetectionResult |
| GET | `/detections/recent` | Khi hiển thị lịch sử detect | Danh sách detect gần nhất |
| GET | `/health` | Monitoring định kỳ | HTTP 200 và trạng thái service |

---

## 3. Error case Consumer cần xử lý

Tối thiểu 5 case.

| Status | Consumer hiểu là gì? | Consumer sẽ xử lý thế nào? |
|---:|---|---|
| 400 | Request sai schema | Sửa payload hoặc log lỗi |
| 401 | Thiếu hoặc hết hạn token | Refresh token hoặc cấu hình lại |
| 403 | Không đủ quyền truy cập API | Hiển thị lỗi quyền truy cập |
| 404 | Detection ID không tồn tại | Hiển thị trạng thái không tồn tại |
| 409 | Duplicate detection request | Retry hoặc bỏ request trùng |
| 422 | imageUrl hoặc payload không hợp lệ | Hiển thị lý do validation |
| 500 | AI service xử lý lỗi | Retry và ghi log hệ thống |
| 503 | AI service unavailable | Chuyển sang degraded mode |

---

## 4. Giả định bổ sung

- Giả định 1: AI Vision phản hồi dưới 3 giây cho mỗi request.
- Giả định 2: Camera Stream gửi payload theo chuẩn JSON UTF-8.
- Giả định 3: Mỗi detection request có detectionId duy nhất.
- Giả định 4: API yêu cầu Bearer token hợp lệ.
- Giả định 5: Tất cả response đều dùng content-type `application/json`.

---

## 5. Câu hỏi cho Provider

1. Detection result được lưu trong bao lâu?
2. AI Vision có hỗ trợ batch detection không?
3. Có giới hạn số request mỗi phút không?
4. Có pagination cho recent detections không?
5. Khi AI detect thất bại có trả retry hint không?

---

## 6. Rủi ro tích hợp

| Rủi ro | Tác động | Đề xuất xử lý |
|---|---|---|
| Provider đổi kiểu dữ liệu | Consumer parse lỗi | Chốt type/format/pattern |
| Provider thiếu mã lỗi | Consumer khó xử lý lỗi | Chuẩn hóa Problem Details |
| Response trả chậm | Camera bị delay | Thống nhất timeout và retry |
| Detection type thay đổi | Sai logic dashboard | Dùng enum cố định |
| API thay đổi version | Client bị breaking change | Áp dụng versioning `/v1` |
| Payload quá lớn | Timeout upload | Thống nhất size limit |