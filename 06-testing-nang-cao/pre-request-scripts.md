# 6.2 Pre-request Scripts

Pre-request Scripts cho phép bạn chạy JavaScript code **TRƯỚC** khi gửi request, giúp generate dynamic data, set variables, hoặc prepare request.

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Hiểu Pre-request Scripts là gì và khi nào dùng
- ✅ Generate dynamic data (timestamps, random values)
- ✅ Set variables trước khi request
- ✅ Prepare authentication tokens
- ✅ Log debugging information

## 1. Pre-request Scripts Tab

> **📸 HÌNH ẢNH:** Pre-request Scripts Tab
> - File: `pre-request-scripts-tab.png`
> - Nội dung: Screenshot của Pre-request Scripts tab trong Postman, showing code editor

<!-- IMAGE_PLACEHOLDER: pre-request-scripts-tab.png -->

**Pre-request Scripts** chạy **TRƯỚC** request được gửi đi.

### Execution Flow

```
Pre-request Script → Request → Response → Tests
```

> **📸 HÌNH ẢNH:** Execution Flow Diagram
> - File: `request-execution-flow.png`
> - Nội dung: Flowchart showing: Pre-request Script (blue) → HTTP Request (green) → Server Response (orange) → Tests (purple)

<!-- IMAGE_PLACEHOLDER: request-execution-flow.png -->

## 2. Set Timestamps

### Current Timestamp

```javascript
// Unix timestamp (milliseconds)
pm.environment.set("timestamp", Date.now());

// Unix timestamp (seconds)
pm.environment.set("timestampSec", Math.floor(Date.now() / 1000));

// ISO format
pm.environment.set("isoDate", new Date().toISOString());

console.log("Request timestamp:", Date.now());
```

**Sử dụng trong request:**
```json
{
  "createdAt": {{timestamp}},
  "date": "{{isoDate}}"
}
```

### Future/Past Dates

```javascript
// 7 days from now
const futureDate = new Date();
futureDate.setDate(futureDate.getDate() + 7);
pm.environment.set("expiryDate", futureDate.toISOString());

// 30 days ago
const pastDate = new Date();
pastDate.setDate(pastDate.getDate() - 30);
pm.environment.set("startDate", pastDate.toISOString());
```

## 3. Generate Random Data

### Random Numbers

```javascript
// Random integer between 0-999
const randomId = Math.floor(Math.random() * 1000);
pm.environment.set("randomId", randomId);

// Random between min-max
function randomInt(min, max) {
    return Math.floor(Math.random() * (max - min + 1)) + min;
}
pm.environment.set("age", randomInt(18, 65));
```

### Random Strings

```javascript
// Random string (using built-in)
pm.environment.set("requestId", pm.variables.replaceIn("{{$guid}}"));

// Custom random string
function randomString(length) {
    const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
    let result = '';
    for (let i = 0; i < length; i++) {
        result += chars.charAt(Math.floor(Math.random() * chars.length));
    }
    return result;
}
pm.environment.set("apiKey", randomString(32));
```

### Postman Dynamic Variables

```javascript
// Use built-in dynamic variables
pm.environment.set("randomEmail", pm.variables.replaceIn("{{$randomEmail}}"));
pm.environment.set("randomFirstName", pm.variables.replaceIn("{{$randomFirstName}}"));
pm.environment.set("randomLastName", pm.variables.replaceIn("{{$randomLastName}}"));
pm.environment.set("randomPhoneNumber", pm.variables.replaceIn("{{$randomPhoneNumber}}"));
pm.environment.set("randomUUID", pm.variables.replaceIn("{{$guid}}"));
```

**Available Dynamic Variables:**
- `{{$guid}}` - GUID
- `{{$timestamp}}` - Unix timestamp
- `{{$randomInt}}` - Random integer 0-1000
- `{{$randomEmail}}` - Random email
- `{{$randomFirstName}}` - Random first name
- `{{$randomLastName}}` - Random last name
- `{{$randomFullName}}` - Random full name
- `{{$randomPhoneNumber}}` - Random phone
- `{{$randomCity}}` - Random city
- `{{$randomCountry}}` - Random country
- `{{$randomColor}}` - Random color

## 4. Set Authentication

### Generate Basic Auth

```javascript
const username = pm.environment.get("username");
const password = pm.environment.get("password");

// Encode to Base64
const credentials = btoa(username + ":" + password);
pm.environment.set("basicAuth", "Basic " + credentials);
```

