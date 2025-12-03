# 4.2 Test POST Requests

POST method dùng để **tạo mới** resources. Đây là method quan trọng thứ hai sau GET.

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Gửi POST requests với JSON body
- ✅ Verify status code 201 Created
- ✅ Verify response trả về resource mới tạo
- ✅ Test với dữ liệu hợp lệ và không hợp lệ
- ✅ Handle errors khi create

## Chuẩn Bị

Sử dụng JSONPlaceholder API:
- **Base URL:** https://jsonplaceholder.typicode.com
- Endpoints: `/users`, `/posts`, `/comments`, `/todos`

## 1. POST Request Cơ Bản

### Cấu Trúc POST Request

```
POST /users
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com"
}
```

### Thành Phần Quan Trọng

1. **Method**: POST
2. **URL**: Endpoint tạo resource
3. **Headers**: `Content-Type: application/json`
4. **Body**: Dữ liệu JSON của resource mới

## 2. Thực Hành 1: Tạo User Mới

### Bước 1: Tạo Request

**Request Name:** Create New User

**Config:**
```
Method: POST
URL: {{base_url}}/users
```

**Headers:**
```
Content-Type: application/json
```

**Body (raw - JSON):**
```json
{
  "name": "John Doe",
  "username": "johndoe",
  "email": "john@example.com",
  "phone": "1-234-567-8901",
  "website": "john-doe.com"
}
```

### Bước 2: Gửi Request

Click **Send**

### Bước 3: Verify Response

**Expected Status:** `201 Created` hoặc `200 OK`

**Expected Response Body:**
```json
{
  "id": 11,
  "name": "John Doe",
  "username": "johndoe",
  "email": "john@example.com",
  "phone": "1-234-567-8901",
  "website": "john-doe.com"
}
```

**Điểm quan trọng:**
- ✅ Server tự động generate `id` (id: 11)
- ✅ Response trả về toàn bộ object vừa tạo
- ✅ Status code là 201 (Created) hoặc 200 (OK)

### Lưu ý về JSONPlaceholder

> **⚠️ Lưu ý:** JSONPlaceholder là **fake API**, nên:
> - Resource không thực sự được tạo trong database
> - ID luôn trả về 11 (không tăng dần)
> - Nếu GET `/users/11` sẽ không tìm thấy
> - Nhưng hoàn hảo để thực hành!

## 3. Thực Hành 2: Tạo Post Mới

### Request: Create New Post

```
Method: POST
URL: {{base_url}}/posts

Headers:
Content-Type: application/json

Body:
{
  "title": "My First Post",
  "body": "This is the content of my first post. It's awesome!",
  "userId": 1
}
```

### Expected Response

**Status:** `201 Created`

**Body:**
```json
{
  "id": 101,
  "title": "My First Post",
  "body": "This is the content of my first post. It's awesome!",
  "userId": 1
}
```

### Verify Points

- ✅ Status code = 201
- ✅ Response có field `id`
- ✅ `title` và `body` giống với request
- ✅ `userId` đúng

## 4. Thực Hành 3: Tạo Comment

### Request: Create Comment for Post

```
Method: POST
URL: {{base_url}}/comments

Body:
{
  "postId": 1,
  "name": "Great post!",
  "email": "commenter@example.com",
  "body": "I really enjoyed reading this post. Thanks for sharing!"
}
```

### Expected Response

```json
{
  "id": 501,
  "postId": 1,
  "name": "Great post!",
  "email": "commenter@example.com",
  "body": "I really enjoyed reading this post. Thanks for sharing!"
}
```

## 5. Thực Hành 4: Tạo Todo

### Request: Create New Todo

```
Method: POST
URL: {{base_url}}/todos

Body:
{
  "userId": 1,
  "title": "Learn API Testing",
  "completed": false
}
```

### Expected Response

```json
{
  "id": 201,
  "userId": 1,
  "title": "Learn API Testing",
  "completed": false
}
```

## 6. POST Với Variables

