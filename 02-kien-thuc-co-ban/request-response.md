# 2.4 Request và Response

Hiểu rõ cấu trúc Request và Response là nền tảng để test API hiệu quả.

## HTTP Request Structure

```
[METHOD] [URL] [HTTP_VERSION]
[Headers]
[Blank Line]
[Body]
```

### Ví Dụ Thực Tế

```http
POST /api/users HTTP/1.1
Host: api.example.com
Content-Type: application/json
Authorization: Bearer abc123token
User-Agent: PostmanRuntime/7.32.0
Accept: */*

{
  "name": "John Doe",
  "email": "john@example.com"
}
```

---

## Request Components

### 1. Request Line

```
POST /api/users HTTP/1.1
│     │          │
│     │          └─ HTTP Version
│     └──────────── URL/Path
└────────────────── HTTP Method
```

**HTTP Method:** GET, POST, PUT, DELETE, PATCH
**URL:** Resource path
**HTTP Version:** Usually HTTP/1.1 or HTTP/2

### 2. Headers

Metadata về request

**Common Headers:**

```http
Host: api.example.com
Content-Type: application/json
Authorization: Bearer token123
Accept: application/json
User-Agent: PostmanRuntime/7.32.0
Accept-Language: en-US,en;q=0.9
```

#### Host
```http
Host: api.example.com
```
- Server domain
- Bắt buộc trong HTTP/1.1

#### Content-Type
```http
Content-Type: application/json
```
- Loại dữ liệu trong body
- Common values:
  - `application/json` - JSON data
  - `application/xml` - XML data
  - `application/x-www-form-urlencoded` - Form data
  - `multipart/form-data` - File uploads
  - `text/plain` - Plain text

#### Authorization
```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```
- Authentication credentials
- Types:
  - `Bearer {token}` - Token-based
  - `Basic {base64}` - Username:password
  - `API-Key {key}` - API key

#### Accept
```http
Accept: application/json
```
- Format client muốn nhận
- `*/*` = accept anything

#### User-Agent
```http
User-Agent: PostmanRuntime/7.32.0
```
- Client information
- Browser, app name, version

### 3. Body

Dữ liệu gửi đi (chỉ có trong POST, PUT, PATCH)

**JSON Body:**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "age": 30
}
```

**Form Data (URL Encoded):**
```
name=John+Doe&email=john%40example.com&age=30
```

**Multipart Form (File Upload):**
```
------WebKitFormBoundary
Content-Disposition: form-data; name="file"; filename="photo.jpg"
Content-Type: image/jpeg

[Binary data]
------WebKitFormBoundary--
```

### 4. Query Parameters

Parameters trong URL sau dấu `?`

```
GET /api/users?page=1&limit=10&status=active
                └────────────┬──────────────┘
                      Query Parameters
```

**Cấu trúc:**
```
?key1=value1&key2=value2&key3=value3
```

**Ví dụ:**
```
/users?page=1&limit=10
/products?category=electronics&sort=price&order=asc
/search?q=laptop&maxPrice=1000
```

### 5. Path Parameters

Parameters trong URL path

```
GET /api/users/123/posts/456
            └─┬┘       └─┬┘
           userId     postId
```

**Pattern:**
```
/users/{userId}
/products/{productId}
/orders/{orderId}/items/{itemId}
```

---

## HTTP Response Structure

```
[HTTP_VERSION] [STATUS_CODE] [STATUS_MESSAGE]
[Headers]
[Blank Line]
[Body]
```

### Ví Dụ Thực Tế

```http
HTTP/1.1 200 OK
Date: Mon, 21 Jan 2024 10:00:00 GMT
Content-Type: application/json
Content-Length: 156
Cache-Control: no-cache
Connection: keep-alive

{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "createdAt": "2024-01-21T10:00:00Z"
}
```

---

## Response Components

### 1. Status Line

```
HTTP/1.1 200 OK
│        │   │
│        │   └─ Status Message
│        └───── Status Code
└───────────── HTTP Version
```

### 2. Response Headers

**Common Headers:**

```http
Content-Type: application/json
Content-Length: 156
Date: Mon, 21 Jan 2024 10:00:00 GMT
Server: nginx/1.18.0
Cache-Control: no-cache, no-store, must-revalidate
ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"
Set-Cookie: sessionId=abc123; HttpOnly; Secure
Access-Control-Allow-Origin: *
```

#### Content-Type
```http
Content-Type: application/json; charset=utf-8
```
- Loại dữ liệu trong response

#### Content-Length
```http
Content-Length: 156
```
- Kích thước body (bytes)

#### Cache-Control
```http
Cache-Control: max-age=3600
```
- Quy tắc caching
- `no-cache` - Không cache
- `max-age=3600` - Cache 1 giờ

#### Set-Cookie
```http
Set-Cookie: sessionId=abc123; HttpOnly; Secure; SameSite=Strict
```
- Set cookies cho client

#### ETag
```http
ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"
```
- Version identifier cho resource
- Dùng cho caching

### 3. Response Body

Dữ liệu server trả về

**Success Response:**
```json
{
  "status": "success",
  "data": {
    "id": 123,
    "name": "John Doe",
    "email": "john@example.com"
  }
}
```

**Error Response:**
```json
{
  "status": "error",
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid email format",
    "field": "email"
  }
}
```

**List Response:**
```json
{
  "data": [
    { "id": 1, "name": "Item 1" },
    { "id": 2, "name": "Item 2" }
  ],
  "meta": {
    "total": 100,
    "page": 1,
    "perPage": 10
  }
}
```

---

## Request/Response Examples

### Example 1: Simple GET

**Request:**
```http
GET /api/users/123 HTTP/1.1
Host: api.example.com
Accept: application/json
```

**Response:**
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 123,
  "name": "John Doe"
}
```

