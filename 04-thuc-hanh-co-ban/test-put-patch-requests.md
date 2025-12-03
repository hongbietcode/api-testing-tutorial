# 4.3 Test PUT và PATCH Requests

PUT và PATCH dùng để **cập nhật** resources. Đây là bài học quan trọng để hiểu sự khác biệt giữa 2 methods này.

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Hiểu sự khác biệt giữa PUT và PATCH
- ✅ Test PUT để update toàn bộ resource
- ✅ Test PATCH để update một phần resource
- ✅ Verify data được cập nhật chính xác
- ✅ Handle update errors

## 1. PUT vs PATCH - Khác Nhau Thế Nào?

### PUT - Update Toàn Bộ (Replace)

**PUT** thay thế **toàn bộ** resource với data mới.

**Ví dụ:**

**Resource hiện tại:**
```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "age": 30
}
```

**PUT Request:**
```json
{
  "name": "Jane Smith",
  "email": "jane@example.com"
}
```

**Kết quả:**
```json
{
  "id": 1,
  "name": "Jane Smith",
  "email": "jane@example.com"
  // "age" bị mất vì không có trong PUT request
}
```

### PATCH - Update Một Phần (Partial Update)

**PATCH** chỉ cập nhật **những fields** được gửi lên.

**Resource hiện tại:**
```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "age": 30
}
```

**PATCH Request:**
```json
{
  "email": "newemail@example.com"
}
```

**Kết quả:**
```json
{
  "id": 1,
  "name": "John Doe",           // Giữ nguyên
  "email": "newemail@example.com", // Đã update
  "age": 30                      // Giữ nguyên
}
```

### So Sánh

| Tiêu chí | PUT | PATCH |
|----------|-----|-------|
| **Mục đích** | Replace toàn bộ | Update một phần |
| **Fields không gửi** | Bị xóa/reset | Giữ nguyên |
| **Idempotent** | Có | Có (thường) |
| **Use case** | Update hoàn toàn | Update vài fields |

## 2. Thực Hành PUT - Update Toàn Bộ User

### Request 1: Get User Hiện Tại

Trước khi update, hãy xem user hiện tại:

```
Method: GET
URL: {{base_url}}/users/1
```

**Response:**
```json
{
  "id": 1,
  "name": "Leanne Graham",
  "username": "Bret",
  "email": "Sincere@april.biz",
  "address": {
    "street": "Kulas Light",
    "city": "Gwenborough"
  },
  "phone": "1-770-736-8031 x56442",
  "website": "hildegard.org",
  "company": {
    "name": "Romaguera-Crona"
  }
}
```

### Request 2: PUT Update User

```
Method: PUT
URL: {{base_url}}/users/1

Headers:
Content-Type: application/json

Body:
{
  "id": 1,
  "name": "Updated Name",
  "username": "updateduser",
  "email": "updated@example.com"
}
```

### Expected Response

**Status:** `200 OK`

**Body:**
```json
{
  "id": 1,
  "name": "Updated Name",
  "username": "updateduser",
  "email": "updated@example.com"
}
```

**Lưu ý:**
- Tất cả fields cũ (address, phone, website, company) không còn
- Chỉ còn fields trong PUT request

### Verify Update

**Tests:**
```javascript
pm.test("Status code is 200", function() {
    pm.response.to.have.status(200);
});

const response = pm.response.json();

pm.test("Name is updated", function() {
    pm.expect(response.name).to.eql("Updated Name");
});

pm.test("Email is updated", function() {
    pm.expect(response.email).to.eql("updated@example.com");
});

pm.test("ID remains the same", function() {
    pm.expect(response.id).to.eql(1);
});
```

## 3. Thực Hành PATCH - Update Một Phần

### Request: PATCH Update Only Email

```
Method: PATCH
URL: {{base_url}}/users/1

Headers:
Content-Type: application/json

Body:
{
  "email": "newemail@example.com"
}
```

### Expected Response

**Status:** `200 OK`

**Body:**
```json
{
  "id": 1,
  "name": "Leanne Graham",           // Giữ nguyên
  "username": "Bret",                // Giữ nguyên
  "email": "newemail@example.com",   // Đã update
  "address": { ... },                 // Giữ nguyên
  "phone": "1-770-736-8031 x56442",  // Giữ nguyên
  "website": "hildegard.org",        // Giữ nguyên
  "company": { ... }                  // Giữ nguyên
}
```

### Verify Partial Update