Sử dụng variables để tạo dynamic data.

### Pre-request Script

```javascript
// Generate random data
pm.environment.set("randomName", pm.variables.replaceIn("{{$randomFirstName}} {{$randomLastName}}"));
pm.environment.set("randomEmail", pm.variables.replaceIn("{{$randomEmail}}"));
pm.environment.set("randomUsername", "user_" + Math.floor(Math.random() * 10000));
```

### Body Sử Dụng Variables

```json
{
  "name": "{{randomName}}",
  "username": "{{randomUsername}}",
  "email": "{{randomEmail}}",
  "phone": "{{$randomPhoneNumber}}",
  "website": "{{$randomDomainName}}"
}
```

Mỗi lần gửi request sẽ có data khác nhau!

## 7. Verify Response Data

### Basic Verification

Sau khi POST, kiểm tra:

1. **Status Code**
   ```javascript
   pm.test("Status code is 201", function() {
     pm.response.to.have.status(201);
   });
   ```

2. **Response có ID**
   ```javascript
   pm.test("Response has id", function() {
     const response = pm.response.json();
     pm.expect(response).to.have.property("id");
   });
   ```

3. **Data Chính Xác**
   ```javascript
   pm.test("Name is correct", function() {
     const response = pm.response.json();
     pm.expect(response.name).to.eql("John Doe");
   });
   ```

### Complete Test Example

**Tab Tests:**
```javascript
// Test status code
pm.test("Status code is 201", function() {
    pm.response.to.have.status(201);
});

// Parse response
const response = pm.response.json();

// Test response has id
pm.test("Response has id", function() {
    pm.expect(response).to.have.property("id");
    pm.expect(response.id).to.be.a("number");
});

// Test data is correct
pm.test("Data matches request", function() {
    pm.expect(response.name).to.eql("John Doe");
    pm.expect(response.email).to.eql("john@example.com");
});

// Save id for later use
pm.environment.set("createdUserId", response.id);
console.log("Created user with ID:", response.id);
```

## 8. Test Với Dữ Liệu Không Hợp Lệ

### Test 1: Missing Required Fields

```
Method: POST
URL: {{base_url}}/posts

Body:
{
  "title": "Post without body"
  // Missing "body" and "userId"
}
```

**Expected:** JSONPlaceholder vẫn accept (vì nó fake), nhưng real API sẽ trả `400 Bad Request`.

### Test 2: Invalid Data Type

```json
{
  "userId": "not-a-number",  // Should be number
  "title": 12345,            // Should be string
  "completed": "yes"         // Should be boolean
}
```

### Test 3: Empty Body

```
Method: POST
URL: {{base_url}}/users

Body:
{}
```

**Expected:** Với real API, sẽ trả `400` hoặc `422 Unprocessable Entity`.

### Test Script cho Invalid Data

```javascript
// Expect error status
pm.test("Status code is 400 or 422", function() {
    pm.expect([400, 422]).to.include(pm.response.code);
});

// Expect error message
pm.test("Response contains error", function() {
    const response = pm.response.json();
    pm.expect(response).to.have.property("error");
});
```

## 9. Chain Requests: Create Then Get

### Request 1: Create User

```javascript
// Tests tab
const response = pm.response.json();
pm.environment.set("newUserId", response.id);
console.log("Created user ID:", response.id);
```

### Request 2: Get Created User

```
Method: GET
URL: {{base_url}}/users/{{newUserId}}
```

Chạy Request 1 trước, sau đó chạy Request 2 để verify user đã được tạo!

> **⚠️ Lưu ý:** Với JSONPlaceholder (fake API), Request 2 sẽ 404 vì không thực sự tạo user. Nhưng với real API, flow này hoạt động!

## 10. POST với Form Data

### Khi nào dùng form-data?

- Upload files
- Submit HTML forms
- Multipart requests

### Example: Create User với form-data

```
Method: POST
URL: {{base_url}}/users

Body Type: form-data

Key       | Value
----------|------------------
name      | John Doe
email     | john@example.com
username  | johndoe
```

