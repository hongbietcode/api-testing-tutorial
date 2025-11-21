# 2.3 HTTP Status Codes

HTTP Status Codes là mã số 3 chữ số mà server trả về để cho biết kết quả của request.

## Cấu Trúc Status Code

```
200
│││
││└─ Specific code (mã cụ thể)
│└── Sub-category
└─── Category (loại)
```

## 5 Loại Status Codes

| Range | Loại | Ý Nghĩa | Ví Dụ |
|-------|------|---------|-------|
| **1xx** | Informational | Đang xử lý | 100 Continue |
| **2xx** | Success | Thành công | 200 OK |
| **3xx** | Redirection | Chuyển hướng | 301 Moved |
| **4xx** | Client Error | Lỗi client | 404 Not Found |
| **5xx** | Server Error | Lỗi server | 500 Internal Error |

---

## 2xx - Success (Thành Công)

### 200 OK ⭐

**Ý nghĩa:** Request thành công

**Khi nào:**
- GET lấy dữ liệu thành công
- PUT/PATCH update thành công
- DELETE xóa thành công (với body)

**Ví dụ:**
```http
GET /users/123

Response:
200 OK
{
  "id": 123,
  "name": "John Doe"
}
```

### 201 Created ⭐

**Ý nghĩa:** Resource được tạo thành công

**Khi nào:**
- POST tạo mới thành công

**Ví dụ:**
```http
POST /users
{
  "name": "John"
}

Response:
201 Created
Location: /users/456
{
  "id": 456,
  "name": "John",
  "createdAt": "2024-01-21T10:00:00Z"
}
```

### 202 Accepted

**Ý nghĩa:** Request được chấp nhận nhưng chưa xử lý xong

**Khi nào:**
- Async operations
- Background jobs
- Long-running tasks

**Ví dụ:**
```http
POST /reports/generate

Response:
202 Accepted
{
  "jobId": "abc123",
  "status": "processing",
  "statusUrl": "/jobs/abc123"
}
```

### 204 No Content ⭐

**Ý nghĩa:** Thành công nhưng không có dữ liệu trả về

**Khi nào:**
- DELETE thành công
- PUT/PATCH thành công (không cần trả data)

**Ví dụ:**
```http
DELETE /users/123

Response:
204 No Content
(Không có body)
```

---

## 3xx - Redirection (Chuyển Hướng)

### 301 Moved Permanently

**Ý nghĩa:** Resource đã chuyển vĩnh viễn

**Ví dụ:**
```http
GET /old-api/users

Response:
301 Moved Permanently
Location: /v2/users
```

### 302 Found

**Ý nghĩa:** Resource tạm thời ở URL khác

### 304 Not Modified

**Ý nghĩa:** Resource không thay đổi (caching)

**Ví dụ:**
```http
GET /users/123
If-None-Match: "etag-123"

Response:
304 Not Modified
(Client dùng cached data)
```

---

## 4xx - Client Errors (Lỗi Phía Client) ⭐

### 400 Bad Request ⭐⭐⭐

**Ý nghĩa:** Request không hợp lệ

**Khi nào:**
- Body sai format
- Thiếu required fields
- Data type không đúng

**Ví dụ 1: JSON sai format**
```http
POST /users
{
  "name": "John"
  "email": "john@test.com"  ← Thiếu dấu phẩy
}

Response:
400 Bad Request
{
  "error": "Invalid JSON format"
}
```

**Ví dụ 2: Thiếu required field**
```http
POST /users
{
  "name": "John"
  // Thiếu email (required)
}

Response:
400 Bad Request
{
  "error": "Validation failed",
  "details": {
    "email": "Email is required"
  }
}
```

### 401 Unauthorized ⭐⭐⭐

**Ý nghĩa:** Chưa xác thực (chưa đăng nhập)

**Khi nào:**
- Thiếu authentication token
- Token không hợp lệ
- Token hết hạn

**Ví dụ 1: Thiếu token**
```http
GET /profile

Response:
401 Unauthorized
{
  "error": "Authentication required",
  "message": "Please provide a valid token"
}
```

**Ví dụ 2: Token hết hạn**
```http
GET /profile
Authorization: Bearer expired_token_123

Response:
401 Unauthorized
{
  "error": "Token expired",
  "message": "Please login again"
}
```

### 403 Forbidden ⭐⭐⭐

**Ý nghĩa:** Không có quyền truy cập

**Phân biệt 401 vs 403:**
- **401**: "Bạn là ai?" (chưa login)
- **403**: "Tôi biết bạn là ai, nhưng bạn không được phép"

**Ví dụ:**
```http
DELETE /users/456
Authorization: Bearer regular_user_token

Response:
403 Forbidden
{
  "error": "Insufficient permissions",
  "message": "Only admins can delete users"
}
```

