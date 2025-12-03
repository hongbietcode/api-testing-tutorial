# 5.5 Bài Tập Thực Hành Authentication

Tổng hợp các bài tập thực hành để củng cố kiến thức về authentication.

## Mục Tiêu

Sau khi hoàn thành các bài tập này, bạn sẽ:
- ✅ Thành thạo các phương thức authentication
- ✅ Xây dựng complete authentication flows
- ✅ Handle errors và edge cases
- ✅ Implement best practices
- ✅ Test real-world APIs

## Bài 1: ReqRes Login Flow (Cơ Bản)

### Mục Tiêu
Implement complete login flow với ReqRes API.

### API
- **Base URL**: https://reqres.in/api
- **Docs**: https://reqres.in

### Tasks

#### 1.1 Register User

**Request:**
```
Method: POST
URL: {{baseUrl}}/register

Body:
{
  "email": "eve.holt@reqres.in",
  "password": "pistol"
}
```

**Tests:**
```javascript
pm.test("Status code is 200", function() {
    pm.response.to.have.status(200);
});

pm.test("Response has id and token", function() {
    const response = pm.response.json();
    pm.expect(response).to.have.property("id");
    pm.expect(response).to.have.property("token");
});

// Save user ID
const response = pm.response.json();
pm.environment.set("userId", response.id);
pm.environment.set("token", response.token);
```

#### 1.2 Login

**Request:**
```
Method: POST
URL: {{baseUrl}}/login

Body:
{
  "email": "eve.holt@reqres.in",
  "password": "cityslicka"
}
```

**Tests:**
```javascript
pm.test("Login successful", function() {
    pm.response.to.have.status(200);
});

const response = pm.response.json();
pm.test("Token received", function() {
    pm.expect(response.token).to.exist;
});

// Save token
pm.environment.set("accessToken", response.token);
console.log("✅ Logged in, token:", response.token);
```

#### 1.3 Get User List (Authenticated)

**Request:**
```
Method: GET
URL: {{baseUrl}}/users?page=1

Headers:
Authorization: Bearer {{accessToken}}
```

**Tests:**
```javascript
pm.test("Authenticated request successful", function() {
    pm.response.to.have.status(200);
});

pm.test("Has user data", function() {
    const response = pm.response.json();
    pm.expect(response.data).to.be.an("array");
    pm.expect(response.data.length).to.be.above(0);
});
```

#### 1.4 Logout (Clear Token)

**Tests:**
```javascript
// Clear token
pm.environment.unset("accessToken");
pm.environment.unset("userId");
console.log("✅ Logged out, tokens cleared");
```

### Deliverable
- Collection "ReqRes Auth Flow" với 4 requests
- Environment với baseUrl variable
- Tất cả tests pass

---

## Bài 2: API Key Authentication (Trung Bình)

### Mục Tiêu
Test multiple APIs sử dụng API keys.

### APIs

#### Option 1: OpenWeatherMap
- Sign up: https://openweathermap.org/api
- Free tier: 60 calls/minute

#### Option 2: News API
- Sign up: https://newsapi.org
- Free tier: 100 requests/day

### Tasks

#### 2.1 Setup Environment

```
weatherApiKey = YOUR_WEATHER_KEY
newsApiKey = YOUR_NEWS_KEY
weatherBaseUrl = https://api.openweathermap.org/data/2.5
newsBaseUrl = https://newsapi.org/v2
```

#### 2.2 Weather API Request

**Request:**
```
Method: GET
URL: {{weatherBaseUrl}}/weather

Params:
  q = London
  appid = {{weatherApiKey}}
  units = metric
```

**Tests:**
```javascript
pm.test("Status is 200", function() {
    pm.response.to.have.status(200);
});

const response = pm.response.json();

pm.test("Has weather data", function() {
    pm.expect(response).to.have.property("weather");
    pm.expect(response).to.have.property("main");
});

pm.test("Temperature is present", function() {
    pm.expect(response.main.temp).to.be.a("number");
});

console.log(`Temperature in ${response.name}: ${response.main.temp}°C`);
```

#### 2.3 Test Invalid API Key

**Request:**
```
Params:
  q = London
  appid = invalid_key_123
```

**Tests:**
```javascript
pm.test("Returns 401 for invalid key", function() {
    pm.response.to.have.status(401);
});

pm.test("Error message exists", function() {
    const response = pm.response.json();
    pm.expect(response.message).to.exist;
});
```

### Deliverable
- Collection "API Key Tests"
- Tests cho valid và invalid keys
- Multiple APIs với different key locations (header vs query param)

---

## Bài 3: Basic Auth với HTTPBin (Trung Bình)

### Mục Tiêu
Test Basic Authentication với các scenarios khác nhau.

### API
- **Base URL**: https://httpbin.org

### Tasks

#### 3.1 Valid Credentials

**Request:**
```
Method: GET
URL: {{baseUrl}}/basic-auth/admin/secret123

Authorization:
  Type: Basic Auth
  Username: admin
  Password: secret123
```

