# 3.4 Environments và Variables

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Hiểu Environments và Variables là gì
- ✅ Tạo và quản lý nhiều environments
- ✅ Sử dụng các loại variables: Global, Collection, Environment
- ✅ Quản lý sensitive data an toàn
- ✅ Switch giữa các environments dễ dàng

## 1. Tại Sao Cần Environments?

### Vấn Đề

Khi develop một API, bạn thường có nhiều môi trường:

```
Development:  http://localhost:3000
Staging:      https://staging-api.example.com
Production:   https://api.example.com
```

Nếu không dùng environments, bạn phải:
- ❌ Thay đổi URL thủ công mỗi lần switch môi trường
- ❌ Duplicate requests cho mỗi môi trường
- ❌ Dễ nhầm lẫn và test nhầm môi trường

### Giải Pháp: Environments

```
Request URL: {{baseUrl}}/users

[Development] baseUrl = http://localhost:3000
[Staging]     baseUrl = https://staging-api.example.com
[Production]  baseUrl = https://api.example.com
```

Chỉ cần switch environment, tất cả requests tự động đổi URL!

## 2. Environment Là Gì?

**Environment** là một tập hợp các variables với values khác nhau cho từng môi trường.

### Ví dụ Environment

**Development Environment:**
```
baseUrl = http://localhost:3000
apiKey = dev_test_key_12345
timeout = 5000
```

**Production Environment:**
```
baseUrl = https://api.example.com
apiKey = prod_live_key_67890
timeout = 30000
```

## 3. Tạo Environment

### Bước 1: Mở Environments

1. Click vào **Environments** tab ở sidebar
2. Hoặc nhấn `Ctrl/Cmd + E`

### Bước 2: Create New Environment

1. Click **"+"** hoặc **Create Environment**
2. Đặt tên: "Development"
3. Thêm variables:

| Variable | Initial Value | Current Value |
|----------|---------------|---------------|
| baseUrl | http://localhost:3000 | http://localhost:3000 |
| apiKey | dev_key_123 | dev_key_123 |

4. Click **Save** (`Ctrl/Cmd + S`)

### Bước 3: Tạo Thêm Environments

Tạo tương tự cho Staging và Production:

**Staging:**
```
baseUrl = https://staging-api.example.com
apiKey = staging_key_456
```

**Production:**
```
baseUrl = https://api.example.com
apiKey = prod_key_789
```

## 4. Sử Dụng Environment

### Chọn Environment

Ở góc trên bên phải, click dropdown và chọn environment:
```
[No Environment ▼] → [Development ▼]
```

### Sử Dụng Variables Trong Request

Thay vì hardcode:
```
❌ http://localhost:3000/users
```

Dùng variable:
```
✅ {{baseUrl}}/users
```

Postman sẽ tự thay thế `{{baseUrl}}` bằng giá trị từ environment đang chọn.

### Xem Giá Trị Variables

Click icon "eye" 👁️ để xem:
```
Environment: Development

baseUrl: http://localhost:3000
apiKey: dev_key_123
```

## 5. Các Loại Variables

Postman có 4 loại variables với scope khác nhau:

### 1. Global Variables

**Scope**: Tất cả collections và environments

**Khi nào dùng:**
- Variables dùng chung cho mọi request
- Config không thay đổi theo môi trường

**Ví dụ:**
```
timestamp = 1234567890
userId = 12345
```

**Cách tạo:**
1. Click **Environments** → **Globals**
2. Thêm variables
3. Click **Save**

### 2. Collection Variables

**Scope**: Chỉ trong một collection

**Khi nào dùng:**
- Variables dùng chung trong collection
- Config chung nhưng khác với collections khác

**Ví dụ:**
```
apiVersion = v1
contentType = application/json
```

**Cách tạo:**
1. Click vào collection
2. Tab **Variables**
3. Thêm variables
4. Click **Save**

### 3. Environment Variables

**Scope**: Trong environment đang chọn

**Khi nào dùng:**
- Variables thay đổi theo môi trường
- URLs, API keys, configs khác nhau

**Ví dụ:**
```
baseUrl = http://localhost:3000
apiKey = dev_key_123
dbName = test_db
```

### 4. Local Variables (trong Scripts)

**Scope**: Chỉ trong request đó (scripts)

**Khi nào dùng:**
- Temporary variables trong tests
- Data chỉ dùng một lần

