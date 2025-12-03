# 6.5 Mock Servers

Mock Servers cho phép tạo fake APIs để test khi backend chưa sẵn sàng hoặc để tạo controlled test environments.

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Hiểu Mock Servers là gì và tại sao cần
- ✅ Tạo mock server từ collection
- ✅ Define mock responses
- ✅ Use mock URLs trong tests
- ✅ Use cases thực tế

## 1. Mock Server Là Gì?

**Mock Server** là fake API server mà Postman tự động tạo dựa trên collection của bạn.

### Tại Sao Cần Mock Servers?

**Vấn đề:**
- ❌ Backend chưa phát triển xong
- ❌ API đang down/maintenance
- ❌ Cần test specific scenarios (errors, edge cases)
- ❌ Không muốn affect production data

**Giải pháp: Mock Server**
- ✅ Frontend có thể develop song song với backend
- ✅ Tests không depend on real API
- ✅ Control responses chính xác
- ✅ Test error cases dễ dàng

> **📸 HÌNH ẢNH:** Mock Server Concept
> - File: `mock-server-concept.png`
> - Nội dung: Diagram showing: Frontend → Mock Server → Returns predefined responses (không cần real backend)

<!-- IMAGE_PLACEHOLDER: mock-server-concept.png -->

## 2. Tạo Mock Server

### Bước 1: Prepare Collection

Tạo collection với các requests và **save example responses**.

**Example Request:**
```
GET /users/1
```

**Save Example Response:**
```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com"
}
```

### Bước 2: Create Mock Server

> **📸 HÌNH ẢNH:** Create Mock Server Dialog
> - File: `create-mock-server.png`
> - Nội dung: Screenshot of "Create Mock Server" dialog với fields: mock server name, environment selection, và options

<!-- IMAGE_PLACEHOLDER: create-mock-server.png -->

**Cách 1: Từ Collection**
1. Right-click collection
2. Chọn **"Mock Collection"**
3. Đặt tên mock server
4. Chọn environment (optional)
5. Click **"Create Mock Server"**

**Cách 2: Từ Mock Servers Tab**
1. Click **"Mock Servers"** trong sidebar
2. Click **"Create Mock Server"**
3. Select collection
4. Configure và create

### Bước 3: Get Mock URL

Postman generates unique mock URL:

```
https://[mock-id].mock.pstmn.io
```

**Ví dụ:**
```
https://1234abcd-5678-efgh-90ij-klmnopqrstuv.mock.pstmn.io
```

## 3. Saving Example Responses

### Save Response as Example

**Sau khi có real response:**
1. Send request và nhận response
2. Click **"Save Response"**
3. Chọn **"Save as example"**
4. Đặt tên example (VD: "Success Response")
5. Save

### Manually Create Example

1. Click vào request
2. Tab **"Examples"**
3. Click **"Add Example"**
4. Fill:
   - Example name
   - Status code
   - Response body
5. Save

### Multiple Examples

Tạo nhiều examples cho different scenarios:

**Example 1: Success**
```
Status: 200 OK
Body:
{
  "id": 1,
  "name": "John Doe"
}
```

**Example 2: Not Found**
```
Status: 404 Not Found
Body:
{
  "error": "User not found"
}
```

**Example 3: Server Error**
```
Status: 500 Internal Server Error
Body:
{
  "error": "Internal server error"
}
```

## 4. Using Mock Server

### Replace Base URL

**Before (Real API):**
```
{{baseUrl}}/users/1
```

**After (Mock):**
```
{{mockUrl}}/users/1
```

### Set Mock URL trong Environment

```
mockUrl = https://1234abcd.mock.pstmn.io
```

### Make Request

```
GET {{mockUrl}}/users/1
```

**Response:** Example response bạn đã save! 🎉

## 5. Example Matching

Mock server chọn example dựa trên:

### 1. Request Method

```
GET /users → Returns GET example
POST /users → Returns POST example
```

### 2. Request Path

```
/users/1 → Returns example for /users/1
/users/2 → Returns example for /users/2 (if saved)
```

### 3. Query Parameters (Advanced)

```
/users?status=active → Example với query param
```

### Default Behavior

Nếu không match chính xác → returns first example

## 6. Mock Server Settings

### Response Delay

Simulate network latency:

1. Mở Mock Server settings
2. Set **"Delay"**: 500ms
3. Save

Mọi responses sẽ delay 500ms (giống real network).

### Match Algorithm

- **Exact match**: Phải match chính xác path và method
- **Closest match**: Tìm example gần nhất

## 7. Practical Use Cases

### Use Case 1: Frontend Development

**Scenario:** Backend chưa ready, frontend cần develop.

**Solution:**
1. Tạo mock server với expected API structure
2. Frontend team dùng mock URL
3. Develop UI/UX mà không cần wait backend
4. Khi backend ready, chỉ cần đổi URL

### Use Case 2: Testing Error Scenarios

**Scenario:** Cần test app handle 500 errors.