### Example 2: POST Create

**Request:**
```http
POST /api/users HTTP/1.1
Host: api.example.com
Content-Type: application/json
Authorization: Bearer token123

{
  "name": "Jane Smith",
  "email": "jane@example.com"
}
```

**Response:**
```http
HTTP/1.1 201 Created
Location: /api/users/456
Content-Type: application/json

{
  "id": 456,
  "name": "Jane Smith",
  "email": "jane@example.com",
  "createdAt": "2024-01-21T10:00:00Z"
}
```

### Example 3: Error Response

**Request:**
```http
POST /api/users HTTP/1.1
Content-Type: application/json

{
  "name": "Invalid User",
  "email": "not-an-email"
}
```

**Response:**
```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "error": "Validation failed",
  "details": {
    "email": "Invalid email format"
  }
}
```

---

## Postman Request/Response

### Viewing in Postman

**Request:**
- Method dropdown
- URL field
- Params tab (query params)
- Authorization tab
- Headers tab
- Body tab

**Response:**
- Status code (colored)
- Time taken
- Size
- Body tab (Pretty, Raw, Preview)
- Headers tab
- Cookies tab
- Test Results tab

### Reading Response in Tests

```javascript
// Status code
const statusCode = pm.response.code;
console.log(statusCode); // 200

// Headers
const contentType = pm.response.headers.get('Content-Type');
console.log(contentType); // application/json

// Body
const body = pm.response.json();
console.log(body.name); // John Doe

// Response time
const responseTime = pm.response.responseTime;
console.log(responseTime); // 245 (ms)

// Size
const size = pm.response.size();
console.log(size); // Size in bytes
```

---

## Common Patterns

### Pagination

**Request:**
```
GET /api/users?page=2&limit=20
```

**Response:**
```json
{
  "data": [...],
  "pagination": {
    "page": 2,
    "limit": 20,
    "total": 100,
    "totalPages": 5,
    "hasNext": true,
    "hasPrevious": true
  }
}
```

### Filtering và Sorting

**Request:**
```
GET /api/products?category=electronics&minPrice=100&sort=price&order=desc
```

### Search

**Request:**
```
GET /api/search?q=laptop&type=product
```

### Nested Resources

**Request:**
```
GET /api/users/123/posts
GET /api/posts/456/comments
```

---

## Best Practices

### Request

✅ **DO:**
- Sử dụng HTTPS
- Include proper headers
- Validate data trước khi gửi
- Use appropriate HTTP method
- Include authentication

❌ **DON'T:**
- Gửi passwords qua query params
- Include sensitive data trong URLs
- Make unnecessary requests
- Send malformed JSON

### Response

✅ **DO:**
- Return appropriate status codes
- Include helpful error messages
- Use consistent structure
- Include timestamps
- Provide links to related resources

❌ **DON'T:**
- Expose internal errors
- Return inconsistent formats
- Include sensitive information
- Use wrong status codes

---

## Testing Requests/Responses

### Test Request Headers

```javascript
// In Pre-request Script
pm.request.headers.add({
    key: 'X-Custom-Header',
    value: 'custom-value'
});
```

### Test Response Headers

```javascript
pm.test("Content-Type is JSON", function () {
    pm.expect(pm.response.headers.get('Content-Type'))
        .to.include('application/json');
});

pm.test("Has CORS headers", function () {
    pm.expect(pm.response.headers.get('Access-Control-Allow-Origin'))
        .to.exist;
});
```

### Test Response Body

```javascript
pm.test("Response structure is correct", function () {
    const response = pm.response.json();

    pm.expect(response).to.have.property('data');
    pm.expect(response).to.have.property('meta');
});
```

### Test Response Time

```javascript
pm.test("Response time is acceptable", function () {
    pm.expect(pm.response.responseTime).to.be.below(200);
});
```

---

## Tổng Kết

- ✅ Request có: Method, URL, Headers, Body
- ✅ Response có: Status Code, Headers, Body
- ✅ Headers chứa metadata
- ✅ Body chứa dữ liệu chính
- ✅ Query params trong URL sau `?`
- ✅ Path params trong URL path
- ✅ Hiểu cấu trúc để test hiệu quả

## Tiếp Theo

Bây giờ hiểu Request/Response rồi, hãy tìm hiểu về [JSON và XML](./json-xml.md)

---

[⬅️ HTTP Status Codes](./http-status-codes.md) | [Chương 2](./README.md) | [Tiếp Theo: JSON & XML ➡️](./json-xml.md)
