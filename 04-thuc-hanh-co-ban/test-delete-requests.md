# 4.4 Test DELETE Requests

DELETE method dùng để **xóa** resources. Đây là bài học cuối cùng về CRUD operations.

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Gửi DELETE requests
- ✅ Verify status code 200/204
- ✅ Verify resource đã bị xóa
- ✅ Test xóa resource không tồn tại (404)
- ✅ Handle delete errors
- ✅ Hoàn thành CRUD cycle

## 1. DELETE Request Cơ Bản

### Cấu Trúc DELETE Request

```
DELETE /users/123
```

**Đặc điểm:**
- Method: DELETE
- URL chứa ID của resource cần xóa
- Thường **không có** request body
- Response có thể empty hoặc chứa deleted resource

### Status Codes

| Code | Ý nghĩa | Response Body |
|------|---------|---------------|
| **200 OK** | Xóa thành công | Có (deleted resource) |
| **204 No Content** | Xóa thành công | Không có (empty) |
| **404 Not Found** | Resource không tồn tại | Error message |

## 2. Thực Hành 1: Delete User

### Request: Delete User

```
Method: DELETE
URL: {{base_url}}/users/1

Headers:
(Không cần thêm gì đặc biệt)
```

### Expected Response

**Status:** `200 OK`

**Body:** `{}` (empty object)

JSONPlaceholder trả về empty object, nhưng một số API có thể trả:
- 204 No Content (no body)
- 200 OK với deleted resource
- 200 OK với success message

### Verify Deletion

**Tests:**
```javascript
pm.test("Status code is 200 or 204", function() {
    pm.expect([200, 204]).to.include(pm.response.code);
});

pm.test("Response time is acceptable", function() {
    pm.expect(pm.response.responseTime).to.be.below(2000);
});

// Nếu có body, verify it's empty hoặc chứa deleted data
if (pm.response.code === 200 && pm.response.text()) {
    pm.test("Response is empty or contains deleted data", function() {
        const response = pm.response.json();
        // Empty object
        if (Object.keys(response).length === 0) {
            pm.expect(response).to.be.an('object');
        }
    });
}
```

## 3. Thực Hành 2: Delete Post

### Request: Delete Post

```
Method: DELETE
URL: {{base_url}}/posts/1
```

### Expected Response

**Status:** `200 OK`
**Body:** `{}`

### Complete Test Suite

```javascript
// Test 1: Status code
pm.test("Status code is 200", function() {
    pm.response.to.have.status(200);
});

// Test 2: Response time
pm.test("Response time < 1000ms", function() {
    pm.expect(pm.response.responseTime).to.be.below(1000);
});

// Test 3: Content-Type
pm.test("Content-Type is JSON", function() {
    pm.expect(pm.response.headers.get("Content-Type")).to.include("application/json");
});

console.log("Post deleted successfully");
```

## 4. Thực Hành 3: Delete Todo

### Request: Delete Todo

```
Method: DELETE
URL: {{base_url}}/todos/1
```

### Tests với Logging

```javascript
const postId = pm.variables.get("todoId") || 1;

pm.test(`DELETE todo ${postId} successful`, function() {
    pm.response.to.have.status(200);
});

console.log(`✅ Deleted todo ID: ${postId}`);
console.log(`Response time: ${pm.response.responseTime}ms`);
```

## 5. Delete Non-existent Resource

### Request: Delete với Invalid ID

```
Method: DELETE
URL: {{base_url}}/users/9999
```

### Expected Response

**Status:** `404 Not Found`

Một số API có thể trả:
- 404 Not Found
- 400 Bad Request
- 200 OK (idempotent delete)

### Tests

```javascript
pm.test("Status code is 404", function() {
    pm.response.to.have.status(404);
});

pm.test("Error message exists", function() {
    // JSONPlaceholder có thể trả empty, nhưng real API nên có message
    const response = pm.response.json();
    console.log("Error response:", response);
});
```

## 6. Chain Requests: Create → Verify → Delete → Verify

### Complete CRUD Flow

**Step 1: Create User (POST)**
```javascript
// Tests
const newUser = pm.response.json();
pm.environment.set("createdUserId", newUser.id);
console.log("✅ Created user ID:", newUser.id);
```

**Step 2: Verify User Exists (GET)**
```
URL: {{base_url}}/users/{{createdUserId}}
```

```javascript
// Tests
pm.test("User exists", function() {
    pm.response.to.have.status(200);
});
```

**Step 3: Delete User (DELETE)**
```
URL: {{base_url}}/users/{{createdUserId}}
```

```javascript
// Tests
pm.test("Delete successful", function() {
    pm.expect([200, 204]).to.include(pm.response.code);
});
console.log("✅ Deleted user ID:", pm.environment.get("createdUserId"));
```

