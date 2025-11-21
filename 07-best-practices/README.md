# Chương 7: Best Practices - Thực Hành Tốt Nhất

Học từ kinh nghiệm của hàng triệu API testers để làm việc hiệu quả và chuyên nghiệp hơn.

## Mục Tiêu

- ✅ Tổ chức tests một cách khoa học
- ✅ Viết test cases maintainable
- ✅ Xử lý lỗi hiệu quả
- ✅ Collaboration với team
- ✅ Security best practices
- ✅ Performance optimization

## 7.1 Tổ Chức Collections

### Cấu Trúc Folders Tốt

```
📁 Project API Tests
  📁 01-Authentication
    ↳ Register
    ↳ Login
    ↳ Logout
    ↳ Refresh Token
  📁 02-Users
    ↳ GET All Users
    ↳ GET User by ID
    ↳ POST Create User
    ↳ PUT Update User
    ↳ DELETE User
  📁 03-Products
    ↳ CRUD operations...
  📁 04-Orders
    ↳ CRUD operations...
  📁 99-Smoke Tests
    ↳ Critical endpoints only
```

### Naming Conventions

**✅ TỐT:**
```
GET All Users
POST Create New User
PUT Update User Profile
DELETE Remove User
```

**❌ TỆ:**
```
users
create
update thing
delete1
```

**Best practices:**
- Bắt đầu bằng HTTP method
- Mô tả rõ ràng action
- Consistent format
- Dễ hiểu cho người mới

## 7.2 Environments Management

### Setup Environments Đúng Cách

**Development:**
```json
{
  "base_url": "http://localhost:3000",
  "db_url": "localhost:5432",
  "environment": "dev"
}
```

**Staging:**
```json
{
  "base_url": "https://staging.api.example.com",
  "db_url": "staging-db.example.com",
  "environment": "staging"
}
```

**Production:**
```json
{
  "base_url": "https://api.example.com",
  "db_url": "prod-db.example.com",
  "environment": "production"
}
```

### Variables Best Practices

**✅ Sử dụng variables cho:**
- Base URLs
- Auth tokens
- API keys
- Common parameters
- Environment-specific values

**❌ KHÔNG hard-code:**
- URLs
- Credentials
- Environment-specific data

## 7.3 Writing Maintainable Tests

### Test Scripts Best Practices

**✅ TỐT:**
```javascript
// Clear, descriptive test name
pm.test("User registration returns 201 Created", () => {
    pm.response.to.have.status(201);
});

// Test one thing at a time
pm.test("Response contains user ID", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('id');
});

pm.test("User ID is a number", () => {
    const response = pm.response.json();
    pm.expect(response.id).to.be.a('number');
});
```

**❌ TỆ:**
```javascript
// Vague name
pm.test("Test 1", () => {
    pm.response.to.have.status(201);
    pm.expect(pm.response.json().id).to.be.a('number');
    pm.expect(pm.response.json().email).to.include('@');
    // Testing too many things at once
});
```

### DRY Principle - Don't Repeat Yourself

**Tạo helper functions:**

```javascript
// Pre-request Script ở Collection level
pm.globals.set("getAuthToken", () => {
    return pm.environment.get("token");
});

pm.globals.set("validateUser", (user) => {
    pm.expect(user).to.have.property('id');
    pm.expect(user).to.have.property('email');
    pm.expect(user).to.have.property('name');
});
```

**Sử dụng:**
```javascript
// Trong bất kỳ request nào
const user = pm.response.json();
eval(pm.globals.get("validateUser"))(user);
```

## 7.4 Error Handling

### Test Error Cases

**Always test:**
1. Happy path (200, 201, 204)
2. Client errors (400, 401, 403, 404, 422)
3. Server errors (500, 502, 503)

**Ví dụ:**
```javascript
// Test invalid input
pm.test("Invalid email returns 400", () => {
    pm.response.to.have.status(400);
});

pm.test("Error message is clear", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('error');
    pm.expect(response.error).to.include('email');
});

// Test unauthorized access
pm.test("No token returns 401", () => {
    pm.response.to.have.status(401);
});

// Test forbidden access
pm.test("Regular user cannot access admin endpoint", () => {
    pm.response.to.have.status(403);
});
```

### Graceful Failure

```javascript
pm.test("Response is valid JSON", () => {
    try {
        pm.response.json();
    } catch (e) {
        pm.expect.fail("Response is not valid JSON");
    }
});
```

## 7.5 Documentation

### Document Collections

Trong Postman, thêm descriptions cho:
- **Collections**: Tổng quan về API
- **Folders**: Mục đích của nhóm endpoints
- **Requests**: Chi tiết endpoint, parameters, responses

**Ví dụ:**
```markdown
# User Management API

## Authentication Required
All endpoints require Bearer token in Authorization header.

## Endpoints

### GET /users
Returns list of all users.

**Query Parameters:**
- page (optional): Page number (default: 1)
- limit (optional): Items per page (default: 10)

**Response 200:**
{
  "users": [...],
  "total": 100,
  "page": 1
}
```

## 7.6 Security Best Practices

### KHÔNG BAO GIỜ:

