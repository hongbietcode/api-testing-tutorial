# 5.3 Basic Authentication

Basic Authentication là phương thức authentication đơn giản nhất, sử dụng username và password được encode bằng Base64.

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Hiểu Basic Auth hoạt động như thế nào
- ✅ Encode username:password thành Base64
- ✅ Gửi Basic Auth trong Authorization header
- ✅ Sử dụng Postman Basic Auth
- ✅ Hiểu security risks và khi nào nên dùng

## 1. Basic Authentication Là Gì?

**Basic Auth** gửi username và password trong mỗi request, được encode bằng Base64.

### Format

```
Authorization: Basic <base64(username:password)>
```

### Ví Dụ

**Credentials:**
```
Username: admin
Password: secret123
```

**Step 1: Combine với dấu `:`**
```
admin:secret123
```

**Step 2: Encode Base64**
```
YWRtaW46c2VjcmV0MTIz
```

**Step 3: Add to Header**
```
Authorization: Basic YWRtaW46c2VjcmV0MTIz
```

## 2. Cách Hoạt Động

### Flow

```
1. Client → GET /api/data
2. Server → 401 Unauthorized
            WWW-Authenticate: Basic realm="API"
3. Client → GET /api/data
            Authorization: Basic <credentials>
4. Server → Verify username:password
5. Server → 200 OK + data (nếu đúng)
            401 Unauthorized (nếu sai)
```

### Decode Base64

Base64 **KHÔNG phải encryption**, chỉ là encoding!

```
YWRtaW46c2VjcmV0MTIz
→ Decode → admin:secret123
```

Bất kỳ ai cũng decode được → **Not secure over HTTP!**

## 3. Thực Hành: HTTPBin Basic Auth

HTTPBin cung cấp endpoints để test Basic Auth.

### Endpoint

```
GET https://httpbin.org/basic-auth/{user}/{password}
```

### Bước 1: Setup Environment

**Environment: HTTPBin**
```
baseUrl = https://httpbin.org
username = testuser
password = testpass
```

### Bước 2: Test với Postman Authorization Tab

**Request: Basic Auth Test**
```
Method: GET
URL: {{baseUrl}}/basic-auth/{{username}}/{{password}}
```

**Authorization Tab:**
1. Type: **Basic Auth**
2. Username: `{{username}}`
3. Password: `{{password}}`

Postman tự động tạo header!

### Bước 3: Verify Request

Click **Send**

**Expected Response:**
```json
{
  "authenticated": true,
  "user": "testuser"
}
```

**Expected Status:** `200 OK`

### Bước 4: Check Headers

Mở **Console** và xem Request Headers:
```
Authorization: Basic dGVzdHVzZXI6dGVzdHBhc3M=
```

## 4. Manual Basic Auth Header

### Cách 1: Postman Tab (Recommended)

Đã demo ở trên ✅

### Cách 2: Manual Header

**Pre-request Script:**
```javascript
const username = pm.environment.get("username");
const password = pm.environment.get("password");

// Combine username:password
const credentials = username + ":" + password;

// Encode to Base64
const encodedCredentials = btoa(credentials);

// Set header
pm.environment.set("basicAuth", encodedCredentials);
```

**Headers:**
```
Authorization: Basic {{basicAuth}}
```

### Built-in btoa() Function

Postman hỗ trợ:
- `btoa(str)`: Encode to Base64
- `atob(str)`: Decode from Base64

```javascript
const encoded = btoa("admin:secret");
// → "YWRtaW46c2VjcmV0"

const decoded = atob("YWRtaW46c2VjcmV0");
// → "admin:secret"
```

## 5. Test Valid Credentials

### Request: Correct Credentials

```
Method: GET
URL: {{baseUrl}}/basic-auth/user1/pass1

Authorization:
  Type: Basic Auth
  Username: user1
  Password: pass1
```

### Tests

```javascript
pm.test("Status code is 200", function() {
    pm.response.to.have.status(200);
});

pm.test("User is authenticated", function() {
    const response = pm.response.json();
    pm.expect(response.authenticated).to.be.true;
});

pm.test("Username is correct", function() {
    const response = pm.response.json();
    pm.expect(response.user).to.eql("user1");
});

console.log("✅ Basic Auth successful");
```

## 6. Test Invalid Credentials

### Request: Wrong Password

```
Method: GET
URL: {{baseUrl}}/basic-auth/user1/pass1

Authorization:
  Username: user1
  Password: wrongpassword
```