**Step 4: Verify User Deleted (GET)**
```
URL: {{base_url}}/users/{{createdUserId}}
```

```javascript
// Tests
pm.test("User no longer exists", function() {
    pm.response.to.have.status(404);
});
console.log("✅ Verified: user is deleted");
```

> **⚠️ Lưu ý:** JSONPlaceholder là fake API nên Step 4 có thể không trả 404. Với real API, flow này hoạt động đúng!

## 7. Delete với Query Parameters

Một số APIs cho phép batch delete:

### Delete Multiple Items

```
Method: DELETE
URL: {{base_url}}/users?ids=1,2,3
```

hoặc

```
URL: {{base_url}}/posts?userId=1
```

(Delete tất cả posts của user 1)

**Lưu ý:** JSONPlaceholder không support batch delete, nhưng nhiều real APIs có.

## 8. Soft Delete vs Hard Delete

### Hard Delete (Permanent)

Resource bị **xóa vĩnh viễn** khỏi database.

```
DELETE /users/123
→ User 123 không còn trong DB
```

### Soft Delete (Logical Delete)

Resource được **đánh dấu là deleted** nhưng vẫn còn trong DB.

```
PATCH /users/123
{
  "deleted": true,
  "deletedAt": "2024-01-15T10:30:00Z"
}
```

GET `/users/123` có thể:
- Trả 404 (ẩn deleted items)
- Trả user với `deleted: true`

**Khi nào test soft delete:**
- Verify `deleted` field = true
- Verify `deletedAt` timestamp
- Verify GET returns 404 hoặc filtered out

## 9. Delete với Authentication

Real APIs thường yêu cầu authentication:

### Delete Request với Auth

```
Method: DELETE
URL: {{apiUrl}}/users/123

Headers:
Authorization: Bearer {{accessToken}}
```

### Unauthorized Delete

```
Method: DELETE
URL: {{apiUrl}}/users/123

Headers:
(Không có Authorization)
```

**Expected:**
```
Status: 401 Unauthorized
```

```javascript
pm.test("Unauthorized without token", function() {
    pm.response.to.have.status(401);
});
```

### Forbidden Delete (Insufficient Permissions)

```
DELETE /users/999
Authorization: Bearer <user_token>
```

User chỉ có thể xóa chính mình, không thể xóa user khác.

**Expected:**
```
Status: 403 Forbidden
```

```javascript
pm.test("Forbidden to delete other users", function() {
    pm.response.to.have.status(403);
});
```

## 10. Idempotency

DELETE nên **idempotent**: xóa 1 lần hay nhiều lần, kết quả giống nhau.

### Test Idempotency

**Lần 1: Delete user 123**
```
DELETE /users/123
→ 200 OK (hoặc 204)
```

**Lần 2: Delete lại user 123**
```
DELETE /users/123
→ 404 Not Found (already deleted)
hoặc
→ 200 OK (idempotent delete)
```

Tùy vào API design:
- **404**: User không còn, không thể delete
- **200/204**: Idempotent, delete lần nào cũng OK

### Test Script

```javascript
// Delete lần đầu
pm.sendRequest({
    url: pm.variables.get("base_url") + "/users/1",
    method: "DELETE"
}, function(err, res) {
    console.log("First delete:", res.code);

    // Delete lần 2
    pm.sendRequest({
        url: pm.variables.get("base_url") + "/users/1",
        method: "DELETE"
    }, function(err, res2) {
        console.log("Second delete:", res2.code);
        pm.test("Idempotent delete", function() {
            // Either 200 (idempotent) or 404 (not found) is acceptable
            pm.expect([200, 404]).to.include(res2.code);
        });
    });
});
```

## 11. Bulk Delete Operations

### Delete All Posts by User

Nếu API support:

```
DELETE /posts?userId=1
```

hoặc qua request body:

```
Method: DELETE
URL: {{base_url}}/posts/bulk

Body:
{
  "ids": [1, 2, 3, 4, 5]
}
```

### Loop Delete với Collection Runner

**Pre-request Script:**
```javascript
const postIds = [1, 2, 3, 4, 5];
const currentId = postIds[pm.info.iteration];
pm.environment.set("postId", currentId);
```

**Request:**
```
DELETE {{base_url}}/posts/{{postId}}
```

**Collection Runner Settings:**
- Iterations: 5
- Delay: 100ms

## 12. Bài Tập Thực Hành

### Bài 1: Complete CRUD Cycle

Tạo collection "Complete CRUD Test":

1. **CREATE** (POST)
   ```json
   POST /users
   {
     "name": "Test User",
     "email": "test@example.com"
   }
   ```
   Save `userId` to environment

2. **READ** (GET)
   ```
   GET /users/{{userId}}
   ```
   Verify user exists