**Use in Headers:**
```
Authorization: {{basicAuth}}
```

### Generate Signature (HMAC)

```javascript
const CryptoJS = require('crypto-js');

const message = "request data";
const secret = pm.environment.get("apiSecret");

// HMAC SHA256
const signature = CryptoJS.HmacSHA256(message, secret).toString();
pm.environment.set("signature", signature);
```

## 5. Request ID Generation

```javascript
// Generate unique request ID
const requestId = `req_${Date.now()}_${Math.random().toString(36).substr(2, 9)}`;
pm.environment.set("requestId", requestId);

console.log("Request ID:", requestId);
```

## 6. Conditional Logic

### Check Environment

```javascript
const env = pm.environment.name;

if (env === "Production") {
    console.log("⚠️ Running in Production!");
    pm.environment.set("apiUrl", "https://api.example.com");
} else {
    console.log("Running in " + env);
    pm.environment.set("apiUrl", "https://dev-api.example.com");
}
```

### Check Variable Exists

```javascript
const token = pm.environment.get("accessToken");

if (!token) {
    console.error("❌ No access token found!");
    throw new Error("Please login first to get access token");
}

console.log("✅ Using token:", token.substring(0, 20) + "...");
```

### Validate Before Request

```javascript
// Check required variables
const requiredVars = ["userId", "apiKey", "baseUrl"];

requiredVars.forEach(varName => {
    const value = pm.environment.get(varName);
    if (!value) {
        throw new Error(`Missing required variable: ${varName}`);
    }
});

console.log("✅ All required variables are set");
```

## 7. Data Transformation

### Format Data

```javascript
// Get raw data
const rawDate = "2024-01-15";

// Transform to different format
const dateObj = new Date(rawDate);
const formattedDate = dateObj.toLocaleDateString('en-US');
pm.environment.set("displayDate", formattedDate);
```

### Build Complex Objects

```javascript
// Build request payload
const user = {
    id: pm.variables.replaceIn("{{$guid}}"),
    name: pm.variables.replaceIn("{{$randomFullName}}"),
    email: pm.variables.replaceIn("{{$randomEmail}}"),
    createdAt: new Date().toISOString(),
    isActive: true
};

// Save as JSON string if needed
pm.environment.set("userData", JSON.stringify(user));
```

## 8. Counter và Iteration

### Simple Counter

```javascript
// Get current counter value
let counter = pm.environment.get("requestCounter") || 0;

// Increment
counter++;

// Save back
pm.environment.set("requestCounter", counter);

console.log("Request #" + counter);
```

### Iteration Data

```javascript
// Useful in Collection Runner
const iteration = pm.info.iteration;
const iterationCount = pm.info.iterationCount;

console.log(`Running iteration ${iteration + 1} of ${iterationCount}`);

// Different behavior per iteration
if (iteration === 0) {
    console.log("First iteration - setup");
} else if (iteration === iterationCount - 1) {
    console.log("Last iteration - cleanup");
}
```

## 9. External Data Processing

### Process CSV Data

When using data files in Collection Runner:

```javascript
// Access data file variables
const userName = pm.iterationData.get("name");
const userEmail = pm.iterationData.get("email");

console.log("Creating user:", userName, userEmail);
```

### Default Values

```javascript
// Set defaults if data not provided
const name = pm.iterationData.get("name") || "Default User";
const age = pm.iterationData.get("age") || 25;

pm.environment.set("userName", name);
pm.environment.set("userAge", age);
```

## 10. Complete Examples

### Example 1: Create User with Random Data

```javascript
// Generate random user data
const firstName = pm.variables.replaceIn("{{$randomFirstName}}");
const lastName = pm.variables.replaceIn("{{$randomLastName}}");
const email = `${firstName.toLowerCase()}.${lastName.toLowerCase()}@test.com`;
const phone = pm.variables.replaceIn("{{$randomPhoneNumber}}");

// Set environment variables
pm.environment.set("firstName", firstName);
pm.environment.set("lastName", lastName);
pm.environment.set("email", email);
pm.environment.set("phone", phone);

console.log("Creating user:", firstName, lastName);
console.log("Email:", email);
```

**Request Body:**
```json
{
  "firstName": "{{firstName}}",
  "lastName": "{{lastName}}",
  "email": "{{email}}",
  "phone": "{{phone}}"
}
```

### Example 2: Token Expiration Check

