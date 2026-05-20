# VERSIONING.md

# Smart Campus Operations Platform — API Versioning Strategy

## 1. Mục đích

Tài liệu này mô tả chiến lược versioning cho hợp đồng API của hệ thống Smart Campus trong Lab 02.

Mục tiêu:

- Đảm bảo Consumer và Provider tương thích khi API thay đổi.
- Giảm rủi ro phá vỡ tích hợp giữa các service.
- Cho phép mở rộng schema trong tương lai mà không làm hỏng client cũ.
- Hỗ trợ quản lý thay đổi giữa các phiên bản hợp đồng API.

---

# 2. Phiên bản hiện tại

| Thành phần | Version |
|---|---|
| OpenAPI Specification | 3.1.0 |
| API Contract Version | v1 |
| Current Release | 1.0.0 |

---

# 3. Quy tắc versioning

Dự án áp dụng nguyên tắc Semantic Versioning (SemVer):

```text
MAJOR.MINOR.PATCH