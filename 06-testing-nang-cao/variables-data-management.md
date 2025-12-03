# 6.3 Variables và Data Management

Variables là trái tim của Postman automation. Hiểu cách quản lý variables giúp bạn tạo được test suites linh hoạt và maintainable.

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Hiểu 4 loại variables: Global, Collection, Environment, Local
- ✅ Biết variable scope hierarchy
- ✅ Set và get variables trong scripts
- ✅ Chain requests với variables
- ✅ Manage data flow giữa requests

## 1. Variable Scopes

> **📸 HÌNH ẢNH:** Variable Scopes Diagram
> - File: `variable-scopes-hierarchy.png`
> - Nội dung: Diagram showing 4 levels: Local (top priority) → Environment → Collection → Global (lowest priority), với arrows indicating scope

<!-- IMAGE_PLACEHOLDER: variable-scopes-hierarchy.png -->

Postman có 4 loại variables với scopes khác nhau:

```
Priority (cao → thấp):
Local > Environment > Collection > Global
```

### Global Variables

**Scope**: Tất cả collections và requests

**Khi nào dùng:**
- Data dùng chung toàn workspace
- Constants không thay đổi

**Ví dụ:**
```javascript
pm.globals.set("companyName", "ACME Corp");
pm.globals.set("apiVersion", "v1");
```

### Collection Variables

**Scope**: Chỉ trong collection đó

**Khi nào dùng:**
- Data specific cho collection
- Config chung trong collection

**Ví dụ:**
```javascript
pm.collectionVariables.set("baseUrl", "https://api.example.com");
pm.collectionVariables.set("timeout", 5000);
```

### Environment Variables

**Scope**: Trong environment đang active

**Khi nào dùng:**
- Data thay đổi theo môi trường (dev/staging/prod)
- Credentials, API keys

**Ví dụ:**
```javascript
pm.environment.set("apiKey", "dev_key_123");
pm.environment.set("dbName", "test_db");
```

### Local Variables

**Scope**: Chỉ trong script đó (temporary)

**Khi nào dùng:**
- Temporary data trong scripts
- Intermediate calculations

**Ví dụ:**
```javascript
pm.variables.set("tempToken", "abc123"); // Chỉ dùng trong request này
```

## 2. Setting Variables

### Set Syntax

```javascript
// Global
pm.globals.set("variableName", "value");

// Collection
pm.collectionVariables.set("variableName", "value");

// Environment
pm.environment.set("variableName", "value");

// Local (temporary)
pm.variables.set("variableName", "value");
```

### Set Multiple Types

```javascript
// String
pm.environment.set("userName", "John Doe");

// Number
pm.environment.set("userId", 12345);

// Boolean
pm.environment.set("isActive", true);

// Object (must stringify)
const user = {id: 1, name: "John"};
pm.environment.set("user", JSON.stringify(user));

// Array (must stringify)
const items = [1, 2, 3];
pm.environment.set("items", JSON.stringify(items));
```

## 3. Getting Variables

### Get Syntax

```javascript
// Get from specific scope
const globalVar = pm.globals.get("variableName");
const collectionVar = pm.collectionVariables.get("variableName");
const envVar = pm.environment.get("variableName");

// Get from any scope (follows priority)
const anyVar = pm.variables.get("variableName");
```

### Get với Default Value

```javascript
const userId = pm.environment.get("userId") || 0;
const userName = pm.environment.get("userName") || "Guest";
```

### Get Complex Types

```javascript
// Get object
const userStr = pm.environment.get("user");
const user = JSON.parse(userStr);
console.log(user.name);

// Get array
const itemsStr = pm.environment.get("items");
const items = JSON.parse(itemsStr);
console.log(items.length);
```

## 4. Variable Resolution

Khi có cùng variable name ở nhiều scopes:

```javascript
// Setup
pm.globals.set("apiUrl", "https://global.com");
pm.collectionVariables.set("apiUrl", "https://collection.com");
pm.environment.set("apiUrl", "https://env.com");
pm.variables.set("apiUrl", "https://local.com");

// Get
const url = pm.variables.get("apiUrl");
console.log(url); // → "https://local.com" (highest priority)
```

## 5. Checking Variable Existence

```javascript
// Check if variable exists
const hasToken = pm.environment.has("accessToken");

if (hasToken) {
    console.log("Token exists");
} else {
    console.log("No token found");
}

// Or check value
const token = pm.environment.get("accessToken");
if (token) {
    console.log("Token:", token);
} else {
    console.error("Missing token!");
}
```