### 404 Not Found ⭐⭐⭐

**Ý nghĩa:** Resource không tồn tại

**Khi nào:**
- ID không tồn tại
- Endpoint sai
- Resource đã bị xóa

**Ví dụ:**
```http
GET /users/99999

Response:
404 Not Found
{
  "error": "User not found",
  "userId": 99999
}
```

### 405 Method Not Allowed

**Ý nghĩa:** HTTP method không được hỗ trợ

**Ví dụ:**
```http
POST /users/123
(Endpoint này chỉ support GET, PUT, DELETE)

Response:
405 Method Not Allowed
Allow: GET, PUT, DELETE
{
  "error": "POST method not allowed on this endpoint"
}
```

### 409 Conflict ⭐⭐

**Ý nghĩa:** Xung đột dữ liệu

**Khi nào:**
- Email/username đã tồn tại
- Version conflict
- Race condition

**Ví dụ:**
```http
POST /users
{
  "email": "existing@example.com"
}

Response:
409 Conflict
{
  "error": "Email already exists",
  "field": "email"
}
```

### 422 Unprocessable Entity ⭐⭐

**Ý nghĩa:** Request đúng format nhưng dữ liệu không hợp lệ

**Phân biệt 400 vs 422:**
- **400**: Syntax sai (JSON lỗi, thiếu field bắt buộc)
- **422**: Syntax đúng nhưng logic/validation sai

**Ví dụ:**
```http
POST /users
{
  "name": "John",
  "email": "invalid-email-format",
  "age": -5
}

Response:
422 Unprocessable Entity
{
  "error": "Validation failed",
  "details": {
    "email": "Invalid email format",
    "age": "Age must be positive"
  }
}
```

### 429 Too Many Requests

**Ý nghĩa:** Quá nhiều requests (rate limiting)

**Ví dụ:**
```http
GET /api/data

Response:
429 Too Many Requests
Retry-After: 60
{
  "error": "Rate limit exceeded",
  "message": "Maximum 100 requests per minute",
  "retryAfter": 60
}
```

---

## 5xx - Server Errors (Lỗi Phía Server) ⭐

### 500 Internal Server Error ⭐⭐⭐

**Ý nghĩa:** Lỗi server không xác định

**Khi nào:**
- Bug trong code
- Exception không handle
- Database connection error

**Ví dụ:**
```http
GET /users/123

Response:
500 Internal Server Error
{
  "error": "Internal server error",
  "message": "An unexpected error occurred"
}
```

**⚠️ Lưu ý:** KHÔNG expose stack trace cho client!

❌ **TỆ:**
```json
{
  "error": "NullPointerException at line 45 in UserController.java",
  "stackTrace": "..."
}
```

✅ **TỐT:**
```json
{
  "error": "Internal server error",
  "requestId": "abc123"
}
```

### 502 Bad Gateway

**Ý nghĩa:** Gateway/proxy nhận response không hợp lệ từ upstream server

**Khi nào:**
- Backend server down
- Timeout từ upstream
- Proxy configuration sai

### 503 Service Unavailable ⭐⭐

**Ý nghĩa:** Service tạm thời không khả dụng

**Khi nào:**
- Maintenance
- Server overload
- Deploying

**Ví dụ:**
```http
GET /api/users

Response:
503 Service Unavailable
Retry-After: 3600
{
  "error": "Service temporarily unavailable",
  "message": "Scheduled maintenance",
  "retryAfter": 3600
}
```

### 504 Gateway Timeout

**Ý nghĩa:** Gateway/proxy timeout chờ upstream server

**Khi nào:**
- Upstream server quá chậm
- Network issues
- Long-running operations

---

## Decision Tree: Chọn Status Code Nào?

```
Request thành công?
├─ Yes → 2xx
│   ├─ Created new resource? → 201
│   ├─ Success with data? → 200
│   └─ Success without data? → 204
│
└─ No → Error
    ├─ Client lỗi? → 4xx
    │   ├─ Syntax/format sai? → 400
    │   ├─ Chưa login? → 401
    │   ├─ Không có quyền? → 403
    │   ├─ Không tìm thấy? → 404
    │   ├─ Validation lỗi? → 422
    │   └─ Conflict? → 409
    │
    └─ Server lỗi? → 5xx
        ├─ Generic error? → 500
        ├─ Service down? → 503
        └─ Timeout? → 504
```

---

## Best Practices

### 1. Sử Dụng Status Codes Đúng

✅ **TỐT:**
```
POST /users → 201 Created (tạo mới)
GET /users/123 → 200 OK (có data)
DELETE /users/123 → 204 No Content
GET /users/9999 → 404 Not Found
```

