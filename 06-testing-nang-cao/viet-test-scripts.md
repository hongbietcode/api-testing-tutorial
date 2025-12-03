# 6.1 Viết Test Scripts

Test scripts giúp tự động hóa việc kiểm tra API responses, giúp bạn phát hiện bugs nhanh chóng và đảm bảo API hoạt động đúng.

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Viết test scripts bằng JavaScript trong Postman
- ✅ Hiểu pm.test() syntax
- ✅ Test status codes, response body, headers, response time
- ✅ Sử dụng Chai assertions
- ✅ Organize tests hiệu quả

## 1. Tests Tab Trong Postman

> **📸 HÌNH ẢNH:** Postman Tests Tab
> - File: `postman-tests-tab.png`
> - Nội dung: Screenshot của Tests tab trong Postman request, showing code editor với test examples

<!-- IMAGE_PLACEHOLDER: postman-tests-tab.png -->

**Tests tab** là nơi bạn viết JavaScript code để validate API responses.

### Vị Trí
- Mở request → Click tab **Tests** (bên cạnh tabs Body, Pre-request Script)

### Khi Nào Chạy
- Tests chạy **SAU** khi nhận được response từ server

## 2. Cú Pháp Cơ Bản

### pm.test() Function

```javascript
pm.test("Test name here", function() {
    // Assertion code
});
```

**Giải thích:**
- `pm.test()`: Postman test function
- `"Test name"`: Mô tả test (hiển thị trong kết quả)
- `function()`: Test logic

### Ví Dụ Đơn Giản

```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

> **📸 HÌNH ẢNH:** Test Results
> - File: `test-results-passed.png`
> - Nội dung: Screenshot test results panel showing passed test với green checkmark

<!-- IMAGE_PLACEHOLDER: test-results-passed.png -->

## 3. Test Status Codes

### Test Một Status Code

```javascript
pm.test("Status code is 200", function() {
    pm.response.to.have.status(200);
});
```

### Test Multiple Status Codes

```javascript
pm.test("Successful POST request", function() {
    pm.expect(pm.response.code).to.be.oneOf([201, 202]);
});
```

### Test Range

```javascript
pm.test("Status code is 2xx", function() {
    pm.expect(pm.response.code).to.be.within(200, 299);
});
```

### Common Status Code Tests

```javascript
// Success
pm.test("Status is 200 OK", () => {
    pm.response.to.have.status(200);
});

// Created
pm.test("Resource created", () => {
    pm.response.to.have.status(201);
});

// No Content
pm.test("Deleted successfully", () => {
    pm.response.to.have.status(204);
});

// Bad Request
pm.test("Invalid data returns 400", () => {
    pm.response.to.have.status(400);
});

// Unauthorized
pm.test("No auth returns 401", () => {
    pm.response.to.have.status(401);
});

// Not Found
pm.test("Resource not found", () => {
    pm.response.to.have.status(404);
});
```

## 4. Test Response Body

### Parse JSON Response

```javascript
const response = pm.response.json();
```

### Test Property Exists

```javascript
pm.test("Response has id property", function() {
    const response = pm.response.json();
    pm.expect(response).to.have.property('id');
});
```

### Test Property Value

```javascript
pm.test("User name is correct", function() {
    const response = pm.response.json();
    pm.expect(response.name).to.eql("Leanne Graham");
});
```

### Test Data Type

```javascript
pm.test("ID is a number", function() {
    const response = pm.response.json();
    pm.expect(response.id).to.be.a('number');
});

pm.test("Name is a string", function() {
    const response = pm.response.json();
    pm.expect(response.name).to.be.a('string');
});

pm.test("Active is boolean", function() {
    const response = pm.response.json();
    pm.expect(response.active).to.be.a('boolean');
});
```

### Test Array Length

```javascript
pm.test("Returns 10 users", function() {
    const users = pm.response.json();
    pm.expect(users).to.be.an('array');
    pm.expect(users).to.have.lengthOf(10);
});
```

### Test Array Contains

```javascript
pm.test("Array contains item", function() {
    const items = pm.response.json();
    pm.expect(items).to.include(1);
});
```

### Test Nested Properties

```javascript
pm.test("Address has city", function() {
    const response = pm.response.json();
    pm.expect(response.address).to.have.property('city');
    pm.expect(response.address.city).to.eql("Gwenborough");
});
```

## 5. Test Response Time

```javascript
pm.test("Response time is less than 200ms", function() {
    pm.expect(pm.response.responseTime).to.be.below(200);
});