```javascript
pm.test("Status code is 200", function() {
    pm.response.to.have.status(200);
});

const response = pm.response.json();

pm.test("Email is updated", function() {
    pm.expect(response.email).to.eql("newemail@example.com");
});

pm.test("Other fields remain unchanged", function() {
    pm.expect(response.name).to.eql("Leanne Graham");
    pm.expect(response.username).to.eql("Bret");
});
```

## 4. Thực Hành: Update Posts

### PUT - Update Entire Post

```
Method: PUT
URL: {{base_url}}/posts/1

Body:
{
  "id": 1,
  "title": "Completely New Title",
  "body": "Completely new content for this post.",
  "userId": 1
}
```

**Expected:**
```json
{
  "id": 1,
  "title": "Completely New Title",
  "body": "Completely new content for this post.",
  "userId": 1
}
```

### PATCH - Update Only Title

```
Method: PATCH
URL: {{base_url}}/posts/1

Body:
{
  "title": "Updated Title Only"
}
```

**Expected:**
```json
{
  "id": 1,
  "title": "Updated Title Only",    // Updated
  "body": "...",                     // Original content
  "userId": 1                        // Original userId
}
```

## 5. Thực Hành: Update Todos

### PATCH - Toggle Todo Completion

```
Method: PATCH
URL: {{base_url}}/todos/1

Body:
{
  "completed": true
}
```

**Expected Response:**
```json
{
  "userId": 1,
  "id": 1,
  "title": "delectus aut autem",
  "completed": true    // Changed from false to true
}
```

### Common Use Case: Mark as Complete

**Pre-request Script** (Get current status):
```javascript
// This would typically get current todo first
// For demo, we'll just toggle
const currentStatus = pm.environment.get("todoCompleted") || false;
pm.environment.set("todoCompleted", !currentStatus);
```

**Body:**
```json
{
  "completed": {{todoCompleted}}
}
```

## 6. Update với Variables

### Save Original Data

**GET User First:**
```javascript
// Tests tab
const user = pm.response.json();
pm.environment.set("originalName", user.name);
pm.environment.set("originalEmail", user.email);
```

### Update Some Fields

**PATCH Request:**
```json
{
  "email": "{{$randomEmail}}",
  "website": "{{$randomDomainName}}"
}
```

### Verify Original Data Preserved

```javascript
const response = pm.response.json();

pm.test("Name unchanged", function() {
    pm.expect(response.name).to.eql(pm.environment.get("originalName"));
});

pm.test("Email updated", function() {
    pm.expect(response.email).to.not.eql(pm.environment.get("originalEmail"));
});
```

## 7. Update Nested Objects

### PUT - Update User Address

```
Method: PUT
URL: {{base_url}}/users/1

Body:
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "address": {
    "street": "New Street 123",
    "city": "New City",
    "zipcode": "12345"
  }
}
```

### PATCH - Update Only Address

```
Method: PATCH
URL: {{base_url}}/users/1

Body:
{
  "address": {
    "city": "Updated City"
  }
}
```

**Lưu ý:** Behavior của nested objects tùy vào API implementation:
- Một số API merge nested objects
- Một số API replace toàn bộ nested object

## 8. Error Handling

### Update Non-existent Resource

```
Method: PUT
URL: {{base_url}}/users/9999

Body:
{
  "name": "Does Not Exist"
}
```

**Expected:**
```
Status: 404 Not Found
```

**Tests:**
```javascript
pm.test("Status code is 404", function() {
    pm.response.to.have.status(404);
});
```

### Update Without Required Fields

```
Method: PUT
URL: {{base_url}}/users/1

Body:
{
  "id": 1
  // Missing name, email, etc.
}
```

**Expected:** Với real API, có thể 400 hoặc 422.

**Tests:**
```javascript
pm.test("Status code indicates error", function() {
    pm.expect([400, 422]).to.include(pm.response.code);
});
```

### Invalid Data Type

```
Method: PATCH
URL: {{base_url}}/todos/1

Body:
{
  "completed": "yes"  // Should be boolean, not string
}
```

## 9. Idempotency

**PUT và PATCH** nên **idempotent**: gửi nhiều lần cho cùng kết quả.

### Test Idempotency

**Gửi request này 3 lần:**
```
Method: PUT
URL: {{base_url}}/users/1

Body:
{
  "id": 1,
  "name": "Same Name",
  "email": "same@email.com"
}
```

**Kết quả lần 1, 2, 3:** Giống nhau!

```json
{
  "id": 1,
  "name": "Same Name",
  "email": "same@email.com"
}
```

## 10. Chain Requests: Create → Update → Get

### Step 1: Create User (POST)

```javascript
// Tests
const response = pm.response.json();
pm.environment.set("userId", response.id);
```

### Step 2: Update User (PUT)

