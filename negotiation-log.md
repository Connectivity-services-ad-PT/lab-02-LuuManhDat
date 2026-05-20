# Biên bản đàm phán hợp đồng API

- Cặp đàm phán: Pair-01 Camera Stream ↔ AI Vision
- Product: Smart Campus Operations Platform
- Provider: AI Vision Service
- Consumer: Camera Stream Service
- Phiên: v1.0
- Ngày: 2026-05-20

---

## Issue #1

- Raised by: Consumer
- Endpoint: POST /detections
- Concern: Consumer cần gửi frame ảnh nhanh theo thời gian thực.
- Proposal: Chỉ gửi metadata và imageUrl thay vì upload file binary trực tiếp.
- Resolution: Accepted
- Rationale: Giảm payload và giảm timeout khi mock bằng Prism.
- Impact: API dùng JSON body với field imageUrl.

---

## Issue #2

- Raised by: Provider
- Endpoint: POST /detections
- Concern: detectionType có thể không thống nhất giữa các service.
- Proposal: Chuẩn hóa enum detectionType gồm PERSON, VEHICLE, UNKNOWN_OBJECT.
- Resolution: Accepted
- Rationale: Tránh parse lỗi và đồng bộ analytics sau này.
- Impact: Consumer phải validate detectionType trước khi gửi.

---

## Issue #3

- Raised by: Consumer
- Endpoint: GET /detections/{detectionId}
- Concern: Consumer cần polling kết quả detection theo id.
- Proposal: Thêm endpoint GET theo UUID detectionId.
- Resolution: Accepted
- Rationale: Giúp Camera Stream theo dõi trạng thái AI processing.
- Impact: Provider phải lưu detectionId và trạng thái detection.

---

## Issue #4

- Raised by: Provider
- Endpoint: GET /detections
- Concern: Số lượng detection có thể rất lớn.
- Proposal: Dùng cursor-based pagination với limit và cursor query param.
- Resolution: Modified
- Rationale: Chấp nhận limit nhưng chưa triển khai cursor thật trong Lab 02.
- Impact: API có limit query param và field nextCursor.

---

## Issue #5

- Raised by: Consumer
- Endpoint: All secured endpoints
- Concern: Consumer cần biết format authentication.
- Proposal: Dùng Bearer JWT token chuẩn OpenAPI securitySchemes.
- Resolution: Accepted
- Rationale: Đồng bộ chuẩn authentication giữa các service Smart Campus.
- Impact: Các endpoint yêu cầu Authorization header.

---

## Issue #6

- Raised by: Provider
- Endpoint: All endpoints
- Concern: Consumer khó xử lý lỗi nếu response không thống nhất.
- Proposal: Chuẩn hóa lỗi theo RFC Problem Details.
- Resolution: Accepted
- Rationale: Dễ debug và dễ mở rộng contract-first.
- Impact: 400/401/403/404/409/422/500 đều dùng schema Problem.

---

# Chốt hợp đồng v1.0

Provider sign-off: AI Vision Team  
Consumer sign-off: Camera Stream Team  
Witness (GV/TA): ____________________  
Date: 2026-05-20

---

## Ghi chú warning nếu Spectral còn cảnh báo

| Warning | Lý do chấp nhận tạm thời | Kế hoạch sửa |
|---|---|---|
| operation-description warning | Một số endpoint thiếu description ngắn | Bổ sung đầy đủ description trước khi final submit |