pm.test("Fast response", function() {
    pm.expect(pm.response.responseTime).to.be.below(500);
});

pm.test("Within acceptable range", function() {
    pm.expect(pm.response.responseTime).to.be.within(100, 1000);
});
```

## 6. Test Headers

```javascript
pm.test("Content-Type is JSON", function() {
    pm.expect(pm.response.headers.get('Content-Type'))
        .to.include('application/json');
});

pm.test("Has Cache-Control header", function() {
    pm.expect(pm.response.headers.has('Cache-Control')).to.be.true;
});

pm.test("Server is cloudflare", function() {
    pm.expect(pm.response.headers.get('Server')).to.eql('cloudflare');
});
```

## 7. Test Response Text

Cho non-JSON responses:

```javascript
pm.test("Body contains success message", function() {
    pm.expect(pm.response.text()).to.include("Success");
});
```

## 8. Complete Example

```javascript
// Test 1: Status code
pm.test("Status code is 200", function() {
    pm.response.to.have.status(200);
});

// Test 2: Response time
pm.test("Response time < 500ms", function() {
    pm.expect(pm.response.responseTime).to.be.below(500);
});

// Parse response once
const response = pm.response.json();

// Test 3: Has required fields
pm.test("Has required fields", function() {
    pm.expect(response).to.have.property('id');
    pm.expect(response).to.have.property('name');
    pm.expect(response).to.have.property('email');
});

// Test 4: Correct data types
pm.test("Correct data types", function() {
    pm.expect(response.id).to.be.a('number');
    pm.expect(response.name).to.be.a('string');
    pm.expect(response.email).to.be.a('string');
});

// Test 5: Specific values
pm.test("User ID is 1", function() {
    pm.expect(response.id).to.eql(1);
});

// Test 6: Email format
pm.test("Email is valid format", function() {
    pm.expect(response.email).to.match(/^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$/);
});

// Test 7: Headers
pm.test("Content-Type is JSON", function() {
    pm.expect(pm.response.headers.get('Content-Type'))
        .to.include('application/json');
});

console.log("✅ All tests completed for user:", response.name);
```

> **📸 HÌNH ẢNH:** Complete Test Results
> - File: `complete-test-results.png`
> - Nội dung: Test results panel showing multiple tests (7 passed), với timestamps và response details

<!-- IMAGE_PLACEHOLDER: complete-test-results.png -->

## 9. Chai Assertions Reference

Postman sử dụng Chai.js assertion library.

### Equality

```javascript
pm.expect(value).to.equal(10);          // Strict equality (===)
pm.expect(value).to.eql(10);            // Deep equality
pm.expect(value).to.not.equal(5);       // Not equal
```

### Type Checking

```javascript
pm.expect(value).to.be.a('string');
pm.expect(value).to.be.a('number');
pm.expect(value).to.be.a('boolean');
pm.expect(value).to.be.an('array');
pm.expect(value).to.be.an('object');
pm.expect(value).to.be.null;
pm.expect(value).to.be.undefined;
```

### Comparisons

```javascript
pm.expect(value).to.be.above(10);       // > 10
pm.expect(value).to.be.below(100);      // < 100
pm.expect(value).to.be.within(10, 100); // Between
pm.expect(value).to.be.at.least(10);    // >= 10
pm.expect(value).to.be.at.most(100);    // <= 100
```

### Strings

```javascript
pm.expect(string).to.include('substring');
pm.expect(string).to.match(/regex/);
pm.expect(string).to.have.lengthOf(10);
```

### Arrays

```javascript
pm.expect(array).to.be.an('array');
pm.expect(array).to.have.lengthOf(5);
pm.expect(array).to.include(item);
pm.expect(array).to.be.empty;
```

### Objects

```javascript
pm.expect(obj).to.have.property('key');
pm.expect(obj).to.have.property('key', 'value');
pm.expect(obj).to.have.all.keys('id', 'name', 'email');
pm.expect(obj).to.be.empty;
```

### Boolean

```javascript
pm.expect(value).to.be.true;
pm.expect(value).to.be.false;
pm.expect(value).to.exist;
```

## 10. Negative Tests

```javascript
// Test error responses
pm.test("Returns 404 for invalid ID", function() {
    pm.response.to.have.status(404);
});