❌ **TỆ:**
```
POST /users → 200 OK (nên là 201!)
GET /users/9999 → 200 OK { "error": "not found" } (nên là 404!)
DELETE /users/123 → 200 OK (nên là 204!)
```

### 2. Consistent Error Format

✅ **TỐT:**
```json
{
  "error": "Error type",
  "message": "Human-readable message",
  "details": {
    "field": "error detail"
  },
  "code": "ERROR_CODE",
  "requestId": "abc123"
}
```

### 3. Provide Helpful Messages

✅ **TỐT:**
```json
{
  "error": "Validation failed",
  "details": {
    "email": "Email format is invalid. Example: user@example.com",
    "age": "Age must be between 18 and 100"
  }
}
```

❌ **TỆ:**
```json
{
  "error": "Error"
}
```

### 4. Security Considerations

**KHÔNG tiết lộ thông tin nhạy cảm:**

❌ **TỆ (Security risk):**
```json
{
  "error": "SQL Error: Table 'users' doesn't exist",
  "query": "SELECT * FROM users WHERE id=123"
}
```

✅ **TỐT:**
```json
{
  "error": "Internal server error",
  "requestId": "abc123"
}
```

---

## Common Scenarios

### Scenario 1: User Registration

**Success:**
```
POST /register
{
  "email": "user@example.com",
  "password": "secure123"
}

→ 201 Created
```

**Email exists:**
```
→ 409 Conflict
{
  "error": "Email already registered"
}
```

**Weak password:**
```
→ 422 Unprocessable Entity
{
  "error": "Password too weak",
  "requirements": ["8+ characters", "1 uppercase", "1 number"]
}
```

### Scenario 2: Get User Profile

**Success:**
```
GET /profile
Authorization: Bearer valid_token

→ 200 OK
{
  "id": 123,
  "name": "John"
}
```

**No token:**
```
GET /profile

→ 401 Unauthorized
{
  "error": "Authentication required"
}
```

**Invalid token:**
```
GET /profile
Authorization: Bearer invalid_token

→ 401 Unauthorized
{
  "error": "Invalid token"
}
```

### Scenario 3: Delete User

**Success:**
```
DELETE /users/123
Authorization: Bearer admin_token

→ 204 No Content
```

**Not admin:**
```
DELETE /users/123
Authorization: Bearer regular_user_token

→ 403 Forbidden
{
  "error": "Admin access required"
}
```

**User not found:**
```
DELETE /users/99999

→ 404 Not Found
{
  "error": "User not found"
}
```

---

## Testing Status Codes

### Test Cases Cần Có

**For every endpoint:**

1. ✅ **Happy path** - 200/201/204
2. ✅ **Invalid input** - 400/422
3. ✅ **Unauthorized** - 401
4. ✅ **Forbidden** - 403
5. ✅ **Not found** - 404
6. ✅ **Server error** - 500

**Postman test example:**
```javascript
// Test happy path
pm.test("Status code is 200", () => {
    pm.response.to.have.status(200);
});

// Test error case
pm.test("Returns 404 for invalid ID", () => {
    pm.response.to.have.status(404);
});

// Test response body for error
pm.test("Error message is clear", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('error');
});
```

---

## Quick Reference Table

| Code | Name | Khi Nào Dùng | Ví Dụ |
|------|------|--------------|-------|
| 200 | OK | Request thành công | GET, PUT thành công |
| 201 | Created | Tạo mới thành công | POST user mới |
| 204 | No Content | Thành công, không có data | DELETE thành công |
| 400 | Bad Request | Request sai format | JSON invalid |
| 401 | Unauthorized | Chưa đăng nhập | Thiếu token |
| 403 | Forbidden | Không có quyền | User thường xóa admin |
| 404 | Not Found | Resource không tồn tại | User ID không có |
| 422 | Unprocessable | Validation failed | Email format sai |
| 429 | Too Many Requests | Rate limit | Quá 100 req/min |
| 500 | Internal Error | Server lỗi | Bug, exception |
| 503 | Service Unavailable | Service down | Maintenance |

---

## Tổng Kết

- ✅ 2xx = Success
- ✅ 4xx = Client error (sửa request)
- ✅ 5xx = Server error (không phải lỗi của bạn)
- ✅ Sử dụng đúng status code cho đúng tình huống
- ✅ Luôn test cả happy path và error cases
- ✅ Provide clear error messages

## Tiếp Theo

Hiểu về status codes rồi, hãy tìm hiểu cấu trúc [Request và Response](./request-response.md)

---

[⬅️ HTTP Methods](./http-methods.md) | [Chương 2](./README.md) | [Tiếp Theo: Request & Response ➡️](./request-response.md)
