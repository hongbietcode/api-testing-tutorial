# 3.5 Request Configuration Chi Tiết

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Cấu hình query parameters và path variables
- ✅ Thiết lập headers đúng cách
- ✅ Gửi request body với các formats khác nhau
- ✅ Sử dụng Pre-request Scripts cơ bản
- ✅ Hiểu và áp dụng các options nâng cao

## 1. Query Parameters

### Query Parameters Là Gì?

Parameters được thêm vào URL sau dấu `?`:

```
https://api.example.com/users?page=1&limit=10&sort=name
                              └─────────┬─────────────┘
                                 Query Parameters
```

### Thêm Query Parameters

**Cách 1: Viết trực tiếp trong URL**
```
{{baseUrl}}/users?page=1&limit=10
```

**Cách 2: Dùng Params Tab (Recommended)**

1. Click tab **Params**
2. Thêm key-value pairs:

| Key | Value | Description |
|-----|-------|-------------|
| page | 1 | Page number |
| limit | 10 | Items per page |
| sort | name | Sort by field |

3. Postman tự động build URL:
```
{{baseUrl}}/users?page=1&limit=10&sort=name
```

### Lợi Ích Dùng Params Tab

- ✅ Dễ quản lý
- ✅ Tự động encode special characters
- ✅ Có thể enable/disable từng param
- ✅ Thêm description cho mỗi param

### Enable/Disable Parameters

Checkbox bên cạnh mỗi param:
- ✅ Checked: Param được thêm vào URL
- ☐ Unchecked: Param bị bỏ qua

**Ví dụ:**
```
✅ page = 1
✅ limit = 10
☐ sort = name    (disabled)

→ URL: /users?page=1&limit=10
```

### Sử Dụng Variables

```
Key: page
Value: {{currentPage}}

Key: limit
Value: {{pageSize}}
```

### Special Characters

Postman tự động encode:
```
search = hello world
→ URL: /users?search=hello%20world

email = test@example.com
→ URL: /users?email=test%40example.com
```

## 2. Path Variables

### Path Variables Là Gì?

Variables trong URL path:

```
https://api.example.com/users/123/posts/456
                            └─┬─┘      └─┬─┘
                           userId     postId
```

### Cấu Hình Path Variables

**Bước 1: Đặt variable trong URL**
```
{{baseUrl}}/users/:userId/posts/:postId
```

**Bước 2: Điền giá trị trong Params Tab**

Tab Params sẽ tự động hiện Path Variables section:

| Key | Value |
|-----|-------|
| userId | 1 |
| postId | 5 |

**Kết quả:**
```
https://api.example.com/users/1/posts/5
```

### Sử Dụng Environment Variables

```
Path: /users/:userId/posts/:postId

Path Variables:
  userId = {{currentUserId}}
  postId = {{selectedPostId}}
```

## 3. Headers

### Headers Là Gì?

Metadata của request, chứa thông tin như:
- Authentication
- Content type
- Accept format
- Custom configs

### Common Headers

| Header | Mục đích | Ví dụ |
|--------|----------|-------|
| Content-Type | Format của request body | application/json |
| Accept | Format mong muốn của response | application/json |
| Authorization | Authentication token | Bearer abc123 |
| User-Agent | Thông tin client | Postman/10.0 |
| X-API-Key | API key | your_api_key |

### Thêm Headers

**Tab Headers:**

| Key | Value |
|-----|-------|
| Content-Type | application/json |
| Authorization | Bearer {{accessToken}} |
| X-API-Key | {{apiKey}} |

### Auto-generated Headers

Postman tự động thêm:
- `Content-Length`
- `User-Agent: PostmanRuntime/7.x`
- `Accept: */*`
- `Connection: keep-alive`

Xem bằng cách:
1. Gửi request
2. Console → Request Headers

### Disable Auto-generated Headers

Settings → **Disable auto-added headers**

### Headers cho Authentication

**1. API Key:**
```
X-API-Key: your_api_key_123
```

**2. Bearer Token:**
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**3. Basic Auth:**
```
Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=
```

_(Hoặc dùng tab Authorization - sẽ học ở Chương 5)_

## 4. Request Body

### Body Types

Postman hỗ trợ nhiều formats:

#### 1. **none**
Không có body (dùng cho GET, DELETE)

#### 2. **form-data**
Gửi dữ liệu dạng form (có thể upload files)

**Khi nào dùng:**
- Upload files
- Submit HTML forms
- Multipart requests

**Ví dụ:**
```
Key         | Value        | Type
------------|--------------|------
name        | John Doe     | Text
email       | john@ex.com  | Text
avatar      | [file.jpg]   | File
```

**Content-Type:** `multipart/form-data`

#### 3. **x-www-form-urlencoded**
Dữ liệu form, encoded như query params

**Khi nào dùng:**
- Submit form đơn giản
- OAuth token requests

