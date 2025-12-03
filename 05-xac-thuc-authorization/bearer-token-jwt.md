# 5.2 Bearer Token và JWT

Bearer Token là phương thức authentication phổ biến nhất cho modern web và mobile applications. JWT (JSON Web Token) là format token được sử dụng rộng rãi nhất.

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Hiểu Bearer Token và JWT là gì
- ✅ Implement login flow để lấy token
- ✅ Gửi token trong Authorization header
- ✅ Handle token expiration
- ✅ Refresh tokens
- ✅ Test authentication flows

## 1. Bearer Token Là Gì?

**Bearer Token** là một access token được gửi trong HTTP Authorization header.

### Format

```
Authorization: Bearer <token>
```

**Ví dụ:**
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Tại Sao Gọi Là "Bearer"?

**Bearer** = "người mang" → Ai mang token này đều có quyền truy cập.

## 2. JWT (JSON Web Token)

### JWT Là Gì?

**JWT** là một format token chứa thông tin dạng JSON, được encode và có thể sign.

### Cấu Trúc JWT

JWT gồm 3 phần, ngăn cách bởi dấu `.`:

```
header.payload.signature
```

**Ví dụ JWT:**
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

### 3 Phần Của JWT

#### 1. Header
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```
→ Encode thành: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9`

#### 2. Payload (Data)
```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "email": "john@example.com",
  "iat": 1516239022,
  "exp": 1516242622
}
```
→ Encode thành: `eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ`

#### 3. Signature
```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```
→ Tạo ra signature để verify token không bị modify

### Decode JWT

Truy cập: https://jwt.io
Paste JWT vào để decode và xem nội dung!

## 3. Authentication Flow

### Typical Flow

```
1. User → POST /login (username, password)
2. Server → Verify credentials
3. Server → Generate JWT token
4. Server → Return token
5. Client → Save token
6. Client → GET /api/data (Authorization: Bearer <token>)
7. Server → Verify token
8. Server → Return data
```

## 4. Thực Hành: ReqRes Login API

ReqRes.in cung cấp fake API để practice authentication.

### Endpoint: Login

**URL:** https://reqres.in/api/login

### Bước 1: Create Environment

**Environment: ReqRes API**
```
baseUrl = https://reqres.in/api
accessToken = (để trống, sẽ được fill sau login)
```

### Bước 2: Login Request

**Request: Login**
```
Method: POST
URL: {{baseUrl}}/login

Headers:
Content-Type: application/json

Body:
{
  "email": "eve.holt@reqres.in",
  "password": "cityslicka"
}
```

**Expected Response:**
```json
{
  "token": "QpwL5tke4Pnpja7X4"
}
```

### Bước 3: Save Token to Environment

**Tests tab:**
```javascript
pm.test("Status code is 200", function() {
    pm.response.to.have.status(200);
});

pm.test("Response has token", function() {
    const response = pm.response.json();
    pm.expect(response).to.have.property("token");
});

// Save token to environment
const response = pm.response.json();
if (response.token) {
    pm.environment.set("accessToken", response.token);
    console.log("✅ Token saved:", response.token);
}
```

### Bước 4: Use Token in Next Request

**Request: Get Users (Authenticated)**
```
Method: GET
URL: {{baseUrl}}/users

Headers:
Authorization: Bearer {{accessToken}}
```

Postman tự động thay `{{accessToken}}` bằng token đã lưu!

## 5. Postman Authorization Tab

### Cách 1: Manual Header

```
Headers:
Authorization: Bearer {{accessToken}}
```

### Cách 2: Authorization Tab (Recommended)

1. Click tab **Authorization**
2. Type: **Bearer Token**
3. Token: `{{accessToken}}`

Postman tự động tạo header:
```
Authorization: Bearer <token value>
```

### Collection-Level Auth

Set bearer token cho cả collection:

1. Click vào Collection
2. Tab **Authorization**
3. Type: **Bearer Token**
4. Token: `{{accessToken}}`

Tất cả requests inherit auth này!

## 6. Login Flow Tests

### Complete Login Test