## 6. Removing Variables

### Unset Single Variable

```javascript
pm.environment.unset("oldToken");
pm.globals.unset("tempData");
pm.collectionVariables.unset("counter");
```

### Clear All Variables

```javascript
// Clear all environment variables
pm.environment.clear();

// Clear all global variables
pm.globals.clear();

// Clear all collection variables
pm.collectionVariables.clear();
```

## 7. Viewing All Variables

```javascript
// Get all as object
const envVars = pm.environment.toObject();
console.log("Environment vars:", envVars);

const globalVars = pm.globals.toObject();
console.log("Global vars:", globalVars);

// List all variable names
Object.keys(envVars).forEach(key => {
    console.log(key, "=", pm.environment.get(key));
});
```

> **📸 HÌNH ẢNH:** Environment Quick Look
> - File: `environment-quick-look.png`
> - Nội dung: Screenshot của eye icon (👁️) được click, showing environment variables panel với list of variables và values

<!-- IMAGE_PLACEHOLDER: environment-quick-look.png -->

## 8. Request Chaining

### Example 1: Login → Get Profile

**Request 1: Login**
```javascript
// Tests tab
const response = pm.response.json();

pm.test("Login successful", () => {
    pm.response.to.have.status(200);
    pm.expect(response.token).to.exist;
});

// Save token và user ID
pm.environment.set("accessToken", response.token);
pm.environment.set("userId", response.user.id);

console.log("✅ Logged in as user:", response.user.id);
```

**Request 2: Get Profile**
```
URL: {{baseUrl}}/users/{{userId}}
Headers:
  Authorization: Bearer {{accessToken}}
```

**Tests:**
```javascript
pm.test("Got user profile", () => {
    pm.response.to.have.status(200);
});

const profile = pm.response.json();
console.log("Profile:", profile.name);
```

### Example 2: Create → Update → Delete

**Request 1: Create User**
```javascript
// Tests
const response = pm.response.json();
pm.environment.set("createdUserId", response.id);
console.log("Created user ID:", response.id);
```

**Request 2: Update User**
```
URL: {{baseUrl}}/users/{{createdUserId}}
Method: PUT
```

**Request 3: Delete User**
```
URL: {{baseUrl}}/users/{{createdUserId}}
Method: DELETE
```

**Request 4: Verify Deleted**
```
URL: {{baseUrl}}/users/{{createdUserId}}
Method: GET
```

```javascript
// Tests
pm.test("User no longer exists", () => {
    pm.response.to.have.status(404);
});

// Cleanup
pm.environment.unset("createdUserId");
```

### Example 3: Multi-step Workflow

> **📸 HÌNH ẢNH:** Request Chaining Flow
> - File: `request-chaining-workflow.png`
> - Nội dung: Flowchart showing: Register → Login (save token) → Create Post (use token) → Get Post (verify) → Delete Post, với variables được pass giữa các steps

<!-- IMAGE_PLACEHOLDER: request-chaining-workflow.png -->

**Workflow: Register → Login → Create Post → Comment**

1. **Register**
```javascript
const response = pm.response.json();
pm.environment.set("userId", response.id);
pm.environment.set("userEmail", response.email);
```

2. **Login**
```javascript
const response = pm.response.json();
pm.environment.set("authToken", response.token);
```

3. **Create Post**
```json
{
  "userId": {{userId}},
  "title": "My Post",
  "body": "Content"
}
```

```javascript
const response = pm.response.json();
pm.environment.set("postId", response.id);
```

4. **Add Comment**
```json
{
  "postId": {{postId}},
  "email": "{{userEmail}}",
  "body": "Great post!"
}
```

## 9. Data Passing Patterns

### Pattern 1: Extract IDs

```javascript
// From single resource
const response = pm.response.json();
pm.environment.set("resourceId", response.id);

// From array
const users = pm.response.json();
const firstUserId = users[0].id;
pm.environment.set("firstUserId", firstUserId);

// All IDs
const allIds = users.map(u => u.id);
pm.environment.set("userIds", JSON.stringify(allIds));
```

### Pattern 2: Extract Nested Data

```javascript
const response = pm.response.json();

// Nested object
pm.environment.set("city", response.address.city);
pm.environment.set("zipCode", response.address.zipcode);

// Nested array
const firstPost = response.posts[0];
pm.environment.set("latestPostId", firstPost.id);
```

### Pattern 3: Accumulate Data