```
URL: {{base_url}}/users/{{userId}}

Body:
{
  "name": "Updated After Creation"
}
```

### Step 3: Verify Update (GET)

```
URL: {{base_url}}/users/{{userId}}
```

```javascript
// Tests
const user = pm.response.json();
pm.test("Name was updated", function() {
    pm.expect(user.name).to.eql("Updated After Creation");
});
```

## 11. Bài Tập Thực Hành

### Bài 1: PUT vs PATCH Comparison

Với user ID=1:

1. **GET** user hiện tại, lưu original data
2. **PUT** update toàn bộ:
   ```json
   {
     "id": 1,
     "name": "New Name",
     "email": "new@email.com"
   }
   ```
3. Verify: chỉ còn name và email
4. **PATCH** update một phần:
   ```json
   {
     "phone": "123-456-7890"
   }
   ```
5. Verify: name, email giữ nguyên, thêm phone

### Bài 2: Update Post Flow

1. GET post ID=1
2. PATCH update title: "My Updated Title"
3. Verify: title mới, body cũ
4. PUT replace toàn bộ:
   ```json
   {
     "id": 1,
     "userId": 1,
     "title": "Completely New",
     "body": "Completely New Content"
   }
   ```
5. Verify: tất cả fields mới

### Bài 3: Todo Status Toggle

Tạo collection "Todo Management":

1. GET todo ID=1, check completed status
2. PATCH toggle completed: `true` → `false` hoặc ngược lại
3. GET lại để verify
4. PATCH toggle lại lần nữa
5. Verify trở về original status

### Bài 4: Update Validation

Test các scenarios:

1. PUT với ID không tồn tại → expect 404
2. PATCH với empty body `{}` → expect ?
3. PUT với invalid JSON → expect 400
4. PATCH với ID mismatch:
   ```
   URL: /users/1
   Body: { "id": 2, "name": "Test" }
   ```
   → expect error

### Bài 5: Batch Updates

Update nhiều users liên tiếp:

```javascript
// Pre-request Script
const userIds = [1, 2, 3, 4, 5];
const currentId = userIds[pm.info.iteration % userIds.length];
pm.environment.set("userId", currentId);
```

```
URL: {{base_url}}/users/{{userId}}
Body: { "email": "updated{{userId}}@example.com" }
```

Dùng Collection Runner, iterations = 5

## 12. Best Practices

### Khi Nào Dùng PUT?

✅ **Dùng PUT khi:**
- Update toàn bộ resource
- Replace tất cả fields
- Client biết toàn bộ resource structure
- Cần idempotency nghiêm ngặt

### Khi Nào Dùng PATCH?

✅ **Dùng PATCH khi:**
- Update một vài fields
- Không muốn ảnh hưởng fields khác
- Tiết kiệm bandwidth (ít data hơn)
- Update partial/incremental

### ✅ DO

- Luôn include ID trong URL
- Verify response chứa updated data
- Test both PUT and PATCH nếu API support cả 2
- Include ID trong body cho PUT
- Use Content-Type: application/json
- Verify unchanged fields với PATCH

### ❌ DON'T

- Dùng PUT khi chỉ muốn update 1-2 fields
- Quên ID trong URL
- Assume API support cả PUT và PATCH
- Update sensitive fields (password) qua plain PATCH
- Skip verification

## 13. Common Errors

| Error | Nguyên nhân | Giải pháp |
|-------|-------------|-----------|
| 404 | Resource không tồn tại | Check ID exists |
| 400 | Invalid JSON | Validate JSON syntax |
| 422 | Validation failed | Check required fields |
| 409 | Conflict (duplicate) | Use different data |
| 401 | No authentication | Add auth token |

## 14. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ Sự khác biệt giữa PUT và PATCH
- ✅ PUT replaces toàn bộ resource
- ✅ PATCH updates một phần resource
- ✅ Verify updates chính xác
- ✅ Handle update errors
- ✅ Chain requests: Create → Update → Get
- ✅ Test idempotency

## 15. Kiến Thức Cần Nhớ

| Khái niệm | Giải thích |
|-----------|------------|
| **PUT** | Replace toàn bộ resource |
| **PATCH** | Update một phần resource |
| **Idempotent** | Gửi nhiều lần = 1 lần |
| **200 OK** | Update thành công |
| **404** | Resource không tồn tại |

## Next Steps

Tiếp tục học:
- **Bài tiếp theo**: [4.4 Test DELETE Requests](./test-delete-requests.md)

---

[⬅️ Test POST Requests](./test-post-requests.md) | [Tổng Quan Chương 4](./README.md) | [Tiếp Theo: DELETE ➡️](./test-delete-requests.md)