**Ví dụ:**
```javascript
// Trong Pre-request Script hoặc Tests
pm.variables.set("tempToken", "abc123");
```

### Variable Scope Hierarchy

```
Local > Environment > Collection > Global
```

Nếu có cùng tên, variable ở scope cao hơn sẽ được ưu tiên.

**Ví dụ:**
```
Global:      baseUrl = https://global.com
Collection:  baseUrl = https://collection.com
Environment: baseUrl = https://env.com

→ Postman sẽ dùng: https://env.com (Environment có priority cao hơn)
```

## 6. Initial Value vs Current Value

Khi tạo variable, có 2 fields:

### Initial Value
- Giá trị được **sync** khi share/export
- Hiển thị cho mọi người
- ✅ **An toàn** để chia sẻ

### Current Value
- Giá trị **local** trên máy bạn
- **Không sync** khi share/export
- ⚠️ Dùng cho **sensitive data**

### Best Practice

**Public data (URLs, configs):**
```
Variable: baseUrl
Initial Value: https://api.example.com
Current Value: https://api.example.com
```

**Sensitive data (API keys, passwords):**
```
Variable: apiKey
Initial Value: <leave empty or use placeholder>
Current Value: actual_secret_key_123
```

Khi export/share, người khác sẽ thấy:
```
apiKey = <empty>
```

Họ phải tự fill vào Current Value.

## 7. Sử Dụng Variables

### Trong Request URL

```
{{baseUrl}}/users/{{userId}}
```

### Trong Query Parameters

```
URL: {{baseUrl}}/posts
Params:
  - userId: {{userId}}
  - _limit: {{limit}}

→ Kết quả: https://api.example.com/posts?userId=1&_limit=10
```

### Trong Headers

```
Authorization: Bearer {{accessToken}}
Content-Type: {{contentType}}
```

### Trong Body

```json
{
  "email": "{{userEmail}}",
  "apiKey": "{{apiKey}}"
}
```

### Trong Scripts

```javascript
// Get variable
const baseUrl = pm.variables.get("baseUrl");
const userId = pm.variables.get("userId");

// Set variable
pm.variables.set("userId", 123);
pm.environment.set("accessToken", "abc123");
pm.collectionVariables.set("version", "v2");
pm.globals.set("timestamp", Date.now());
```

## 8. Quản Lý Environments

### Duplicate Environment

1. Hover vào environment
2. Click **"..."** → **Duplicate**
3. Rename: "Development Copy"

Hữu ích khi cần test với config tương tự nhưng khác chút.

### Export Environment

1. Hover vào environment
2. Click **"..."** → **Export**
3. Save file `.json`

### Import Environment

1. Click **Import** button
2. Chọn file environment `.json`
3. Click **Import**

### Delete Environment

1. Hover vào environment
2. Click **"..."** → **Delete**
3. Confirm

## 9. Thực Hành: Setup Environments

### Bài tập 1: Tạo 3 Environments

Tạo environments cho JSONPlaceholder API:

**1. Development:**
```
baseUrl = https://jsonplaceholder.typicode.com
timeout = 5000
apiVersion = v1
```

**2. Staging (giả lập):**
```
baseUrl = https://staging.jsonplaceholder.typicode.com
timeout = 10000
apiVersion = v1
```

**3. Production (giả lập):**
```
baseUrl = https://jsonplaceholder.typicode.com
timeout = 30000
apiVersion = v1
```

### Bài tập 2: Update Collection

1. Mở collection "JSONPlaceholder API Tests" (từ bài trước)
2. Update tất cả URLs thành:
   ```
   ❌ https://jsonplaceholder.typicode.com/users
   ✅ {{baseUrl}}/users
   ```
3. Test với từng environment:
   - Chọn Development → Send requests
   - Chọn Staging → Send requests
   - Chọn Production → Send requests

### Bài tập 3: Authentication Simulation

Tạo environment với authentication:

**Development:**
```
baseUrl = https://api.example.com
apiKey = dev_key_abc123
bearerToken = <empty>
username = test_user
password = <empty in Initial, fill in Current>
```

Thêm vào request Headers:
```
Authorization: Bearer {{bearerToken}}
X-API-Key: {{apiKey}}
```

## 10. Advanced: Scripts với Variables

### Pre-request Script: Set Dynamic Variables

