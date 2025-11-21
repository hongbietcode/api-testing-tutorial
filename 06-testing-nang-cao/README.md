# Chương 6: Testing Nâng Cao

Chương này sẽ đưa kỹ năng API testing của bạn lên một tầm cao mới với test automation, scripting, và các kỹ thuật nâng cao.

## Mục Tiêu Học Tập

Sau khi hoàn thành chương này, bạn sẽ:

- ✅ Viết test scripts bằng JavaScript trong Postman
- ✅ Tự động hóa API tests
- ✅ Sử dụng Collection Runner
- ✅ Data-driven testing
- ✅ Chaining requests
- ✅ Sử dụng Mock Servers
- ✅ Newman CLI basics

## Nội Dung Chương

### 6.1 Viết Test Scripts

#### Tests Tab trong Postman

Postman sử dụng JavaScript để viết tests:

```javascript
// Test status code
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

// Test response time
pm.test("Response time < 500ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(500);
});

// Test response body
pm.test("Body has email", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData).to.have.property('email');
});

// Test specific value
pm.test("Name is correct", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.name).to.eql("Leanne Graham");
});
```

#### Common Test Patterns

**1. Status Code Tests:**
```javascript
pm.test("Status code is 200", () => {
    pm.response.to.have.status(200);
});

pm.test("Status code is 201 or 202", () => {
    pm.expect(pm.response.code).to.be.oneOf([201, 202]);
});
```

**2. Response Body Tests:**
```javascript
// Check property exists
pm.test("Has id property", () => {
    pm.expect(pm.response.json()).to.have.property('id');
});

// Check value
pm.test("User id is 1", () => {
    const response = pm.response.json();
    pm.expect(response.id).to.equal(1);
});

// Check array length
pm.test("Returns 10 users", () => {
    const users = pm.response.json();
    pm.expect(users).to.have.lengthOf(10);
});
```

**3. Response Time Tests:**
```javascript
pm.test("Fast response", () => {
    pm.expect(pm.response.responseTime).to.be.below(200);
});
```

**4. Header Tests:**
```javascript
pm.test("Content-Type is JSON", () => {
    pm.expect(pm.response.headers.get('Content-Type'))
        .to.include('application/json');
});
```

### 6.2 Pre-request Scripts

Chạy code TRƯỚC khi gửi request:

```javascript
// Set timestamp
pm.environment.set("timestamp", Date.now());

// Generate random email
const randomEmail = `user${Math.floor(Math.random() * 1000)}@test.com`;
pm.environment.set("randomEmail", randomEmail);

// Log for debugging
console.log("Sending request at:", new Date());
```

### 6.3 Variables và Data Management

#### Set Variables trong Scripts

```javascript
// Set environment variable
pm.environment.set("token", jsonData.token);

// Set global variable
pm.globals.set("userId", jsonData.id);

// Set collection variable
pm.collectionVariables.set("apiKey", "abc123");

// Get variables
const token = pm.environment.get("token");
const userId = pm.globals.get("userId");
```

#### Chaining Requests

**Example: Login → Get Profile**

Request 1 (Login):
```javascript
// Tests tab
pm.test("Login successful", () => {
    pm.response.to.have.status(200);

    // Save token for next request
    const response = pm.response.json();
    pm.environment.set("authToken", response.token);
});
```

Request 2 (Get Profile):
```
Authorization: Bearer {{authToken}}
```

### 6.4 Collection Runner

Chạy toàn bộ collection tự động:

**Cách sử dụng:**
1. Click "..." trên collection
2. Chọn "Run collection"
3. Chọn environment
4. Chọn iterations (số lần chạy)
5. Click "Run"

**Data-Driven Testing:**

Tạo file CSV/JSON với test data:

users.csv:
```csv
name,email,age
John,john@test.com,25
Jane,jane@test.com,30
Bob,bob@test.com,28
```

Sử dụng trong request:
```json
{
  "name": "{{name}}",
  "email": "{{email}}",
  "age": {{age}}
}
```

Collection Runner sẽ chạy request cho mỗi dòng trong CSV!

### 6.5 Mock Servers

Tạo fake API để test khi backend chưa sẵn sàng:

**Tạo Mock Server:**
1. Right-click Collection → "Mock Collection"
2. Tên mock server
3. Save example responses
4. Postman generate mock URL

**Sử dụng:**
```
https://[mock-id].mock.pstmn.io/users
→ Trả về example response bạn đã save
```

### 6.6 Newman CLI

Chạy Postman collections từ command line:

**Cài đặt:**
```bash
npm install -g newman
```

**Chạy collection:**
```bash
# Basic
newman run collection.json

# Với environment
newman run collection.json -e environment.json

# Với reporter
newman run collection.json -r html

# Nhiều iterations
newman run collection.json -n 10
```

**Use cases:**
- CI/CD pipelines (Jenkins, GitHub Actions)
- Scheduled tests (cron jobs)
- Automation scripts
- Load testing

### 6.7 JSON Schema Validation

Validate response structure:

```javascript
const schema = {
    type: "object",
    properties: {
        id: { type: "number" },
        name: { type: "string" },
        email: { type: "string" }
    },
    required: ["id", "name", "email"]
};

pm.test("Schema is valid", () => {
    pm.response.to.have.jsonSchema(schema);
});
```

## Bài Tập Thực Hành

### Bài 1: Write Tests (Dễ)

Sử dụng JSONPlaceholder, viết tests cho:

1. GET /users/1
   - Status is 200
   - Response time < 500ms
   - Has name property
   - Name is "Leanne Graham"

2. POST /users
   - Status is 201
   - Response has id
   - id is a number

### Bài 2: Chaining Requests (Trung bình)

Tạo workflow:
1. POST /users (tạo user) → save userId
2. GET /users/{{userId}} (verify user created)
3. PUT /users/{{userId}} (update user)
4. GET /users/{{userId}} (verify update)
5. DELETE /users/{{userId}} (delete user)

### Bài 3: Data-Driven Testing (Khó)

1. Tạo CSV với 10 users
2. POST create user cho mỗi user
3. Verify tất cả created successfully
4. Run với Collection Runner

### Bài 4: Newman CLI (Nâng cao)

1. Export collection
2. Export environment
3. Chạy với Newman
4. Generate HTML report

## Best Practices

### Tests

- ✅ Test status code đầu tiên
- ✅ Test structure trước values
- ✅ Test cả positive và negative cases
- ✅ Descriptive test names
- ✅ DRY - Don't Repeat Yourself

### Scripts

- ✅ Comment code rõ ràng
- ✅ Handle errors gracefully
- ✅ Use console.log for debugging
- ✅ Clean up variables khi không dùng

### Organization

- ✅ Logical folder structure
- ✅ Clear naming conventions
- ✅ Document collections
- ✅ Version control (export to Git)

## Thời Gian Học

**Ước tính: 6-8 giờ**
- Học scripting: 3 giờ
- Thực hành: 3-5 giờ

## Tài Nguyên

- [Postman Scripting Reference](https://learning.postman.com/docs/writing-scripts/intro-to-scripts/)
- [Chai Assertion Library](https://www.chaijs.com/api/bdd/)
- [Newman Documentation](https://github.com/postmanlabs/newman)

---

[⬅️ Chương 5](../05-xac-thuc-authorization/README.md) | [Về Trang Chủ](../README.md) | [Tiếp Theo: Chương 7 ➡️](../07-best-practices/README.md)