3. **UPDATE** (PUT)
   ```json
   PUT /users/{{userId}}
   {
     "name": "Updated User",
     "email": "updated@example.com"
   }
   ```
   Verify update

4. **DELETE** (DELETE)
   ```
   DELETE /users/{{userId}}
   ```
   Verify deletion

5. **VERIFY DELETED** (GET)
   ```
   GET /users/{{userId}}
   ```
   Expect 404

### Bài 2: Delete Error Scenarios

Tạo collection "Delete Error Tests":

1. Delete non-existent user → expect 404
2. Delete with invalid ID format → expect 400
3. Delete với negative ID → expect 400
4. Delete root resource → expect 405 (Method Not Allowed)

### Bài 3: Delete Todos Flow

1. GET all todos của user 1
2. Lưu todo IDs vào array
3. Loop delete từng todo
4. Verify mỗi todo bị xóa
5. GET lại all todos, verify count giảm

### Bài 4: Idempotent Delete Test

1. Create new post
2. Delete post → expect 200
3. Delete lại lần 2 → expect 404 hoặc 200
4. Delete lần 3 → expect 404 hoặc 200
5. Verify idempotency

### Bài 5: Batch Operations

Với từng resource type (users, posts, todos):

1. Create 5 items
2. Verify tất cả tồn tại
3. Delete tất cả
4. Verify tất cả đã xóa

Dùng Collection Runner với data file CSV:

**delete-test-data.csv:**
```csv
userId
1
2
3
4
5
```

## 13. Best Practices

### ✅ DO

- Verify status code (200, 204, hoặc 404)
- Test delete non-existent resources
- Test idempotency (delete nhiều lần)
- Verify resource không còn tồn tại sau delete
- Use appropriate authentication
- Log delete operations
- Test delete permissions

### ❌ DON'T

- Delete production data by mistake
- Skip verification after delete
- Assume DELETE always returns 200
- Delete without checking if resource exists
- Hardcode IDs trong delete requests
- Ignore 404 errors

## 14. Security Considerations

### Prevent Accidental Deletes

```javascript
// Pre-request Script: Safety check
const env = pm.environment.name;
if (env === "Production") {
    throw new Error("🚨 STOP! Cannot delete in Production environment!");
}
```

### Require Confirmation

```javascript
// Pre-request Script
const confirmDelete = pm.variables.get("confirmDelete");
if (!confirmDelete || confirmDelete !== "YES") {
    throw new Error("Delete not confirmed. Set 'confirmDelete' variable to 'YES'");
}
```

## 15. Common Errors

| Error | Nguyên nhân | Giải pháp |
|-------|-------------|-----------|
| 404 | Resource không tồn tại | Check ID exists first |
| 401 | No authentication | Add auth token |
| 403 | No permission | Check user permissions |
| 405 | Method not allowed | Check API supports DELETE |
| 409 | Conflict (có dependencies) | Delete dependencies first |

## 16. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ Gửi DELETE requests
- ✅ Verify status codes (200, 204, 404)
- ✅ Verify resource đã xóa
- ✅ Test idempotency
- ✅ Handle delete errors
- ✅ Complete CRUD cycle (Create → Read → Update → Delete)

**Chúc mừng! Bạn đã hoàn thành CRUD operations!** 🎉

## 17. CRUD Summary Table

| Operation | Method | Purpose | Status |
|-----------|--------|---------|--------|
| **Create** | POST | Tạo mới resource | 201 |
| **Read** | GET | Lấy resource | 200 |
| **Update** | PUT/PATCH | Cập nhật resource | 200 |
| **Delete** | DELETE | Xóa resource | 200/204 |

## 18. Kiến Thức Cần Nhớ

| Khái niệm | Giải thích |
|-----------|------------|
| **DELETE** | HTTP method để xóa resource |
| **200 OK** | Xóa thành công, có response body |
| **204 No Content** | Xóa thành công, không có body |
| **404 Not Found** | Resource không tồn tại |
| **Idempotent** | Xóa nhiều lần = 1 lần |
| **Hard Delete** | Xóa vĩnh viễn |
| **Soft Delete** | Đánh dấu deleted |

## Next Steps

Hoàn thành Chương 4! Bây giờ bạn đã thành thạo:
- ✅ GET - Đọc dữ liệu
- ✅ POST - Tạo mới
- ✅ PUT/PATCH - Cập nhật
- ✅ DELETE - Xóa

Tiếp tục:
- **Chương tiếp theo**: [Chương 5: Authentication & Authorization](../05-xac-thuc-authorization/README.md)

---

[⬅️ Test PUT & PATCH Requests](./test-put-patch-requests.md) | [Tổng Quan Chương 4](./README.md) | [Tiếp Theo: Chương 5 ➡️](../05-xac-thuc-authorization/README.md)