```javascript
// Get existing array or create new
let collectedIds = pm.environment.get("collectedIds");
collectedIds = collectedIds ? JSON.parse(collectedIds) : [];

// Add new ID
const response = pm.response.json();
collectedIds.push(response.id);

// Save back
pm.environment.set("collectedIds", JSON.stringify(collectedIds));

console.log("Collected IDs:", collectedIds);
```

## 10. Advanced Variable Management

### Counter Pattern

```javascript
// Initialize or increment counter
let counter = pm.environment.get("requestCounter");
counter = counter ? parseInt(counter) + 1 : 1;
pm.environment.set("requestCounter", counter);

console.log("Request #" + counter);
```

### State Machine Pattern

```javascript
// Track workflow state
const currentState = pm.environment.get("workflowState") || "init";

switch(currentState) {
    case "init":
        pm.environment.set("workflowState", "authenticated");
        break;
    case "authenticated":
        pm.environment.set("workflowState", "data_loaded");
        break;
    case "data_loaded":
        pm.environment.set("workflowState", "completed");
        break;
}

console.log("Workflow state:", pm.environment.get("workflowState"));
```

### Conditional Variables

```javascript
// Different values based on environment
const env = pm.environment.name;

if (env === "Production") {
    pm.variables.set("maxRetries", 1);
    pm.variables.set("timeout", 5000);
} else {
    pm.variables.set("maxRetries", 3);
    pm.variables.set("timeout", 30000);
}
```

## 11. Variable Validation

### Pre-request Validation

```javascript
// Check required variables exist
const required = ["baseUrl", "apiKey", "userId"];

const missing = required.filter(name => !pm.environment.get(name));

if (missing.length > 0) {
    console.error("❌ Missing variables:", missing);
    throw new Error("Missing required variables: " + missing.join(", "));
}

console.log("✅ All required variables present");
```

### Type Validation

```javascript
const userId = pm.environment.get("userId");

// Check type
if (typeof userId !== 'number') {
    console.warn("Warning: userId should be a number");
    // Auto-convert
    pm.environment.set("userId", parseInt(userId));
}
```

## 12. Debug Variables

### Log All Variables

```javascript
console.log("=== Current Variables ===");

console.log("Environment:", pm.environment.name);
console.log("Env vars:", pm.environment.toObject());
console.log("Collection vars:", pm.collectionVariables.toObject());
console.log("Global vars:", pm.globals.toObject());
```

### Variable Snapshot

```javascript
// Take snapshot for debugging
const snapshot = {
    timestamp: new Date().toISOString(),
    environment: pm.environment.name,
    variables: {
        env: pm.environment.toObject(),
        collection: pm.collectionVariables.toObject(),
        global: pm.globals.toObject()
    }
};

console.log("Snapshot:", JSON.stringify(snapshot, null, 2));
```

## 13. Best Practices

### ✅ DO

- Use environment variables cho data thay đổi theo môi trường
- Use collection variables cho config chung
- Clear variables sau khi dùng xong
- Validate variables trước khi dùng
- Use descriptive variable names
- Document variable dependencies
- Stringify objects/arrays trước khi lưu

### ❌ DON'T

- Store sensitive data trong global variables
- Hardcode values instead of variables
- Forget to unset temporary variables
- Mix up variable scopes
- Store large data trong variables (performance)
- Use variables khi constants đơn giản hơn

## 14. Common Patterns

| Pattern | Use Case | Example |
|---------|----------|---------|
| **ID Passing** | Chain CRUD operations | Create → save ID → Update/Delete |
| **Token Management** | Auth workflows | Login → save token → Use in headers |
| **Pagination** | Loop through pages | Save nextPage → Use in next request |
| **Accumulation** | Collect data | Gather IDs from multiple requests |
| **State Tracking** | Multi-step workflows | Track current step |
| **Counters** | Iterations | Count requests, attempts |

## 15. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ 4 loại variables: Global, Collection, Environment, Local
- ✅ Variable scope hierarchy
- ✅ Set/get variables trong scripts
- ✅ Request chaining với variables
- ✅ Data passing patterns
- ✅ Validation và debugging
- ✅ Best practices

## Next Steps

- **Bài tiếp theo**: [6.4 Collection Runner](./collection-runner.md)

---

[⬅️ Pre-request Scripts](./pre-request-scripts.md) | [Tổng Quan Chương 6](./README.md) | [Tiếp Theo: Collection Runner ➡️](./collection-runner.md)