**Tests:**
```javascript
pm.test("Authentication successful", function() {
    pm.response.to.have.status(200);
});

pm.test("User is authenticated", function() {
    const response = pm.response.json();
    pm.expect(response.authenticated).to.be.true;
    pm.expect(response.user).to.eql("admin");
});
```

#### 3.2 Invalid Password

```
Authorization:
  Username: admin
  Password: wrongpassword
```

**Tests:**
```javascript
pm.test("Returns 401", function() {
    pm.response.to.have.status(401);
});
```

#### 3.3 Missing Credentials

```
Authorization: No Auth
```

**Tests:**
```javascript
pm.test("Returns 401 without auth", function() {
    pm.response.to.have.status(401);
});

pm.test("WWW-Authenticate header present", function() {
    pm.expect(pm.response.headers.has("WWW-Authenticate")).to.be.true;
});
```

#### 3.4 Manual Base64 Encoding

**Pre-request Script:**
```javascript
const username = "testuser";
const password = "testpass";
const encoded = btoa(username + ":" + password);
pm.environment.set("basicAuth", "Basic " + encoded);
console.log("Encoded:", encoded);
```

**Headers:**
```
Authorization: {{basicAuth}}
```

**Request:**
```
GET {{baseUrl}}/basic-auth/testuser/testpass
```

### Deliverable
- Collection "Basic Auth Tests"
- Test valid/invalid/missing credentials
- Manual encoding demo

---

## Bài 4: Multi-Auth Collection (Nâng Cao)

### Mục Tiêu
Tạo collection sử dụng multiple authentication methods.

### Structure

```
📁 Multi-Auth API Tests
  📁 Public Endpoints (No Auth)
    - GET Public Data
  📁 API Key Endpoints
    - GET Weather
    - GET News
  📁 Bearer Token Endpoints
    - POST Login
    - GET User Profile
    - PUT Update Profile
  📁 Basic Auth Endpoints
    - GET Protected Resource
```

### Requirements

1. **Collection-level variable:** `authType`
2. **Pre-request Script** (collection level):
```javascript
const authType = pm.collectionVariables.get("authType");

switch(authType) {
    case "bearer":
        pm.request.headers.add({
            key: "Authorization",
            value: "Bearer " + pm.environment.get("accessToken")
        });
        break;
    case "apikey":
        pm.request.headers.add({
            key: "X-API-Key",
            value: pm.environment.get("apiKey")
        });
        break;
    case "basic":
        const username = pm.environment.get("username");
        const password = pm.environment.get("password");
        const encoded = btoa(username + ":" + password);
        pm.request.headers.add({
            key: "Authorization",
            value: "Basic " + encoded
        });
        break;
    default:
        // No auth
}
```

### Deliverable
- Collection với 4 folders
- Dynamic authentication based on `authType`
- Documentation cho mỗi endpoint

---

## Bài 5: Token Expiration Handling (Nâng Cao)

### Mục Tiêu
Implement automatic token refresh logic.

### Scenario

API returns:
```json
{
  "accessToken": "short_lived_token",
  "refreshToken": "long_lived_token",
  "expiresIn": 900  // 15 minutes
}
```

### Tasks

#### 5.1 Login và Save Tokens

**Tests:**
```javascript
const response = pm.response.json();

pm.environment.set("accessToken", response.accessToken);
pm.environment.set("refreshToken", response.refreshToken);

// Calculate expiration time
const expiresAt = Date.now() + (response.expiresIn * 1000);
pm.environment.set("tokenExpiresAt", expiresAt);

console.log("Token expires at:", new Date(expiresAt));
```

#### 5.2 Pre-request Check (Collection Level)

```javascript
const expiresAt = pm.environment.get("tokenExpiresAt");
const now = Date.now();

// Check if token expires within next 60 seconds
if (expiresAt && (expiresAt - now) < 60000) {
    console.log("⚠️ Token expiring soon, should refresh");

    // Option 1: Throw error and ask user to refresh
    throw new Error("Token expired. Please run login request.");

    // Option 2: Auto-refresh (advanced)
    // pm.sendRequest({ ... refresh token request ... });
}
```

#### 5.3 Refresh Token Request

```
Method: POST
URL: {{baseUrl}}/auth/refresh

Body:
{
  "refreshToken": "{{refreshToken}}"
}
```

**Tests:**
```javascript
const response = pm.response.json();

pm.environment.set("accessToken", response.accessToken);
const expiresAt = Date.now() + (response.expiresIn * 1000);
pm.environment.set("tokenExpiresAt", expiresAt);

console.log("✅ Token refreshed");
```

### Deliverable
- Login flow với token expiration tracking
- Refresh token endpoint
- Pre-request script để check expiration

---

## Bài 6: Security Testing (Nâng Cao)

### Mục Tiêu
Test common authentication vulnerabilities.

### Test Cases

#### 6.1 Brute Force Protection

Gửi multiple login requests với wrong password:

**Collection Runner:**
- Iterations: 10
- Delay: 100ms

**Tests:**
```javascript
if (pm.info.iteration >= 5) {
    pm.test("Should rate limit after 5 attempts", function() {
        pm.expect([429, 403]).to.include(pm.response.code);
    });
}
```

#### 6.2 Token Reuse After Logout