```javascript
// Check if token is expired
const tokenExpiry = pm.environment.get("tokenExpiry");
const now = Date.now();

if (tokenExpiry && now > tokenExpiry) {
    console.warn("⚠️ Token expired!");
    // Clear expired token
    pm.environment.unset("accessToken");
    throw new Error("Token expired. Please re-authenticate.");
}

console.log("✅ Token is valid");
```

### Example 3: API Rate Limiting

```javascript
// Track API calls to avoid rate limiting
const lastRequestTime = pm.environment.get("lastRequestTime") || 0;
const now = Date.now();
const minInterval = 100; // ms between requests

const timeSinceLastRequest = now - lastRequestTime;

if (timeSinceLastRequest < minInterval) {
    const waitTime = minInterval - timeSinceLastRequest;
    console.log(`Waiting ${waitTime}ms to avoid rate limit...`);
    // Note: Postman doesn't support sleep, this is just logging
}

pm.environment.set("lastRequestTime", now);
```

## 11. Debugging Pre-request Scripts

### Console Logging

```javascript
console.log("=== Pre-request Script Start ===");
console.log("Environment:", pm.environment.name);
console.log("Current user:", pm.environment.get("userId"));
console.log("Timestamp:", new Date().toISOString());

// Log all environment variables
const allVars = pm.environment.toObject();
console.log("Environment variables:", allVars);

console.log("=== Pre-request Script End ===");
```

> **📸 HÌNH ẢNH:** Console Output with Pre-request Logs
> - File: `pre-request-console-output.png`
> - Nội dung: Postman Console showing pre-request script logs, variables, và timestamps

<!-- IMAGE_PLACEHOLDER: pre-request-console-output.png -->

### Error Handling

```javascript
try {
    // Your code
    const token = pm.environment.get("token");
    if (!token) {
        throw new Error("No token found");
    }

    // Process token
    console.log("✅ Token processed");

} catch (error) {
    console.error("❌ Error:", error.message);
    // Optionally stop execution
    // throw error;
}
```

## 12. Collection-Level Pre-request Scripts

Set pre-request script cho **toàn bộ collection**:

1. Click vào Collection
2. Tab **Pre-request Scripts**
3. Write code
4. Save

**Code chạy cho MỌI request trong collection!**

### Example: Collection-Level Auth Check

```javascript
// This runs before EVERY request in collection
const token = pm.environment.get("accessToken");
const tokenExpiry = pm.environment.get("tokenExpiry");

// Skip auth check for login endpoint
const isLoginRequest = pm.request.url.toString().includes("/login");

if (!isLoginRequest) {
    if (!token) {
        throw new Error("No access token. Please run login request first.");
    }

    if (Date.now() > tokenExpiry) {
        throw new Error("Access token expired. Please re-login.");
    }
}
```

## 13. Best Practices

### ✅ DO

- Use pre-request scripts để generate dynamic data
- Set variables thay vì hardcode trong request
- Log useful debugging info
- Validate required variables exist
- Clean up unused variables
- Use built-in dynamic variables khi có thể
- Comment code rõ ràng

### ❌ DON'T

- Make API calls trong pre-request (nặng, slow)
- Store sensitive data trong logs
- Duplicate logic across multiple requests (dùng collection-level)
- Forget error handling
- Generate data không deterministic cho critical tests

## 14. Common Use Cases

| Use Case | Example |
|----------|---------|
| **Timestamps** | Created date, expiry time |
| **Random Data** | Test với data khác nhau mỗi lần |
| **Authentication** | Generate auth headers, signatures |
| **Request IDs** | Unique identifier cho mỗi request |
| **Validation** | Check variables exist trước request |
| **Counters** | Track số lần request |
| **Environment Setup** | Set URLs based on environment |
| **Data Formatting** | Transform data trước gửi |

## 15. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ Pre-request scripts chạy TRƯỚC request
- ✅ Generate dynamic data (timestamps, random values)
- ✅ Set environment variables
- ✅ Validate và prepare requests
- ✅ Debug với console.log
- ✅ Collection-level vs request-level scripts
- ✅ Best practices

## Next Steps

- **Bài tiếp theo**: [6.3 Variables và Data Management](./variables-data-management.md)

---

[⬅️ Test Scripts](./viet-test-scripts.md) | [Tổng Quan Chương 6](./README.md) | [Tiếp Theo: Variables ➡️](./variables-data-management.md)