```javascript
pm.test("Status code is 200", function() {
    pm.response.to.have.status(200);
});

const response = pm.response.json();

pm.test("Response has token", function() {
    pm.expect(response).to.have.property("token");
    pm.expect(response.token).to.be.a("string");
    pm.expect(response.token.length).to.be.above(0);
});

// Save token
pm.environment.set("accessToken", response.token);

// Save login time for expiration tracking
pm.environment.set("tokenIssuedAt", Date.now());

console.log("✅ Login successful");
console.log("Token:", response.token);
```

### Test Invalid Credentials

**Request: Login with Wrong Password**
```json
{
  "email": "eve.holt@reqres.in",
  "password": "wrong_password"
}
```

**Expected:**
```
Status: 400 Bad Request

{
  "error": "user not found"
}
```

**Tests:**
```javascript
pm.test("Status code is 400", function() {
    pm.response.to.have.status(400);
});

pm.test("Response has error message", function() {
    const response = pm.response.json();
    pm.expect(response).to.have.property("error");
});

console.log("✅ Invalid credentials handled correctly");
```

## 7. Token Expiration

### JWT Expiration Claims

JWT thường chứa:
- `iat` (issued at): Thời điểm tạo token
- `exp` (expiration): Thời điểm hết hạn

```json
{
  "userId": 123,
  "iat": 1704067200,
  "exp": 1704070800
}
```

### Check Token Expiration

**Pre-request Script:**
```javascript
// Get token issued time
const issuedAt = pm.environment.get("tokenIssuedAt");
const currentTime = Date.now();

// Token expires after 1 hour (example)
const expirationTime = 60 * 60 * 1000; // 1 hour in ms

if (issuedAt && (currentTime - issuedAt) > expirationTime) {
    console.warn("⚠️ Token may be expired. Consider re-authenticating.");

    // Auto-clear expired token
    pm.environment.unset("accessToken");
    throw new Error("Token expired. Please login again.");
}
```

### Handle 401 Unauthorized

**Tests:**
```javascript
if (pm.response.code === 401) {
    console.error("❌ Unauthorized: Token may be expired or invalid");

    // Clear token
    pm.environment.unset("accessToken");

    pm.test("Should re-authenticate", function() {
        pm.expect.fail("Token expired. Run login request first.");
    });
}
```

## 8. Refresh Tokens

Nhiều APIs sử dụng **Access Token** (short-lived) và **Refresh Token** (long-lived).

### Refresh Token Flow

```
1. Login → Get Access Token (15 min) + Refresh Token (7 days)
2. Use Access Token for API calls
3. Token expires → 401 Unauthorized
4. POST /refresh (with Refresh Token)
5. Get new Access Token
6. Continue using new token
```

### Example: Refresh Token Request

```
Method: POST
URL: {{baseUrl}}/auth/refresh

Body:
{
  "refreshToken": "{{refreshToken}}"
}
```

**Response:**
```json
{
  "accessToken": "new_access_token",
  "refreshToken": "new_refresh_token"
}
```

**Tests:**
```javascript
const response = pm.response.json();

// Update tokens
pm.environment.set("accessToken", response.accessToken);
pm.environment.set("refreshToken", response.refreshToken);
pm.environment.set("tokenIssuedAt", Date.now());

console.log("✅ Tokens refreshed");
```

## 9. Auto-Refresh Token (Advanced)

### Pre-request Script (Collection Level)

```javascript
const accessToken = pm.environment.get("accessToken");
const refreshToken = pm.environment.get("refreshToken");
const issuedAt = pm.environment.get("tokenIssuedAt");
const currentTime = Date.now();

// Token expires in 15 minutes
const tokenLifetime = 15 * 60 * 1000;

// Check if token will expire soon (within 1 minute)
if (issuedAt && (currentTime - issuedAt) > (tokenLifetime - 60000)) {
    console.log("🔄 Token expiring soon, refreshing...");

    // Refresh token request
    pm.sendRequest({
        url: pm.environment.get("baseUrl") + "/auth/refresh",
        method: "POST",
        header: {
            "Content-Type": "application/json"
        },
        body: {
            mode: "raw",
            raw: JSON.stringify({
                refreshToken: refreshToken
            })
        }
    }, function(err, response) {
        if (err) {
            console.error("❌ Failed to refresh token:", err);
            return;
        }

        const data = response.json();
        pm.environment.set("accessToken", data.accessToken);
        pm.environment.set("tokenIssuedAt", Date.now());
        console.log("✅ Token auto-refreshed");
    });
}
```