**Ví dụ:**
```
Key       | Value
----------|----------
username  | john
password  | secret123
```

**Content-Type:** `application/x-www-form-urlencoded`

**Request body:**
```
username=john&password=secret123
```

#### 4. **raw**
Gửi text thuần (JSON, XML, HTML, Text)

**JSON** (Phổ biến nhất)
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "age": 30
}
```

**Content-Type:** `application/json`

**XML**
```xml
<user>
  <name>John Doe</name>
  <email>john@example.com</email>
</user>
```

**Content-Type:** `application/xml`

**Text**
```
Plain text content here
```

**Content-Type:** `text/plain`

#### 5. **binary**
Upload file trực tiếp

**Khi nào dùng:**
- Upload single file
- API chỉ nhận file content

**Cách dùng:**
1. Chọn **binary**
2. Click **Select File**
3. Chọn file cần upload

#### 6. **GraphQL**
Query language cho APIs

```graphql
query {
  user(id: 1) {
    name
    email
  }
}
```

## 5. JSON Body Chi Tiết

### Syntax Highlighting

Postman tự động:
- Highlight syntax
- Check JSON validity
- Auto-format (Beautify)

### Variables trong JSON

```json
{
  "name": "{{userName}}",
  "email": "{{userEmail}}",
  "apiKey": "{{apiKey}}",
  "timestamp": {{$timestamp}},
  "id": "{{$guid}}"
}
```

### Nested Objects

```json
{
  "user": {
    "name": "John",
    "email": "john@example.com",
    "address": {
      "street": "123 Main St",
      "city": "New York",
      "country": "USA"
    }
  },
  "metadata": {
    "createdAt": {{$timestamp}},
    "source": "postman"
  }
}
```

### Arrays

```json
{
  "users": [
    {
      "name": "John",
      "email": "john@example.com"
    },
    {
      "name": "Jane",
      "email": "jane@example.com"
    }
  ],
  "tags": ["api", "testing", "postman"]
}
```

### Beautify JSON

Click **Beautify** để format code đẹp:

**Trước:**
```json
{"name":"John","email":"john@example.com","age":30}
```

**Sau:**
```json
{
  "name": "John",
  "email": "john@example.com",
  "age": 30
}
```

## 6. Pre-request Scripts

Scripts chạy **trước** khi gửi request.

### Khi Nào Dùng?

- ✅ Set dynamic variables
- ✅ Generate timestamps, IDs
- ✅ Calculate signatures
- ✅ Prepare test data

### Ví Dụ 1: Set Timestamp

```javascript
// Set current timestamp
pm.environment.set("timestamp", Date.now());

// Set formatted date
const now = new Date();
pm.environment.set("currentDate", now.toISOString());
```

Dùng trong body:
```json
{
  "createdAt": {{timestamp}},
  "date": "{{currentDate}}"
}
```

### Ví Dụ 2: Generate Random Data

```javascript
// Random user ID
const userId = Math.floor(Math.random() * 1000);
pm.environment.set("userId", userId);

// Random email
const randomEmail = `user${userId}@example.com`;
pm.environment.set("userEmail", randomEmail);
```

### Ví Dụ 3: Hash Password

```javascript
const CryptoJS = require('crypto-js');

// Get password from variable
const password = pm.variables.get("password");

// Hash with MD5
const hashedPassword = CryptoJS.MD5(password).toString();

// Save to variable
pm.environment.set("hashedPassword", hashedPassword);
```

### Ví Dụ 4: Get Variable from Previous Request

```javascript
// Get access token (saved by previous request)
const token = pm.environment.get("accessToken");

if (!token) {
  console.error("No access token found! Run login request first.");
} else {
  console.log("Using token:", token.substring(0, 20) + "...");
}
```

### Built-in Libraries

Postman có sẵn:
- `crypto-js`: Cryptography
- `atob/btoa`: Base64 encoding
- `xml2js`: XML parsing
- `cheerio`: HTML parsing
- `lodash`: Utility functions

## 7. Request Settings

Click tab **Settings** để cấu hình:

### Request Timeout
```
Timeout: 0 (unlimited) hoặc milliseconds
```

### Redirect Settings
```
Follow redirects: ON/OFF
Max redirects: 5
```

### SSL Certificate
```
SSL certificate verification: ON/OFF
```

_(Tắt khi test với self-signed certificates)_

### Encoding
```
Encode URL: Auto/Manual
```

## 8. Thực Hành

### Bài tập 1: Query Parameters

Sử dụng JSONPlaceholder API:

**Request 1:** Get posts với pagination
```
URL: {{baseUrl}}/posts

Query Params:
  _page = 1
  _limit = 5
```

Expected: 5 posts đầu tiên

**Request 2:** Filter posts
```
URL: {{baseUrl}}/posts

Query Params:
  userId = 1
  _limit = 3
