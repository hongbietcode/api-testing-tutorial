# 7.4 Error Handling

Error handling tốt giúp API tests robust, maintainable, và catch edge cases hiệu quả.

## Mục Tiêu

- ✅ Negative testing
- ✅ Error response validation
- ✅ Edge cases coverage
- ✅ Graceful failure handling

## 1. Negative Testing

### Tại Sao Cần Negative Testing?

**Positive Testing:**
```javascript
pm.test("Valid user creation returns 201", () => {
    pm.response.to.have.status(201);
});
```

**Negative Testing:**
```javascript
pm.test("Missing email returns 400", () => {
    pm.response.to.have.status(400);
});

pm.test("Invalid email format returns 400", () => {
    pm.response.to.have.status(400);
});

pm.test("Duplicate email returns 409", () => {
    pm.response.to.have.status(409);
});
```

### Test Both Paths

- ✅ Valid inputs (Happy path)
- ✅ Invalid inputs (Sad path)
- ✅ Edge cases
- ✅ Boundary conditions

> **📸 HÌNH ẢNH:** Positive vs Negative Testing
> - File: `positive-negative-testing.png`
> - Nội dung: Diagram showing Happy Path (green arrows, 200/201 responses) vs Sad Path (red arrows, 400/404/500 responses)

<!-- IMAGE_PLACEHOLDER: positive-negative-testing.png -->

## 2. Common Error Scenarios

### 400 Bad Request

**Test invalid input:**
```javascript
pm.test("Missing required field returns 400", () => {
    pm.response.to.have.status(400);
    const response = pm.response.json();
    pm.expect(response).to.have.property('error');
    pm.expect(response.error).to.include('required');
});
```

**Test cases:**
- Missing required fields
- Invalid data types
- Malformed JSON
- Invalid field formats (email, phone, etc.)

### 401 Unauthorized

**Test missing authentication:**
```javascript
pm.test("No token returns 401", () => {
    pm.response.to.have.status(401);
});

pm.test("Invalid token returns 401", () => {
    pm.response.to.have.status(401);
    const response = pm.response.json();
    pm.expect(response.error).to.include('Invalid token');
});

pm.test("Expired token returns 401", () => {
    pm.response.to.have.status(401);
    const response = pm.response.json();
    pm.expect(response.error).to.include('expired');
});
```

### 403 Forbidden

**Test authorization:**
```javascript
pm.test("User cannot access admin endpoint", () => {
    pm.response.to.have.status(403);
    const response = pm.response.json();
    pm.expect(response.error).to.include('Forbidden');
});
```

### 404 Not Found

**Test non-existent resources:**
```javascript
pm.test("Non-existent user returns 404", () => {
    pm.response.to.have.status(404);
});

pm.test("Invalid ID format returns 404", () => {
    pm.response.to.have.status(404);
});
```

### 409 Conflict

**Test duplicate resources:**
```javascript
pm.test("Duplicate email returns 409", () => {
    pm.response.to.have.status(409);
    const response = pm.response.json();
    pm.expect(response.error).to.include('already exists');
});
```

### 500 Internal Server Error

**Verify error handling:**
```javascript
pm.test("Server error returns 500", () => {
    pm.response.to.have.status(500);
});

pm.test("Error response has proper structure", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('error');
    pm.expect(response).to.have.property('message');
});
```

## 3. Error Response Validation

### Standard Error Format

**Expected structure:**
```json
{
  "error": "ValidationError",
  "message": "Email is required",
  "statusCode": 400,
  "timestamp": "2024-01-15T10:30:00Z"
}
```

**Validate structure:**
```javascript
pm.test("Error response has correct structure", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('error');
    pm.expect(response).to.have.property('message');
    pm.expect(response).to.have.property('statusCode');
    pm.expect(response.statusCode).to.equal(400);
});
```

### Validate Error Messages