**Expected:**
```
Status: 401 Unauthorized
```

### Tests

```javascript
pm.test("Status code is 401", function() {
    pm.response.to.have.status(401);
});

pm.test("WWW-Authenticate header exists", function() {
    pm.expect(pm.response.headers.has("WWW-Authenticate")).to.be.true;
});

console.log("✅ Invalid credentials rejected correctly");
```

## 7. Test Missing Credentials

### Request: No Authorization Header

```
Method: GET
URL: {{baseUrl}}/basic-auth/user1/pass1

Authorization: No Auth
```

**Expected:**
```
Status: 401 Unauthorized

Headers:
WWW-Authenticate: Basic realm="Fake Realm"
```

### Tests

```javascript
pm.test("Returns 401 without credentials", function() {
    pm.response.to.have.status(401);
});

pm.test("Requests authentication", function() {
    const wwwAuth = pm.response.headers.get("WWW-Authenticate");
    pm.expect(wwwAuth).to.include("Basic");
});
```

## 8. Collection-Level Basic Auth

Set Basic Auth cho cả collection.

### Setup

1. Click vào **Collection**
2. Tab **Authorization**
3. Type: **Basic Auth**
4. Username: `{{username}}`
5. Password: `{{password}}`

Tất cả requests tự động có Basic Auth!

### Override Cho Request Cụ Thể

Một request cần credentials khác:
1. Vào request đó
2. Tab Authorization
3. Type: Basic Auth
4. Nhập username/password riêng

## 9. Security Considerations

### ⚠️ Security Risks

| Risk | Mô tả |
|------|-------|
| **Base64 ≠ Encryption** | Dễ dàng decode |
| **Credentials in every request** | Nhiều cơ hội bị intercept |
| **No expiration** | Không tự động expire như token |
| **Password reuse** | Nếu password leak, tất cả APIs bị compromise |

### ❌ NEVER Use Basic Auth Over HTTP

```
http://api.example.com/data
Authorization: Basic YWRtaW46c2VjcmV0
```

Bất kỳ ai trên network đều thấy được credentials!

### ✅ ALWAYS Use HTTPS

```
https://api.example.com/data
Authorization: Basic YWRtaW46c2VjcmV0
```

HTTPS encrypts toàn bộ request, bao gồm headers.

## 10. Khi Nào Dùng Basic Auth?

### ✅ Phù Hợp Khi

- **Internal APIs** (không public)
- **Simple tools/scripts**
- **Development/Testing** (không production)
- **APIs behind firewall**
- **Temporary access**
- **Combined với HTTPS**

### ❌ KHÔNG Nên Dùng Khi

- Public APIs
- Mobile apps (credentials trong app)
- Long-term production use
- High-security requirements
- User-facing applications

### Better Alternatives

| Use Case | Better Choice |
|----------|---------------|
| Public APIs | API Key hoặc OAuth 2.0 |
| Web/Mobile apps | Bearer Token (JWT) |
| Enterprise | OAuth 2.0, SAML |
| Microservices | Mutual TLS, Service tokens |

## 11. Bài Tập Thực Hành

### Bài 1: HTTPBin Basic Auth Tests

Create collection "Basic Auth Tests":

1. **Valid Credentials**
   ```
   GET /basic-auth/admin/admin123
   Auth: admin:admin123
   → Expect 200
   ```

2. **Invalid Password**
   ```
   GET /basic-auth/admin/admin123
   Auth: admin:wrongpass
   → Expect 401
   ```

3. **Invalid Username**
   ```
   GET /basic-auth/admin/admin123
   Auth: wronguser:admin123
   → Expect 401
   ```

4. **No Credentials**
   ```
   GET /basic-auth/admin/admin123
   Auth: None
   → Expect 401
   ```

5. **Empty Password**
   ```
   GET /basic-auth/admin/admin123
   Auth: admin:(empty)
   → Expect 401
   ```

### Bài 2: Manual Encoding

Viết script để manual encode credentials:

**Pre-request Script:**
```javascript
// Get credentials
const user = "myuser";
const pass = "mypass";

// Method 1: btoa
const encoded1 = btoa(user + ":" + pass);
console.log("btoa:", encoded1);

// Method 2: CryptoJS (available in Postman)
const credentials = user + ":" + pass;
const wordArray = CryptoJS.enc.Utf8.parse(credentials);
const encoded2 = CryptoJS.enc.Base64.stringify(wordArray);
console.log("CryptoJS:", encoded2);

// Set environment variable
pm.environment.set("basicAuthHeader", "Basic " + encoded1);
```