```javascript
// Set timestamp
pm.environment.set("timestamp", Date.now());

// Set random user ID
pm.environment.set("randomUserId", Math.floor(Math.random() * 10) + 1);

// Set request ID
pm.environment.set("requestId", pm.variables.replaceIn("{{$guid}}"));
```

### Tests: Extract Data to Variables

```javascript
// Extract từ response
const response = pm.response.json();

// Save to environment
pm.environment.set("userId", response.id);
pm.environment.set("userName", response.name);
pm.environment.set("userEmail", response.email);

console.log("Saved userId:", response.id);
```

### Chain Requests với Variables

**Request 1: Login**
```javascript
// Tests
const response = pm.response.json();
pm.environment.set("accessToken", response.token);
pm.environment.set("userId", response.user.id);
```

**Request 2: Get User Profile**
```
URL: {{baseUrl}}/users/{{userId}}
Headers:
  Authorization: Bearer {{accessToken}}
```

Bây giờ Request 2 sẽ dùng token và userId từ Request 1!

## 11. Built-in Dynamic Variables

Postman cung cấp variables tự động:

```
{{$guid}}          → Random GUID
{{$timestamp}}     → Unix timestamp (seconds)
{{$randomInt}}     → Random integer (0-1000)
{{$randomEmail}}   → Random email
{{$randomFirstName}} → Random first name
{{$randomLastName}}  → Random last name
{{$randomPhoneNumber}} → Random phone
{{$randomColor}}   → Random color name
```

**Ví dụ sử dụng:**
```json
{
  "id": "{{$guid}}",
  "email": "{{$randomEmail}}",
  "firstName": "{{$randomFirstName}}",
  "createdAt": {{$timestamp}}
}
```

## 12. Security Best Practices

### ✅ DO

- Dùng Current Value cho API keys, passwords
- Không commit environment files có secrets
- Dùng `.gitignore` cho `*.postman_environment.json`
- Rotate API keys thường xuyên
- Dùng environment variables thay vì hardcode

### ❌ DON'T

- Lưu passwords trong Initial Value
- Share environments có sensitive data
- Commit secrets vào Git
- Dùng production credentials trong tests
- Hardcode API keys trong requests

### Secure Workflow

1. Create environment với placeholders:
```
apiKey = <YOUR_API_KEY_HERE>
password = <YOUR_PASSWORD_HERE>
```

2. Export và share environment

3. Mỗi người tự fill Current Values:
```
apiKey = actual_key_abc123
password = actual_password_xyz789
```

## 13. Tips và Tricks

### Tip 1: Quick Environment Switch

Keyboard shortcut: `Ctrl/Cmd + E` → Select environment

### Tip 2: Check Variable Values

Hover chuột lên `{{variableName}}` để xem giá trị hiện tại.

### Tip 3: Use Console to Debug

```javascript
console.log("baseUrl:", pm.variables.get("baseUrl"));
console.log("All env vars:", pm.environment.toObject());
```

### Tip 4: Reset Environment

Để reset tất cả variables:
1. Click vào environment
2. Click **"..."** → **Reset**
3. Confirm

## 14. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ Tạo và quản lý environments
- ✅ Sử dụng 4 loại variables: Global, Collection, Environment, Local
- ✅ Hiểu Initial vs Current values
- ✅ Switch giữa environments dễ dàng
- ✅ Bảo mật sensitive data
- ✅ Chain requests với variables
- ✅ Dùng built-in dynamic variables

## 15. Cheat Sheet

| Task | Command |
|------|---------|
| Mở Environments | `Ctrl/Cmd + E` |
| Sử dụng variable | `{{variableName}}` |
| Get variable (script) | `pm.variables.get("name")` |
| Set env variable | `pm.environment.set("name", "value")` |
| Set global | `pm.globals.set("name", "value")` |
| Set collection var | `pm.collectionVariables.set("name", "value")` |
| Random GUID | `{{$guid}}` |
| Timestamp | `{{$timestamp}}` |

## Next Steps

Bây giờ bạn đã biết environments và variables, hãy tiếp tục:
- **Bài tiếp theo**: [3.5 Request Configuration](./request-configuration.md)

---

[⬅️ Collections](./collections.md) | [Tổng Quan Chương 3](./README.md) | [Tiếp Theo: Request Configuration ➡️](./request-configuration.md)
