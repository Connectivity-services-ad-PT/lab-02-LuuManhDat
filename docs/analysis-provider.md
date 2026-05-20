# Phân tích yêu cầu — vai Provider

- Cặp đàm phán: Pair 01 — Camera Stream ↔ AI Vision
- Product: Smart Campus Operations Platform
- Provider service: AI Vision
- Consumer service: Camera Stream
- Người viết: Lưu Mạnh Đạt
- Ngày: 20/05/2026

---

## 1. Resource chính

| Resource | Mô tả | Thuộc tính bắt buộc | Thuộc tính tùy chọn |
|---|---|---|---|
| DetectionRequest | Payload gửi frame ảnh để AI phân tích | cameraId, imageUrl | description |
| DetectionResult | Kết quả detect từ AI | detectionId, detectionType, confidence | plateNumber, personName |
| Alert | Thông tin cảnh báo sau detect | alertId, severity, timestamp | note |

---

## 2. Action/API dự kiến

| Method | Path | Mục đích | Consumer gọi khi nào? |
|---|---|---|---|
| POST | `/detections` | Gửi ảnh để AI detect | Khi camera phát hiện motion |
| GET | `/detections/{id}` | Lấy kết quả detect theo ID | Khi cần kiểm tra kết quả |
| GET | `/detections/recent` | Lấy danh sách detect gần nhất | Dashboard hoặc monitoring |
| GET | `/health` | Kiểm tra trạng thái service | Monitoring định kỳ |

---

## 3. Error case

Tối thiểu 5 case.

| Status | Tình huống | Response body dự kiến |
|---:|---|---|
| 400 | Payload sai định dạng | `Problem` |
| 401 | Thiếu Bearer token | `Problem` |
| 403 | Token hợp lệ nhưng không có quyền | `Problem` |
| 404 | Detection ID không tồn tại | `Problem` |
| 409 | Duplicate detection request | `Problem` |
| 422 | imageUrl không hợp lệ hoặc ảnh bị lỗi | `Problem` |
| 500 | AI engine xử lý thất bại | `Problem` |

---

## 4. Giả định bổ sung

Ghi rõ những điểm user story chưa nói nhưng Provider cần giả định.

- Giả định 1: AI Vision chỉ xử lý ảnh JPEG hoặc PNG.
- Giả định 2: Kích thước payload tối đa là 10MB.
- Giả định 3: Detection result được lưu trong 24 giờ.
- Giả định 4: Consumer phải gửi Bearer token hợp lệ.
- Giả định 5: Tất cả timestamp dùng chuẩn UTC ISO-8601.

---

## 5. Câu hỏi cho Consumer

1. Camera Stream có cần realtime response dưới 1 giây không?
2. Consumer có cần callback/webhook khi detect hoàn thành không?
3. Có cần hỗ trợ batch detection cho nhiều frame cùng lúc không?
4. Consumer có cần filter detection theo cameraId không?
5. Có cần pagination cho API recent detections không?

---

## 6. Rủi ro tích hợp

| Rủi ro | Tác động | Đề xuất xử lý |
|---|---|---|
| Tên field không thống nhất | Consumer parse lỗi | Chốt naming trong `openapi.yaml` |
| Payload ảnh quá lớn | Timeout hoặc crash mock server | Giới hạn size payload |
| Detection type không thống nhất | Sai logic xử lý phía Consumer | Dùng enum và discriminator |
| Response trả chậm | Camera bị delay | Thống nhất timeout và retry |
| Consumer gửi sai content-type | API reject request | Validate `application/json` |
| Version API thay đổi | Consumer bị breaking change | Áp dụng versioning `/v1` |