**Headers:**
```
Authorization: {{basicAuthHeader}}
```

### Bài 3: Compare Auth Methods

Tạo requests với cùng endpoint, khác auth:

1. **Basic Auth**
   ```
   GET /anything
   Authorization: Basic <credentials>
   ```

2. **Bearer Token**
   ```
   GET /anything
   Authorization: Bearer <token>
   ```

3. **API Key**
   ```
   GET /anything
   X-API-Key: <key>
   ```

So sánh responses và headers.

### Bài 4: Security Test

Test HTTPBin delay endpoint với Basic Auth:

```
GET {{baseUrl}}/delay/3
Authorization: Basic Auth
```

Verify:
1. Request takes ~3 seconds
2. Credentials sent trong mỗi request
3. Response vẫn 200 OK

## 12. Best Practices

### ✅ DO

- **Always use HTTPS** (mandatory!)
- Store credentials in environment variables
- Use Current Value for passwords
- Set timeout cho requests
- Implement retry logic
- Log authentication failures
- Use strong passwords

### ❌ DON'T

- Use over HTTP (insecure!)
- Hardcode credentials
- Commit credentials to Git
- Share credentials in screenshots
- Use weak passwords
- Store passwords in plaintext logs
- Use for public APIs

## 13. Environment Security

### Secure Setup

**Environment Variables:**
```
username = admin
password = <empty Initial Value, fill Current Value only>
```

**Export Environment:**
- Initial Value: empty (safe to share)
- Current Value: local only (not exported)

### .gitignore

```
*.postman_environment.json
!example.postman_environment.json
```

## 14. Debugging

### Check Authorization Header

**Console:**
```javascript
// Pre-request
const username = pm.environment.get("username");
const password = pm.environment.get("password");

if (!username || !password) {
    console.error("❌ Missing credentials!");
    throw new Error("Set username and password in environment");
}

console.log("Username:", username);
console.log("Password:", "*".repeat(password.length));
```

### Decode Header

```javascript
// Tests
const authHeader = pm.request.headers.get("Authorization");
console.log("Auth header:", authHeader);

if (authHeader && authHeader.startsWith("Basic ")) {
    const encoded = authHeader.substring(6);
    const decoded = atob(encoded);
    console.log("Decoded:", decoded);
    // Output: username:password
}
```

## 15. Common Errors

| Error | Nguyên nhân | Giải pháp |
|-------|-------------|-----------|
| **401** | Wrong credentials | Check username/password |
| **401** | Missing Auth header | Add Basic Auth |
| **403** | Valid auth, no permission | Check user role |
| **Empty password** | Variable không set | Set password trong environment |

## 16. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ Basic Authentication hoạt động như thế nào
- ✅ Format: `Authorization: Basic base64(username:password)`
- ✅ Encode/decode với btoa/atob
- ✅ Sử dụng Postman Basic Auth tab
- ✅ Test valid/invalid credentials
- ✅ Security risks và best practices
- ✅ Khi nào nên (và không nên) dùng Basic Auth

## 17. So Sánh Auth Methods

| Feature | Basic Auth | API Key | Bearer Token |
|---------|------------|---------|--------------|
| **Setup** | Đơn giản | Đơn giản | Trung bình |
| **Security** | Thấp-Trung bình | Trung bình | Cao |
| **Expiration** | Không | Không (thường) | Có |
| **Stateless** | Có | Có | Có |
| **Use HTTPS** | Bắt buộc | Nên | Bắt buộc |

## 18. Kiến Thức Cần Nhớ

| Khái niệm | Giải thích |
|-----------|------------|
| **Basic Auth** | Username:password encoded Base64 |
| **Base64** | Encoding (NOT encryption!) |
| **btoa()** | Function encode to Base64 |
| **atob()** | Function decode from Base64 |
| **401** | Unauthorized - invalid credentials |
| **HTTPS** | Bắt buộc khi dùng Basic Auth |

## Next Steps

Tiếp tục học:
- **Bài tiếp theo**: [5.4 OAuth 2.0 Cơ Bản](./oauth-2.md)

---

[⬅️ Bearer Token](./bearer-token-jwt.md) | [Tổng Quan Chương 5](./README.md) | [Tiếp Theo: OAuth 2.0 ➡️](./oauth-2.md)