```javascript
pm.test("Error message is descriptive", () => {
    const response = pm.response.json();
    pm.expect(response.message).to.be.a('string');
    pm.expect(response.message.length).to.be.above(0);
    // Message should explain what went wrong
    pm.expect(response.message).to.match(/email|required|invalid/i);
});
```

> **📸 HÌNH ẢNH:** Error Response Structure
> - File: `error-response-structure.png`
> - Nội dung: JSON example showing standard error format với highlighted fields (error, message, statusCode, timestamp)

<!-- IMAGE_PLACEHOLDER: error-response-structure.png -->

## 4. Edge Cases

### Boundary Testing

```javascript
// Test minimum length
pm.test("Username too short returns 400", () => {
    // Username = "ab" (min 3 characters)
    pm.response.to.have.status(400);
});

// Test maximum length
pm.test("Username too long returns 400", () => {
    // Username = "a".repeat(51) (max 50 characters)
    pm.response.to.have.status(400);
});

// Test exact boundaries
pm.test("Username at min length accepted", () => {
    // Username = "abc" (exactly 3)
    pm.response.to.have.status(201);
});
```

### Special Characters

```javascript
pm.test("Special characters in name handled", () => {
    // Name = "John O'Brien"
    pm.response.to.have.status(201);
});

pm.test("Unicode characters supported", () => {
    // Name = "Nguyễn Văn A"
    pm.response.to.have.status(201);
});

pm.test("SQL injection attempt rejected", () => {
    // Input = "'; DROP TABLE users; --"
    pm.response.to.have.status(400);
});
```

### Null and Empty Values

```javascript
pm.test("Null value rejected", () => {
    // field = null
    pm.response.to.have.status(400);
});

pm.test("Empty string rejected", () => {
    // field = ""
    pm.response.to.have.status(400);
});

pm.test("Whitespace-only rejected", () => {
    // field = "   "
    pm.response.to.have.status(400);
});
```

## 5. Try-Catch Error Handling

### Safe JSON Parsing

**❌ BAD:**
```javascript
pm.test("Has user data", () => {
    const response = pm.response.json(); // Might crash!
    pm.expect(response.id).to.exist;
});
```

**✅ GOOD:**
```javascript
pm.test("Response is valid JSON", () => {
    try {
        const response = pm.response.json();
        pm.expect(response).to.be.an('object');
    } catch (e) {
        pm.expect.fail("Response is not valid JSON");
    }
});
```

### Safe Property Access

```javascript
pm.test("User has address", () => {
    try {
        const response = pm.response.json();
        pm.expect(response.user).to.exist;
        pm.expect(response.user.address).to.exist;
        pm.expect(response.user.address.city).to.be.a('string');
    } catch (e) {
        pm.expect.fail(`Address validation failed: ${e.message}`);
    }
});
```

### Helper Function

**Collection Pre-request:**
```javascript
pm.globals.set("safeGetJson", `
    function() {
        try {
            return pm.response.json();
        } catch (e) {
            console.error("JSON parse error:", e);
            return null;
        }
    }
`);
```

**Use in tests:**
```javascript
const response = eval(pm.globals.get("safeGetJson"))();
if (response) {
    pm.expect(response.id).to.exist;
} else {
    pm.expect.fail("Invalid JSON response");
}
```

## 6. Timeout Handling

### Test Request Timeout

```javascript
pm.test("Response time is acceptable", () => {
    pm.expect(pm.response.responseTime).to.be.below(5000);
});

pm.test("Fast endpoint responds quickly", () => {
    pm.expect(pm.response.responseTime).to.be.below(500);
});
```

### Handle Slow Responses

**Pre-request Script:**
```javascript
// Set timeout for this request
pm.request.timeout = 30000; // 30 seconds
```

**Test:**
```javascript
pm.test("Slow operation completes within timeout", () => {
    pm.response.to.have.status(200);
    pm.expect(pm.response.responseTime).to.be.below(30000);
});
```

## 7. Multiple Assertions

### Order Matters