**Solution:**
```javascript
// Pre-request Script
// Toggle giữa success và error
const testError = pm.environment.get("testError");

if (testError === "true") {
    // Use example with 500 error
    postman.setNextRequest("Get User - 500 Error Example");
} else {
    postman.setNextRequest("Get User - Success Example");
}
```

### Use Case 3: Demo/Presentation

**Scenario:** Demo app nhưng không muốn dùng production data.

**Solution:**
- Mock server với controlled, safe data
- Không risk expose real customer data
- Responses luôn consistent

### Use Case 4: Integration Testing

**Scenario:** Test integration với third-party API.

**Solution:**
- Mock third-party API responses
- No API key needed
- No rate limits
- Consistent results

## 8. Advanced Mock Features

### Dynamic Responses

Sử dụng variables trong example responses:

```json
{
  "id": {{userId}},
  "timestamp": "{{$timestamp}}",
  "requestId": "{{$guid}}"
}
```

### Conditional Examples

Based on headers hoặc query params (Postman Pro feature).

## 9. Mock vs Real API

### Khi Nào Dùng Mock

✅ **Dùng Mock khi:**
- Backend chưa ready
- Testing error scenarios
- Cần consistent data
- Offline development
- Demo/presentation
- Integration testing

### Khi Nào Dùng Real API

✅ **Dùng Real khi:**
- End-to-end testing
- Performance testing
- Validation với real data
- Production readiness
- Integration verification

### Best Practice: Both!

```javascript
// Environment variables
const useMock = pm.environment.get("useMock");

const baseUrl = useMock === "true"
    ? pm.environment.get("mockUrl")
    : pm.environment.get("realApiUrl");

pm.variables.set("baseUrl", baseUrl);
```

Easy toggle giữa mock và real!

## 10. Limitations

### Mock Server Limitations

- ❌ Không có business logic
- ❌ Không validate input
- ❌ Không persist data
- ❌ Static responses only
- ❌ Limited request matching

### Not a Replacement

Mock servers **KHÔNG thay thế** real API testing. Chỉ là tool bổ sung.

## 11. Debugging Mock Servers

### Check Mock URL

```bash
curl https://[mock-id].mock.pstmn.io/users/1
```

### View Mock Logs

1. Open Mock Server trong Postman
2. Tab **"Call Logs"**
3. See all requests made to mock

> **📸 HÌNH ẢNH:** Mock Server Call Logs
> - File: `mock-server-logs.png`
> - Nội dung: Screenshot showing call logs với timestamp, method, path, status code của các requests đến mock server

<!-- IMAGE_PLACEHOLDER: mock-server-logs.png -->

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| 404 | No example saved | Save example response |
| Wrong response | Wrong example match | Check example path |
| Slow response | Network issue | Check internet connection |
| Empty response | Example not configured | Add response body |

## 12. Example: Complete Mock Setup

### Step-by-Step

**1. Create Collection: "User API"**

**2. Add Requests:**
```
GET /users
GET /users/:id
POST /users
PUT /users/:id
DELETE /users/:id
```

**3. Save Example Responses:**

**GET /users:**
```json
{
  "users": [
    {"id": 1, "name": "John"},
    {"id": 2, "name": "Jane"}
  ]
}
```

**GET /users/1:**
```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com"
}
```

**POST /users:**
```
Status: 201 Created
{
  "id": 3,
  "name": "New User",
  "email": "new@example.com"
}
```

**4. Create Mock Server:**
- Name: "User API Mock"
- From collection: "User API"

**5. Get Mock URL:**
```
https://abcd1234.mock.pstmn.io
```

**6. Test:**
```bash
curl https://abcd1234.mock.pstmn.io/users
# Returns: {"users": [...]}

curl https://abcd1234.mock.pstmn.io/users/1
# Returns: {"id": 1, "name": "John Doe", ...}
```

**7. Use in Tests:**
```javascript
pm.test("Mock returns user data", () => {
    const response = pm.response.json();
    pm.expect(response.id).to.equal(1);
    pm.expect(response.name).to.equal("John Doe");
});
```

## 13. Best Practices

### ✅ DO

- Save realistic example responses
- Create examples cho cả success và error cases
- Use descriptive example names
- Document mock server usage
- Version control collections với examples
- Test mocks before sharing
- Clean up unused mocks

### ❌ DON'T

- Rely solely on mocks for testing
- Forget to update examples khi API changes
- Use mocks trong production
- Share mock URLs publicly (có thể có sensitive data)
- Create too many unnecessary mocks

## 14. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ Mock Servers là fake APIs
- ✅ Tạo mock từ collections
- ✅ Save và manage example responses
- ✅ Use mock URLs trong development/testing
- ✅ Practical use cases
- ✅ Mock vs Real API trade-offs
- ✅ Best practices

## Next Steps

- **Bài tiếp theo**: [6.6 Newman CLI](./newman-cli.md)

---

[⬅️ Collection Runner](./collection-runner.md) | [Tổng Quan Chương 6](./README.md) | [Tiếp Theo: Newman CLI ➡️](./newman-cli.md)