## 10. Security Best Practices

### ✅ DO

- **Store tokens in environment variables** (Current Value only)
- **Use HTTPS** always
- **Set reasonable expiration times** (15-60 minutes)
- **Implement refresh tokens** for better UX
- **Clear tokens on logout**
- **Validate token in tests**
- **Never log full tokens** (only first/last chars)

### ❌ DON'T

- Store tokens in Global variables
- Hardcode tokens in requests
- Share tokens between users
- Use tokens over HTTP (insecure)
- Store tokens in localStorage (XSS vulnerable)
- Ignore token expiration

## 11. Bài Tập Thực Hành

### Bài 1: Complete ReqRes Auth Flow

Create collection "ReqRes Authentication":

1. **Register User**
   ```
   POST {{baseUrl}}/register
   Body:
   {
     "email": "eve.holt@reqres.in",
     "password": "pistol"
   }
   ```

2. **Login**
   ```
   POST {{baseUrl}}/login
   Save token to environment
   ```

3. **Get User List (Authenticated)**
   ```
   GET {{baseUrl}}/users
   Authorization: Bearer {{accessToken}}
   ```

4. **Logout** (Clear token)
   ```javascript
   pm.environment.unset("accessToken");
   ```

### Bài 2: Error Handling

Test các scenarios:

1. Login without email → expect 400
2. Login without password → expect 400
3. Login with unregistered email → expect 400
4. Use expired/invalid token → expect 401

### Bài 3: Token Validation

```javascript
// Pre-request Script
const token = pm.environment.get("accessToken");

if (!token) {
    throw new Error("No access token. Please login first.");
}

// Optional: Check token format (JWT has 3 parts)
const parts = token.split('.');
if (parts.length !== 3) {
    console.warn("⚠️ Token format may be invalid");
}
```

### Bài 4: Login Flow with Newman

Export collection và environment, run với Newman:

```bash
newman run reqres-auth.json -e reqres-env.json
```

Should see:
```
✓ Login successful
✓ Token saved
✓ Get users with token
```

## 12. Common Errors

| Error | Nguyên nhân | Giải pháp |
|-------|-------------|-----------|
| **401 Unauthorized** | Token invalid/expired | Re-login |
| **403 Forbidden** | Token valid but no permission | Check role/permissions |
| **400 Bad Request** | Missing credentials | Add email/password |
| **Token not found** | Token not saved | Check Tests script |

## 13. Debugging Tips

### Console Logging

```javascript
// Check if token exists
const token = pm.environment.get("accessToken");
console.log("Current token:", token ? token.substring(0, 20) + "..." : "NONE");

// Check token age
const issuedAt = pm.environment.get("tokenIssuedAt");
if (issuedAt) {
    const age = Math.floor((Date.now() - issuedAt) / 1000);
    console.log(`Token age: ${age} seconds`);
}
```

### Postman Console

Open Console (`Ctrl/Cmd + Alt + C`) để xem:
- Request headers (có token không?)
- Response (token format đúng không?)
- Script logs
- Errors

## 14. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ Bearer Token và JWT là gì
- ✅ Cấu trúc JWT (header.payload.signature)
- ✅ Login flow: credentials → token
- ✅ Gửi token trong Authorization header
- ✅ Save token to environment
- ✅ Handle token expiration
- ✅ Refresh tokens
- ✅ Test authentication flows
- ✅ Security best practices

## 15. Kiến Thức Cần Nhớ

| Khái niệm | Giải thích |
|-----------|------------|
| **Bearer Token** | Access token gửi trong Authorization header |
| **JWT** | JSON Web Token - format token phổ biến |
| **Access Token** | Short-lived token (15-60 min) |
| **Refresh Token** | Long-lived token để lấy access token mới |
| **401** | Unauthorized - token invalid/expired |
| **exp** | Expiration time claim trong JWT |

## Next Steps

Tiếp tục học:
- **Bài tiếp theo**: [5.3 Basic Authentication](./basic-auth.md)

---

[⬅️ API Key](./api-key.md) | [Tổng Quan Chương 5](./README.md) | [Tiếp Theo: Basic Auth ➡️](./basic-auth.md)