**Flow:**
1. Login → Save token
2. Logout (server invalidates token)
3. Try to use old token → Should fail

**Tests:**
```javascript
pm.test("Old token is invalid", function() {
    pm.response.to.have.status(401);
});
```

#### 6.3 Expired Token

**Pre-request:**
```javascript
// Set expired token
pm.environment.set("accessToken", "expired_token_from_yesterday");
```

**Tests:**
```javascript
pm.test("Expired token rejected", function() {
    pm.response.to.have.status(401);
});

pm.test("Error indicates expiration", function() {
    const response = pm.response.json();
    pm.expect(response.error).to.match(/expired|invalid/i);
});
```

#### 6.4 SQL Injection in Login

**Request:**
```json
{
  "email": "' OR '1'='1",
  "password": "' OR '1'='1"
}
```

**Tests:**
```javascript
pm.test("SQL injection prevented", function() {
    pm.expect([400, 401]).to.include(pm.response.code);
    // Should NOT return 200 with admin access
});
```

### Deliverable
- Collection "Security Tests"
- Tests cho các vulnerabilities
- Documentation về findings

---

## Bài 7: Real-World API Integration (Pro Level)

### Mục Tiêu
Integrate với real production API.

### Suggested APIs

1. **GitHub API**
   - Docs: https://docs.github.com/en/rest
   - Auth: Personal Access Token

2. **Spotify API**
   - Docs: https://developer.spotify.com/documentation/web-api
   - Auth: OAuth 2.0

3. **Trello API**
   - Docs: https://developer.atlassian.com/cloud/trello/rest
   - Auth: API Key + Token

### Example: GitHub API

#### 7.1 Setup

Environment:
```
githubToken = YOUR_PERSONAL_ACCESS_TOKEN
githubBaseUrl = https://api.github.com
```

#### 7.2 Get Authenticated User

```
Method: GET
URL: {{githubBaseUrl}}/user

Headers:
Authorization: Bearer {{githubToken}}
Accept: application/vnd.github+json
```

#### 7.3 List Repositories

```
GET {{githubBaseUrl}}/user/repos
```

#### 7.4 Create Repository

```
Method: POST
URL: {{githubBaseUrl}}/user/repos

Body:
{
  "name": "test-repo-from-postman",
  "description": "Created via Postman API",
  "private": false
}
```

#### 7.5 Delete Repository

```
Method: DELETE
URL: {{githubBaseUrl}}/repos/{{username}}/test-repo-from-postman
```

### Deliverable
- Working integration với real API
- Complete CRUD operations
- Error handling
- Documentation

---

## Bài 8: Environment Management (Best Practices)

### Mục Tiêu
Setup secure environment management.

### Tasks

#### 8.1 Create 3 Environments

**Development:**
```
baseUrl = http://localhost:3000
apiKey = dev_key_123
username = dev_user
password = <CURRENT_VALUE_ONLY>
```

**Staging:**
```
baseUrl = https://staging-api.example.com
apiKey = staging_key_456
username = staging_user
password = <CURRENT_VALUE_ONLY>
```

**Production:**
```
baseUrl = https://api.example.com
apiKey = prod_key_789
username = prod_user
password = <CURRENT_VALUE_ONLY>
```

#### 8.2 Safety Script (Collection Pre-request)

```javascript
const env = pm.environment.name;

// Prevent destructive operations in production
const destructiveMethods = ["DELETE", "PUT", "PATCH"];
const isDestructive = destructiveMethods.includes(pm.request.method);

if (env === "Production" && isDestructive) {
    const confirmed = pm.environment.get("confirmProduction");

    if (confirmed !== "YES") {
        throw new Error("🚨 STOP! Destructive operation in Production. Set 'confirmProduction=YES' to proceed.");
    }
}

console.log(`Running in: ${env}`);
```

### Deliverable
- 3 environments properly configured
- Safety scripts
- Documentation về best practices

---

## Đánh Giá và Nộp Bài

### Checklist

- [ ] Tất cả collections có proper naming
- [ ] Environments được setup đúng
- [ ] Sensitive data trong Current Value only
- [ ] Tests cover happy path và error cases
- [ ] Console logs hữu ích
- [ ] Documentation đầy đủ
- [ ] Collection có thể run với Collection Runner
- [ ] Export collections và environments

### Nộp Bài

Export:
1. Collection JSON files
2. Environment JSON files (với Initial Values trống cho sensitive data)
3. README.md giải thích cách setup và run

---

## Tổng Kết

Sau khi hoàn thành các bài tập này, bạn đã:
- ✅ Master tất cả auth methods: No Auth, API Key, Bearer Token, Basic Auth, OAuth 2.0
- ✅ Implement complete authentication flows
- ✅ Handle tokens, expiration, refresh
- ✅ Test security vulnerabilities
- ✅ Integrate với real APIs
- ✅ Follow best practices

**Chúc mừng bạn đã hoàn thành Chương 5! 🎉**

---

[⬅️ OAuth 2.0](./oauth-2.md) | [Tổng Quan Chương 5](./README.md) | [Tiếp Theo: Chương 6 ➡️](../06-testing-nang-cao/README.md)