**Content-Type tự động:** `multipart/form-data`

## 11. Common POST Errors

| Error | Nguyên nhân | Giải pháp |
|-------|-------------|-----------|
| 400 Bad Request | Invalid JSON syntax | Check JSON với Beautify |
| 401 Unauthorized | Thiếu auth token | Thêm Authorization header |
| 403 Forbidden | Không có quyền | Check permissions |
| 422 Unprocessable | Thiếu required fields | Thêm đầy đủ fields |
| 500 Server Error | Lỗi server | Báo dev team |

## 12. Bài Tập Thực Hành

### Bài 1: CRUD Flow - Create

1. **Create User**
   ```json
   {
     "name": "Your Name",
     "username": "yourusername",
     "email": "your@email.com"
   }
   ```

2. **Create Post cho user đó**
   ```json
   {
     "userId": 1,
     "title": "My Awesome Post",
     "body": "Post content here..."
   }
   ```

3. **Create Comment cho post**
   ```json
   {
     "postId": 1,
     "name": "Nice post!",
     "email": "commenter@example.com",
     "body": "Great content!"
   }
   ```

### Bài 2: Random Data Generation

Tạo request với **tất cả** data random:

**Pre-request Script:**
```javascript
pm.environment.set("randomTitle", pm.variables.replaceIn("{{$randomCatchPhrase}}"));
pm.environment.set("randomBody", pm.variables.replaceIn("{{$randomLoremParagraph}}"));
pm.environment.set("randomUserId", Math.floor(Math.random() * 10) + 1);
```

**Body:**
```json
{
  "userId": {{randomUserId}},
  "title": "{{randomTitle}}",
  "body": "{{randomBody}}"
}
```

Gửi 5 lần và quan sát data khác nhau!

### Bài 3: Create Multiple Todos

Tạo 5 todos với status khác nhau:

```json
// Todo 1
{
  "userId": 1,
  "title": "Learn GET requests",
  "completed": true
}

// Todo 2
{
  "userId": 1,
  "title": "Learn POST requests",
  "completed": true
}

// Todo 3
{
  "userId": 1,
  "title": "Learn PUT requests",
  "completed": false
}
```

### Bài 4: Validation Tests

Tạo collection "POST Validation Tests" với:

1. Valid post (expect 201)
2. Missing title (expect 400/422 với real API)
3. Missing body (expect 400/422)
4. Invalid userId type (expect 400/422)
5. Empty body {} (expect 400/422)

## 13. Best Practices

### ✅ DO

- Always set `Content-Type: application/json`
- Verify status code = 201 hoặc 200
- Verify response contains the created resource
- Save created ID to environment variable
- Test both valid and invalid data
- Use variables for dynamic data

### ❌ DON'T

- Forget to set Content-Type header
- Hardcode data in body (use variables)
- Skip verification of created resource
- Assume POST always returns 201 (có thể 200)
- Send sensitive data in plaintext

## 14. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ Cấu hình POST requests
- ✅ Gửi JSON body
- ✅ Verify responses
- ✅ Test với data hợp lệ và không hợp lệ
- ✅ Sử dụng variables cho dynamic data
- ✅ Chain requests
- ✅ Handle errors

## 15. Kiến Thức Cần Nhớ

| Khái niệm | Giải thích |
|-----------|------------|
| **POST** | HTTP method để tạo resource mới |
| **201 Created** | Status code khi tạo thành công |
| **Content-Type** | Header chỉ định format của body |
| **Request Body** | Dữ liệu JSON của resource mới |
| **Response** | Chứa resource vừa tạo + ID |

## Next Steps

Tiếp tục học:
- **Bài tiếp theo**: [4.3 Test PUT và PATCH Requests](./test-put-patch-requests.md)

---

[⬅️ Test GET Requests](./test-get-requests.md) | [Tổng Quan Chương 4](./README.md) | [Tiếp Theo: PUT & PATCH ➡️](./test-put-patch-requests.md)
