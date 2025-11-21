# 2.2 HTTP Methods

HTTP Methods (còn gọi là HTTP Verbs) cho biết loại hành động bạn muốn thực hiện với resource.

## Các HTTP Methods Chính

| Method | Mục Đích | CRUD | Idempotent | Safe |
|--------|----------|------|------------|------|
| **GET** | Lấy dữ liệu | Read | ✅ | ✅ |
| **POST** | Tạo mới | Create | ❌ | ❌ |
| **PUT** | Update toàn bộ | Update | ✅ | ❌ |
| **PATCH** | Update một phần | Update | ❌ | ❌ |
| **DELETE** | Xóa | Delete | ✅ | ❌ |
| HEAD | Giống GET nhưng không có body | - | ✅ | ✅ |
| OPTIONS | Xem methods được hỗ trợ | - | ✅ | ✅ |

**Idempotent:** Gọi nhiều lần → kết quả giống nhau
**Safe:** Không thay đổi dữ liệu server

---

## GET - Lấy Dữ Liệu

**Mục đích:** Lấy dữ liệu từ server

**Đặc điểm:**
- ✅ Idempotent (gọi nhiều lần, kết quả giống nhau)
- ✅ Safe (không thay đổi dữ liệu)
- ✅ Có thể cache
- ❌ Không có request body
- ✅ Parameters qua URL (query string)

### Syntax

```
GET /resource
GET /resource/{id}
GET /resource?param1=value1&param2=value2
```

### Ví Dụ 1: Lấy Tất Cả Users

**Request:**
```http
GET /users HTTP/1.1
Host: api.example.com
```

**Response 200 OK:**
```json
[
  {
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com"
  },
  {
    "id": 2,
    "name": "Jane Smith",
    "email": "jane@example.com"
  }
]
```

### Ví Dụ 2: Lấy User Cụ Thể

**Request:**
```http
GET /users/1 HTTP/1.1
Host: api.example.com
```

**Response 200 OK:**
```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "age": 30
}
```

### Ví Dụ 3: GET với Query Parameters

**Request:**
```http
GET /users?status=active&role=admin HTTP/1.1
Host: api.example.com
```

**Response 200 OK:**
```json
{
  "data": [
    {
      "id": 5,
      "name": "Admin User",
      "status": "active",
      "role": "admin"
    }
  ],
  "total": 1
}
```

### Ví Dụ 4: GET Resource Không Tồn Tại

**Request:**
```http
GET /users/9999 HTTP/1.1
Host: api.example.com
```

**Response 404 Not Found:**
```json
{
  "error": "User not found",
  "code": 404
}
```

### Use Cases

- Lấy danh sách items
- Xem chi tiết một item
- Search và filter
- Pagination

---

## POST - Tạo Mới

**Mục đích:** Tạo resource mới

**Đặc điểm:**
- ❌ KHÔNG idempotent (gọi nhiều lần → tạo nhiều resources)
- ❌ KHÔNG safe (thay đổi dữ liệu)
- ❌ KHÔNG cache
- ✅ CÓ request body
- ✅ Thường trả về 201 Created

### Syntax

```
POST /resource
Content-Type: application/json

{
  "field1": "value1",
  "field2": "value2"
}
```

### Ví Dụ 1: Tạo User Mới

**Request:**
```http
POST /users HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "name": "Bob Wilson",
  "email": "bob@example.com",
  "age": 25
}
```

**Response 201 Created:**
```json
{
  "id": 123,
  "name": "Bob Wilson",
  "email": "bob@example.com",
  "age": 25,
  "createdAt": "2024-01-21T10:00:00Z"
}
```

**Response Headers:**
```http
Location: /users/123
```

### Ví Dụ 2: POST với Validation Error

**Request:**
```http
POST /users HTTP/1.1
Content-Type: application/json

{
  "name": "Bob",
  "email": "invalid-email"
}
```

**Response 400 Bad Request:**
```json
{
  "error": "Validation failed",
  "details": {
    "email": "Invalid email format"
  }
}
```

### Ví Dụ 3: POST Duplicate

**Request:**
```http
POST /users HTTP/1.1
Content-Type: application/json

{
  "email": "existing@example.com"
}
```

**Response 409 Conflict:**
```json
{
  "error": "Email already exists",
  "code": 409
}
```

### Use Cases

- Tạo user mới
- Submit form
- Upload file
- Đặt hàng
- Đăng bài viết

---

## PUT - Update Toàn Bộ

**Mục đích:** Thay thế toàn bộ resource