**❌ BAD:**
```javascript
pm.test("Everything works", () => {
    const response = pm.response.json();
    pm.expect(response.user.email).to.include('@'); // Crashes if user doesn't exist!
    pm.response.to.have.status(200);
});
```

**✅ GOOD:**
```javascript
pm.test("Status is 200", () => {
    pm.response.to.have.status(200);
});

pm.test("Response has user", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('user');
});

pm.test("User has valid email", () => {
    const response = pm.response.json();
    pm.expect(response.user.email).to.include('@');
});
```

## 8. Error Recovery

### Continue on Error

```javascript
// Pre-request Script
pm.globals.set("testPassed", true);

// Tests
pm.test("Critical test", () => {
    try {
        pm.response.to.have.status(200);
    } catch (e) {
        pm.globals.set("testPassed", false);
        console.error("Critical test failed:", e);
        throw e; // Re-throw to mark test as failed
    }
});

// Next request Pre-request
if (pm.globals.get("testPassed") === "false") {
    console.warn("Previous test failed, skipping this request");
    // Optionally skip this request
}
```

### Cleanup on Failure

```javascript
// Tests tab
pm.test("Create and validate user", () => {
    pm.response.to.have.status(201);
    const response = pm.response.json();
    pm.environment.set("createdUserId", response.id);
});

// If test fails, still cleanup
if (pm.response.code !== 201) {
    console.warn("User creation failed, cleanup not needed");
    pm.environment.unset("createdUserId");
}
```

## 9. Best Practices

### ✅ DO

- Test both success and failure cases
- Validate error response structure
- Test edge cases and boundaries
- Use try-catch for safe parsing
- Order assertions logically
- Provide descriptive error messages
- Test timeout scenarios
- Validate HTTP status codes
- Check error message clarity

### ❌ DON'T

- Only test happy paths
- Ignore error responses
- Skip boundary testing
- Crash tests with unsafe code
- Test everything in one assertion
- Use generic error messages
- Ignore slow responses
- Assume JSON is always valid

## 10. Complete Example

```javascript
// Folder: User Creation - Error Cases

// Request 1: Missing Required Field
pm.test("Status is 400", () => {
    pm.response.to.have.status(400);
});

pm.test("Error response has correct structure", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('error');
    pm.expect(response).to.have.property('message');
});

pm.test("Error message mentions missing field", () => {
    const response = pm.response.json();
    pm.expect(response.message).to.include('email');
    pm.expect(response.message).to.include('required');
});

// Request 2: Invalid Email Format
pm.test("Invalid email returns 400", () => {
    pm.response.to.have.status(400);
});

pm.test("Error explains email is invalid", () => {
    const response = pm.response.json();
    pm.expect(response.message).to.match(/email.*invalid/i);
});

// Request 3: Duplicate Email
pm.test("Duplicate email returns 409", () => {
    pm.response.to.have.status(409);
});

pm.test("Error indicates conflict", () => {
    const response = pm.response.json();
    pm.expect(response.error).to.equal('Conflict');
    pm.expect(response.message).to.include('already exists');
});

// Request 4: Unauthorized Access
pm.test("No token returns 401", () => {
    pm.response.to.have.status(401);
});

pm.test("Unauthorized error returned", () => {
    const response = pm.response.json();
    pm.expect(response.error).to.equal('Unauthorized');
});
```

> **📸 HÌNH ẢNH:** Error Test Results
> - File: `error-test-results.png`
> - Nội dung: Test results panel showing multiple error scenarios tested (400, 401, 404, 409) với passed assertions

<!-- IMAGE_PLACEHOLDER: error-test-results.png -->

## Tổng Kết

- ✅ Negative testing catches bugs
- ✅ Validate error response structure
- ✅ Test edge cases and boundaries
- ✅ Use try-catch for safety
- ✅ Handle timeouts gracefully
- ✅ Provide clear error messages

---

[⬅️ Maintainable Tests](./writing-maintainable-tests.md) | [Tổng Quan Chương 7](./README.md) | [Tiếp Theo: Documentation ➡️](./documentation.md)