❌ Hard-code credentials trong requests
❌ Commit API keys/tokens vào Git
❌ Share production credentials
❌ Log sensitive data
❌ Use HTTP cho production (phải HTTPS)

### NÊN:

✅ Use environment variables
✅ Separate dev/staging/prod credentials
✅ Rotate keys định kỳ
✅ Use HTTPS
✅ Implement proper authentication tests
✅ Test authorization thoroughly
✅ Validate input sanitization

### Git Best Practices

**.gitignore:**
```
# Postman sensitive files
*.postman_environment.json
secrets.json
.env
```

**Export collections WITHOUT environments:**
- Collections: ✅ Commit to Git
- Environments: ❌ KHÔNG commit (có credentials)
- Share environments securely (encrypted, password manager)

## 7.7 Performance

### Response Time Tests

```javascript
pm.test("API responds in < 500ms", () => {
    pm.expect(pm.response.responseTime).to.be.below(500);
});

pm.test("Fast for critical endpoint < 200ms", () => {
    pm.expect(pm.response.responseTime).to.be.below(200);
});
```

### Batch Tests

Dùng Collection Runner để test performance:
- Run 100 iterations
- Check average response time
- Identify bottlenecks

## 7.8 Collaboration

### Team Workspaces

- ✅ Use team workspace cho shared collections
- ✅ Comment trên requests để discuss
- ✅ @mention teammates
- ✅ Track changes (Postman has version history)

### Code Reviews

Review Postman collections như code:
- Test coverage đầy đủ?
- Naming conventions consistent?
- Documentation clear?
- Error handling proper?
- Security practices followed?

## 7.9 CI/CD Integration

### Newman trong CI Pipeline

**GitHub Actions example:**
```yaml
name: API Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install Newman
        run: npm install -g newman
      - name: Run API Tests
        run: newman run collection.json -e ${{ secrets.ENVIRONMENT }}
```

**Benefits:**
- Automated testing on every commit
- Catch regressions early
- Quality gates before deployment

## 7.10 Monitoring

### Production Monitoring

Postman Monitors (paid feature):
- Schedule tests to run every X minutes
- Alert when tests fail
- Track API uptime
- Performance metrics

**Alternative (free):**
- Newman + Cron jobs
- Newman + CI/CD scheduled pipelines

## Checklist

Trước khi release/share collection:

- [ ] All tests passing
- [ ] No hard-coded credentials
- [ ] Clear naming conventions
- [ ] Proper folder structure
- [ ] Documentation complete
- [ ] Error cases tested
- [ ] Environment variables documented
- [ ] Example responses saved
- [ ] Security reviewed
- [ ] Performance acceptable

## Common Mistakes to Avoid

### ❌ Mistake 1: Too Few Tests
```javascript
// CHỈ test status code
pm.test("Status is 200", () => {
    pm.response.to.have.status(200);
});
```

### ✅ Better:
```javascript
pm.test("Status is 200", () => {
    pm.response.to.have.status(200);
});

pm.test("Response has correct structure", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('data');
    pm.expect(response.data).to.be.an('array');
});

pm.test("Response time acceptable", () => {
    pm.expect(pm.response.responseTime).to.be.below(500);
});
```

### ❌ Mistake 2: Flaky Tests

Tests sometimes pass, sometimes fail (không stable).

**Causes:**
- Timing issues
- External dependencies
- Shared test data

**Solutions:**
- Use unique test data (timestamps, UUIDs)
- Proper setup/teardown
- Avoid dependencies between tests

### ❌ Mistake 3: Poor Test Data Management

**Bad:**
```javascript
// Using same hardcoded data
{
  "email": "test@test.com"  // Conflict nếu đã tồn tại
}
```

**Good:**
```javascript
// Generate unique data
const timestamp = Date.now();
pm.environment.set("testEmail", `test${timestamp}@test.com`);
```

## Tips & Tricks

### 1. Keyboard Shortcuts

- `Ctrl/Cmd + Enter`: Send request
- `Ctrl/Cmd + S`: Save request
- `Ctrl/Cmd + F`: Search in collection
- `Ctrl/Cmd + Shift + F`: Search all workspaces

### 2. Console for Debugging

```javascript
console.log("Response:", pm.response.json());
console.log("Token:", pm.environment.get("token"));
console.table(pm.response.json().users);
```

View: View → Show Postman Console

### 3. Postman Flows (New Feature)

Visual API workflow builder - great for complex scenarios.

### 4. Snippets

Postman provides code snippets on the right:
- Status code checks
- Response body validation
- Set environment variables
- Common assertions

## Tổng Kết

Best practices giúp bạn:
- 🚀 Làm việc nhanh hơn
- 🎯 Ít lỗi hơn
- 👥 Collaborate tốt hơn
- 🔒 Bảo mật hơn
- 📈 Maintainable code

## Thời Gian Học

**Ước tính: 3-4 giờ**

---

[⬅️ Chương 6](../06-testing-nang-cao/README.md) | [Về Trang Chủ](../README.md) | [Tiếp Theo: Chương 8 ➡️](../08-du-an-thuc-te/README.md)