**Đặc điểm:**
- ✅ Idempotent (gọi nhiều lần, kết quả giống nhau)
- ❌ KHÔNG safe
- ✅ CÓ request body
- ✅ Phải gửi TẤT CẢ fields
- ✅ Trả về 200 OK hoặc 204 No Content

### Syntax

```
PUT /resource/{id}
Content-Type: application/json

{
  "field1": "new_value1",
  "field2": "new_value2",
  "field3": "new_value3"
}
```

### Ví Dụ: Update User

**Trước khi update:**
```json
{
  "id": 123,
  "name": "Bob Wilson",
  "email": "bob@example.com",
  "age": 25
}
```

**Request:**
```http
PUT /users/123 HTTP/1.1
Content-Type: application/json

{
  "name": "Bob Wilson Jr.",
  "email": "bob.jr@example.com",
  "age": 26
}
```

**Response 200 OK:**
```json
{
  "id": 123,
  "name": "Bob Wilson Jr.",
  "email": "bob.jr@example.com",
  "age": 26,
  "updatedAt": "2024-01-21T11:00:00Z"
}
```

### ⚠️ Lưu Ý: PUT Thay Thế Toàn Bộ

Nếu bạn không gửi một field, field đó có thể bị xóa!

**Request thiếu field:**
```http
PUT /users/123 HTTP/1.1
Content-Type: application/json

{
  "name": "Bob Wilson"
}
```

**Kết quả:**
```json
{
  "id": 123,
  "name": "Bob Wilson",
  "email": null,    ← BỊ XÓA!
  "age": null       ← BỊ XÓA!
}
```

---

## PATCH - Update Một Phần

**Mục đích:** Cập nhật một hoặc vài fields

**Đặc điểm:**
- ❌ KHÔNG idempotent (theo spec, nhưng thực tế thường là)
- ❌ KHÔNG safe
- ✅ CÓ request body
- ✅ Chỉ gửi fields cần update
- ✅ Trả về 200 OK

### Syntax

```
PATCH /resource/{id}
Content-Type: application/json

{
  "field_to_update": "new_value"
}
```

### Ví Dụ: Update Chỉ Email

**Trước khi update:**
```json
{
  "id": 123,
  "name": "Bob Wilson",
  "email": "old@example.com",
  "age": 25
}
```

**Request:**
```http
PATCH /users/123 HTTP/1.1
Content-Type: application/json

{
  "email": "new@example.com"
}
```

**Response 200 OK:**
```json
{
  "id": 123,
  "name": "Bob Wilson",           ← Không đổi
  "email": "new@example.com",     ← Đã update
  "age": 25                        ← Không đổi
}
```

### PUT vs PATCH

| | PUT | PATCH |
|---|-----|-------|
| **Update** | Toàn bộ resource | Một phần |
| **Fields** | Phải gửi tất cả | Chỉ gửi cần update |
| **Idempotent** | ✅ Yes | ❌ No (technically) |
| **Use When** | Replace hoàn toàn | Update vài fields |

**Ví dụ thực tế:**

```
PUT /users/123
→ "Thay user 123 bằng dữ liệu này"

PATCH /users/123
→ "Chỉ đổi email của user 123"
```

---

## DELETE - Xóa Resource

**Mục đích:** Xóa resource

**Đặc điểm:**
- ✅ Idempotent (xóa nhiều lần = xóa 1 lần)
- ❌ KHÔNG safe
- ❌ Thường KHÔNG có body
- ✅ Trả về 200, 202, hoặc 204

### Syntax

```
DELETE /resource/{id}
```

### Ví Dụ 1: Xóa Thành Công

**Request:**
```http
DELETE /users/123 HTTP/1.1
```

**Response 204 No Content**
(Không có body)

Hoặc:

**Response 200 OK:**
```json
{
  "message": "User deleted successfully",
  "deletedId": 123
}
```

### Ví Dụ 2: Xóa Resource Không Tồn Tại

**Request:**
```http
DELETE /users/9999 HTTP/1.1
```

**Response 404 Not Found:**
```json
{
  "error": "User not found"
}
```

### Ví Dụ 3: Soft Delete

Một số APIs không xóa thật, chỉ đánh dấu:

**Request:**
```http
DELETE /users/123 HTTP/1.1
```

**Server thực hiện:**
```sql
UPDATE users SET deleted_at = NOW() WHERE id = 123
```

**Response 200 OK:**
```json
{
  "message": "User deleted",
  "id": 123,
  "deletedAt": "2024-01-21T12:00:00Z"
}
```

### Idempotency của DELETE

DELETE 1 lần:
```
DELETE /users/123 → 204 No Content
User 123 đã bị xóa
```

DELETE lần 2 (cùng resource):
```
DELETE /users/123 → 404 Not Found
User không tồn tại (vì đã xóa)
```