```

Expected: 3 posts của user 1

**Request 3:** Search comments
```
URL: {{baseUrl}}/comments

Query Params:
  postId = 1
  _limit = 2
```

### Bài tập 2: Headers

**Request:** Get với custom headers
```
URL: {{baseUrl}}/posts/1

Headers:
  Accept: application/json
  User-Agent: MyApp/1.0
  X-Custom-Header: test-value
```

Check Console để xem request headers đã gửi.

### Bài tập 3: JSON Body

Tạo request giả lập tạo user:

```
Method: POST
URL: {{baseUrl}}/users

Headers:
  Content-Type: application/json

Body (raw - JSON):
{
  "name": "{{$randomFirstName}} {{$randomLastName}}",
  "email": "{{$randomEmail}}",
  "username": "user_{{$randomInt}}",
  "website": "https://example.com",
  "phone": "{{$randomPhoneNumber}}",
  "company": {
    "name": "{{$randomCompanyName}}",
    "catchPhrase": "{{$randomCatchPhrase}}"
  }
}
```

Gửi request nhiều lần, mỗi lần sẽ có data ngẫu nhiên khác nhau!

### Bài tập 4: Pre-request Script

**Request:** Create post với timestamp

**Pre-request Script:**
```javascript
// Generate timestamp
pm.environment.set("timestamp", Date.now());

// Generate random title
const adjectives = ["Amazing", "Awesome", "Great", "Cool"];
const nouns = ["Post", "Article", "Story", "Content"];
const randomAdj = adjectives[Math.floor(Math.random() * adjectives.length)];
const randomNoun = nouns[Math.floor(Math.random() * nouns.length)];
pm.environment.set("postTitle", `${randomAdj} ${randomNoun}`);

console.log("Generated title:", pm.environment.get("postTitle"));
```

**Body:**
```json
{
  "title": "{{postTitle}}",
  "body": "This post was created at {{timestamp}}",
  "userId": 1
}
```

**URL:** `POST {{baseUrl}}/posts`

Gửi request và xem Console logs!

## 9. Best Practices

### ✅ DO

- Dùng Params tab thay vì viết trong URL
- Tổ chức headers rõ ràng
- Dùng variables cho dynamic data
- Beautify JSON trước khi gửi
- Add descriptions cho params/headers
- Validate JSON syntax

### ❌ DON'T

- Hardcode sensitive data trong body
- Gửi body cho GET requests
- Quên set Content-Type header
- Dùng incorrect body type
- Copy-paste toàn bộ response làm test data

## 10. Troubleshooting

### Lỗi: Request body không được gửi

**Nguyên nhân:** Body type = "none" hoặc method không hỗ trợ body

**Giải pháp:**
1. Chọn đúng body type (raw - JSON)
2. Dùng POST/PUT/PATCH (không phải GET)

### Lỗi: 400 Bad Request

**Nguyên nhân:** JSON syntax sai

**Giải pháp:**
1. Click **Beautify** để check syntax
2. Validate JSON tại jsonlint.com
3. Check comma cuối cùng, quotes, brackets

### Lỗi: Headers không được apply

**Nguyên nhân:** Header bị disabled

**Giải pháp:**
1. Check checkbox bên cạnh header
2. Check Settings → Auto-added headers

## 11. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ Cấu hình query parameters và path variables
- ✅ Thiết lập headers đúng cách
- ✅ Sử dụng 6 loại body types
- ✅ Làm việc với JSON body và variables
- ✅ Viết Pre-request Scripts cơ bản
- ✅ Troubleshoot common issues

## 12. Cheat Sheet

| Element | Syntax |
|---------|--------|
| Query param | `?page=1&limit=10` |
| Path variable | `/users/:userId` |
| Variable | `{{variableName}}` |
| Random GUID | `{{$guid}}` |
| Timestamp | `{{$timestamp}}` |
| Random email | `{{$randomEmail}}` |
| Content-Type JSON | `application/json` |
| Content-Type Form | `application/x-www-form-urlencoded` |
| Bearer token | `Authorization: Bearer {{token}}` |

## Next Steps

Chúc mừng! Bạn đã hoàn thành **Chương 3: Postman Cơ Bản**.

Bây giờ bạn đã biết:
- ✅ Cài đặt và sử dụng Postman
- ✅ Gửi requests
- ✅ Tổ chức với Collections
- ✅ Quản lý Environments và Variables
- ✅ Cấu hình requests chi tiết

Hãy tiếp tục:
- **Chương tiếp theo**: [Chương 4: Thực Hành Cơ Bản](../04-thuc-hanh-co-ban/README.md)

---

[⬅️ Environments & Variables](./environments-variables.md) | [Tổng Quan Chương 3](./README.md) | [Tiếp Theo: Chương 4 ➡️](../04-thuc-hanh-co-ban/README.md)