pm.test("Error message exists", function() {
    const response = pm.response.json();
    pm.expect(response).to.have.property('error');
    pm.expect(response.error).to.be.a('string');
});

// Test missing auth
pm.test("Unauthorized without token", function() {
    pm.response.to.have.status(401);
});

// Test validation errors
pm.test("Validates required fields", function() {
    pm.response.to.have.status(400);
    const response = pm.response.json();
    pm.expect(response.errors).to.be.an('array');
});
```

## 11. Conditional Tests

```javascript
// Only test if status is 200
if (pm.response.code === 200) {
    pm.test("Has user data", function() {
        const response = pm.response.json();
        pm.expect(response.name).to.exist;
    });
}

// Test based on environment
const env = pm.environment.name;
if (env === "Production") {
    pm.test("Response time < 100ms in prod", function() {
        pm.expect(pm.response.responseTime).to.be.below(100);
    });
}
```

## 12. Debugging Tests

### Console Logging

```javascript
console.log("Response:", pm.response.json());
console.log("Status:", pm.response.code);
console.log("Time:", pm.response.responseTime + "ms");
```

> **📸 HÌNH ẢNH:** Console Output
> - File: `console-logs-debugging.png`
> - Nội dung: Postman Console window showing console.log outputs với response data

<!-- IMAGE_PLACEHOLDER: console-logs-debugging.png -->

### View Console

`View > Show Postman Console` hoặc `Ctrl/Cmd + Alt + C`

## 13. Best Practices

### ✅ DO

- Test status code đầu tiên
- Parse response một lần, reuse variable
- Use descriptive test names
- Test structure trước values
- Test cả positive và negative cases
- Log useful debugging info
- Group related tests

### ❌ DON'T

- Hardcode expected values (dùng variables)
- Skip error cases
- Write overly complex tests
- Duplicate test logic
- Forget to handle null/undefined

## 14. Common Patterns

### Pattern 1: Complete CRUD Validation

```javascript
// POST - Create
pm.test("Created successfully", () => pm.response.to.have.status(201));
pm.test("Returns created resource", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('id');
});

// GET - Read
pm.test("Fetched successfully", () => pm.response.to.have.status(200));
pm.test("Returns correct resource", () => {
    const response = pm.response.json();
    pm.expect(response.id).to.eql(pm.environment.get("resourceId"));
});

// PUT - Update
pm.test("Updated successfully", () => pm.response.to.have.status(200));
pm.test("Returns updated resource", () => {
    const response = pm.response.json();
    pm.expect(response.name).to.eql("Updated Name");
});

// DELETE - Delete
pm.test("Deleted successfully", () => {
    pm.expect([200, 204]).to.include(pm.response.code);
});
```

### Pattern 2: Pagination Tests

```javascript
pm.test("Has pagination info", function() {
    const response = pm.response.json();
    pm.expect(response).to.have.property('page');
    pm.expect(response).to.have.property('per_page');
    pm.expect(response).to.have.property('total');
});

pm.test("Returns correct page", function() {
    const response = pm.response.json();
    const requestedPage = pm.request.url.query.get("page");
    pm.expect(response.page).to.eql(parseInt(requestedPage));
});
```

## 15. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ Viết tests với pm.test()
- ✅ Test status codes, body, headers, response time
- ✅ Sử dụng Chai assertions
- ✅ Negative testing
- ✅ Debugging với console.log
- ✅ Best practices

## Next Steps

- **Bài tiếp theo**: [6.2 Pre-request Scripts](./pre-request-scripts.md)

---

[⬅️ Tổng Quan Chương 6](./README.md) | [Tiếp Theo: Pre-request Scripts ➡️](./pre-request-scripts.md)