Cả 2 lần đều không có user 123 → **Idempotent**!

---

## HEAD - Lấy Headers

**Mục đích:** Giống GET nhưng chỉ trả về headers, không có body

**Use Cases:**
- Check xem resource có tồn tại không
- Kiểm tra Content-Length trước khi download
- Check Last-Modified date

**Ví dụ:**
```http
HEAD /users/123 HTTP/1.1

Response 200 OK
Content-Type: application/json
Content-Length: 156
Last-Modified: Mon, 21 Jan 2024 10:00:00 GMT

(Không có body)
```

---

## OPTIONS - Xem Methods Được Hỗ Trợ

**Mục đích:** Xem endpoint hỗ trợ methods nào

**Ví dụ:**
```http
OPTIONS /users HTTP/1.1

Response 200 OK
Allow: GET, POST, PUT, DELETE, OPTIONS
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
```

**Use Case:**
- CORS preflight requests
- API discovery

---

## Tổng Hợp: CRUD Operations

| Operation | HTTP Method | Endpoint | Body | Response |
|-----------|------------|----------|------|----------|
| **Create** | POST | `/users` | ✅ | 201 + resource |
| **Read All** | GET | `/users` | ❌ | 200 + array |
| **Read One** | GET | `/users/123` | ❌ | 200 + resource |
| **Update Full** | PUT | `/users/123` | ✅ | 200 + resource |
| **Update Partial** | PATCH | `/users/123` | ✅ | 200 + resource |
| **Delete** | DELETE | `/users/123` | ❌ | 204 hoặc 200 |

---

## Best Practices

### 1. Sử Dụng Method Đúng

✅ **TỐT:**
```
GET    /users      - Lấy danh sách
POST   /users      - Tạo mới
PUT    /users/123  - Update toàn bộ
DELETE /users/123  - Xóa
```

❌ **TỆ:**
```
GET  /createUser?name=John     - Đừng dùng GET để tạo!
POST /getUsers                 - Đừng dùng POST để lấy!
GET  /deleteUser/123           - Đừng dùng GET để xóa!
```

### 2. Idempotency Quan Trọng

Với PUT và DELETE, gọi nhiều lần phải an toàn:

```
PUT /users/123 { "name": "John" }
→ Gọi 1 lần: name = "John"
→ Gọi 100 lần: name vẫn = "John" ✅

POST /users { "name": "John" }
→ Gọi 1 lần: 1 user
→ Gọi 100 lần: 100 users! ⚠️
```

### 3. Status Codes Đúng

```
GET    → 200 (OK)
POST   → 201 (Created)
PUT    → 200 (OK) hoặc 204 (No Content)
PATCH  → 200 (OK)
DELETE → 204 (No Content) hoặc 200 (OK)
```

---

## Thực Hành

### Bài Tập 1: Phân Tích

Cho các operations sau, hãy xác định HTTP method đúng:

1. Lấy profile của user
2. Tạo bài post mới
3. Đổi password
4. Xóa comment
5. Update avatar (chỉ avatar)
6. Thay toàn bộ thông tin sản phẩm

<details>
<summary><b>Đáp án</b></summary>

1. GET /users/{id} hoặc /profile
2. POST /posts
3. PATCH /users/{id}/password
4. DELETE /comments/{id}
5. PATCH /users/{id}/avatar
6. PUT /products/{id}
</details>

### Bài Tập 2: Sửa Lỗi

Tìm lỗi sai:

```
1. GET /users { "name": "John" }
2. POST /getUsers
3. DELETE /users/123 { "reason": "spam" }
4. PUT /users/123 { "email": "new@example.com" }
```

<details>
<summary><b>Đáp án</b></summary>

1. ❌ GET không nên có body → Dùng query params
2. ❌ Dùng GET thay vì POST để lấy data
3. ⚠️ DELETE thường không có body (nhưng technically OK)
4. ⚠️ PUT chỉ update email → nên dùng PATCH
</details>

---

## Tổng Kết

- ✅ GET: Lấy dữ liệu (safe, idempotent)
- ✅ POST: Tạo mới (không idempotent)
- ✅ PUT: Update toàn bộ (idempotent)
- ✅ PATCH: Update một phần
- ✅ DELETE: Xóa (idempotent)
- ✅ Sử dụng method đúng cho operation đúng!

## Tiếp Theo

Bây giờ hiểu về HTTP methods rồi, hãy tìm hiểu về [HTTP Status Codes](./http-status-codes.md)

---

[⬅️ REST API](./rest-api.md) | [Chương 2](./README.md) | [Tiếp Theo: HTTP Status Codes ➡️](./http-status-codes.md)
