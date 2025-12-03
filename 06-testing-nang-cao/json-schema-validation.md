# 6.7 JSON Schema Validation

JSON Schema Validation cho phép validate cấu trúc của API responses, ensuring data consistency và detecting breaking changes.

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Hiểu JSON Schema là gì
- ✅ Định nghĩa schemas
- ✅ Validate responses với schemas
- ✅ Test data types, required fields, formats
- ✅ Detect API contract violations

## 1. JSON Schema Là Gì?

**JSON Schema** là vocabulary để describe và validate JSON data structures.

### Tại Sao Cần Schema Validation?

**Testing values:**
```javascript
pm.expect(response.id).to.equal(1);  // ❌ Brittle
pm.expect(response.name).to.equal("John");  // ❌ Specific
```

**Testing structure:**
```javascript
pm.response.to.have.jsonSchema(schema);  // ✅ Robust
```

### Benefits

- ✅ Validate data structure thay vì values
- ✅ Detect breaking changes
- ✅ Contract testing
- ✅ Ensure API consistency
- ✅ Better error messages

> **📸 HÌNH ẢNH:** Schema Validation Concept
> - File: `schema-validation-concept.png`
> - Nội dung: Diagram showing: API Response → Schema Validator → Pass/Fail (with expected structure vs actual structure comparison)

<!-- IMAGE_PLACEHOLDER: schema-validation-concept.png -->

## 2. Basic Schema Structure

### Simple Schema

```json
{
  "type": "object",
  "properties": {
    "id": {
      "type": "number"
    },
    "name": {
      "type": "string"
    }
  },
  "required": ["id", "name"]
}
```

**Validates:**
```json
{
  "id": 1,
  "name": "John Doe"
}
```

**Rejects:**
```json
{
  "id": "not-a-number",  // ❌ Wrong type
  "name": "John"
}

{
  "name": "John"  // ❌ Missing 'id'
}
```

## 3. JSON Schema Types

### Primitive Types

```javascript
// String
{
  "type": "string"
}

// Number
{
  "type": "number"
}

// Integer
{
  "type": "integer"
}

// Boolean
{
  "type": "boolean"
}

// Null
{
  "type": "null"
}
```

### Complex Types

```javascript
// Object
{
  "type": "object",
  "properties": {
    "key": { "type": "string" }
  }
}

// Array
{
  "type": "array",
  "items": {
    "type": "string"
  }
}
```

## 4. Schema Validation trong Postman

### Setup

Postman uses **Tiny Validator (tv4)** library.

### Basic Validation

```javascript
// Define schema
const schema = {
    type: "object",
    properties: {
        id: { type: "number" },
        name: { type: "string" },
        email: { type: "string" }
    },
    required: ["id", "name", "email"]
};

// Validate response
pm.test("Schema is valid", function() {
    pm.response.to.have.jsonSchema(schema);
});
```

### Complete Example

```javascript
// User schema
const userSchema = {
    type: "object",
    properties: {
        id: {
            type: "integer"
        },
        name: {
            type: "string",
            minLength: 1
        },
        email: {
            type: "string",
            format: "email"
        },
        age: {
            type: "integer",
            minimum: 0,
            maximum: 150
        },
        isActive: {
            type: "boolean"
        }
    },
    required: ["id", "name", "email"]
};

// Test
pm.test("Response matches user schema", function() {
    const response = pm.response.json();
    pm.expect(response).to.have.jsonSchema(userSchema);
});
```

## 5. Required Fields

### Define Required Fields

```javascript
const schema = {
    type: "object",
    properties: {
        id: { type: "number" },
        name: { type: "string" },
        email: { type: "string" },
        phone: { type: "string" }  // Optional
    },
    required: ["id", "name", "email"]  // id, name, email bắt buộc
};
```

**Valid:**
```json
{ "id": 1, "name": "John", "email": "john@test.com" }
{ "id": 1, "name": "John", "email": "john@test.com", "phone": "123" }
```

**Invalid:**
```json
{ "id": 1, "name": "John" }  // ❌ Missing email
```

## 6. String Formats

### Format Validation

```javascript
{
    type: "string",
    format: "email"  // Validates email format
}

{
    type: "string",
    format: "uri"    // Validates URL
}

{
    type: "string",
    format: "date-time"  // ISO 8601 datetime
}

{
    type: "string",
    format: "ipv4"   // IP address
}
```

### String Constraints

```javascript
{
    type: "string",
    minLength: 3,
    maxLength: 50
}

{
    type: "string",
    pattern: "^[A-Z][a-z]+$"  // Regex: Capital first letter
}
```

## 7. Number Constraints

```javascript
{
    type: "number",
    minimum: 0,
    maximum: 100
}

{
    type: "integer",
    multipleOf: 5  // Must be divisible by 5
}

{
    type: "number",
    exclusiveMinimum: 0,  // > 0 (not >=)
    exclusiveMaximum: 100 // < 100 (not <=)
}
```

## 8. Array Validation

### Array of Strings

```javascript
{
    type: "array",
    items: {
        type: "string"
    },
    minItems: 1,
    maxItems: 10
}
```

**Valid:**
```json
["apple", "banana", "orange"]
```

### Array of Objects

```javascript
{
    type: "array",
    items: {
        type: "object",
        properties: {
            id: { type: "number" },
            name: { type: "string" }
        },
        required: ["id", "name"]
    }
}
```

**Valid:**
```json
[
    { "id": 1, "name": "John" },
    { "id": 2, "name": "Jane" }
]
```

### Unique Items

```javascript
{
    type: "array",
    items: { type: "number" },
    uniqueItems: true  // No duplicates
}
```

## 9. Nested Objects

```javascript
const schema = {
    type: "object",
    properties: {
        id: { type: "number" },
        name: { type: "string" },
        address: {
            type: "object",
            properties: {
                street: { type: "string" },
                city: { type: "string" },
                zipcode: { type: "string" },
                geo: {
                    type: "object",
                    properties: {
                        lat: { type: "string" },
                        lng: { type: "string" }
                    },
                    required: ["lat", "lng"]
                }
            },
            required: ["street", "city"]
        }
    },
    required: ["id", "name", "address"]
};
```

**Validates:**
```json
{
    "id": 1,
    "name": "John",
    "address": {
        "street": "Main St",
        "city": "NYC",
        "zipcode": "10001",
        "geo": {
            "lat": "40.7128",
            "lng": "-74.0060"
        }
    }
}
```

## 10. Enum Values

Restrict to specific values:

```javascript
{
    type: "string",
    enum: ["active", "inactive", "pending"]
}

{
    type: "number",
    enum: [1, 2, 3, 5, 8, 13]  // Fibonacci
}
```

**Valid:**
```json
"active"
"pending"
```

**Invalid:**
```json
"deleted"  // ❌ Not in enum
```

## 11. AllOf, AnyOf, OneOf

### AllOf (AND)

Must match ALL schemas:

```javascript
{
    allOf: [
        { type: "number" },
        { minimum: 10 },
        { maximum: 100 }
    ]
}
// Must be number AND >= 10 AND <= 100
```

### AnyOf (OR)

Must match AT LEAST ONE:

```javascript
{
    anyOf: [
        { type: "string" },
        { type: "number" }
    ]
}
// Can be string OR number
```

### OneOf (XOR)

Must match EXACTLY ONE:

```javascript
{
    oneOf: [
        { type: "string", maxLength: 5 },
        { type: "number", minimum: 0 }
    ]
}
// Either short string OR positive number (not both)
```

## 12. Complete Example: User API

```javascript
const userSchema = {
    type: "object",
    properties: {
        id: {
            type: "integer",
            minimum: 1
        },
        username: {
            type: "string",
            minLength: 3,
            maxLength: 20,
            pattern: "^[a-zA-Z0-9_]+$"
        },
        email: {
            type: "string",
            format: "email"
        },
        profile: {
            type: "object",
            properties: {
                firstName: { type: "string" },
                lastName: { type: "string" },
                age: {
                    type: "integer",
                    minimum: 13,
                    maximum: 120
                },
                avatar: {
                    type: "string",
                    format: "uri"
                }
            },
            required: ["firstName", "lastName"]
        },
        roles: {
            type: "array",
            items: {
                type: "string",
                enum: ["admin", "user", "moderator"]
            },
            minItems: 1,
            uniqueItems: true
        },
        status: {
            type: "string",
            enum: ["active", "inactive", "suspended"]
        },
        createdAt: {
            type: "string",
            format: "date-time"
        }
    },
    required: ["id", "username", "email", "profile", "roles", "status"]
};

pm.test("User response matches schema", function() {
    pm.response.to.have.jsonSchema(userSchema);
});
```

## 13. Array Schema Example

```javascript
// Schema for array of users
const usersSchema = {
    type: "array",
    items: {
        type: "object",
        properties: {
            id: { type: "number" },
            name: { type: "string" },
            email: {
                type: "string",
                format: "email"
            }
        },
        required: ["id", "name", "email"]
    },
    minItems: 1
};

pm.test("Users array matches schema", function() {
    const response = pm.response.json();
    pm.expect(response).to.have.jsonSchema(usersSchema);
});
```

## 14. Reusable Schemas

### Define Schema Globally

```javascript
// Collection Pre-request Script
pm.globals.set("userSchema", JSON.stringify({
    type: "object",
    properties: {
        id: { type: "number" },
        name: { type: "string" }
    },
    required: ["id", "name"]
}));
```

### Use in Tests

```javascript
// Tests tab
const userSchema = JSON.parse(pm.globals.get("userSchema"));

pm.test("Schema is valid", function() {
    pm.response.to.have.jsonSchema(userSchema);
});
```

## 15. Error Messages

> **📸 HÌNH ẢNH:** Schema Validation Error
> - File: `schema-validation-error.png`
> - Nội dung: Test results showing failed schema validation với detailed error message indicating which field failed và why

<!-- IMAGE_PLACEHOLDER: schema-validation-error.png -->

Schema validation errors are descriptive:

```
AssertionError: expected response to have JSON schema

Schema validation failed:
  - Property 'email' is required
  - Property 'age' type should be integer, found string
  - Property 'status' must be one of: active, inactive, suspended
```

## 16. Best Practices

### ✅ DO

- Validate structure, not values
- Use schema cho contract testing
- Define required fields
- Use appropriate data types
- Add format validation (email, URL, etc.)
- Store reusable schemas globally
- Version schemas khi API changes

### ❌ DON'T

- Over-specify schemas (too strict)
- Validate values instead of types
- Forget optional fields
- Ignore schema errors
- Hardcode schemas in every test

## 17. Use Cases

### Use Case 1: API Contract Testing

Ensure API doesn't break contracts:

```javascript
pm.test("API contract maintained", function() {
    pm.response.to.have.jsonSchema(contractSchema);
});
```

### Use Case 2: Backward Compatibility

```javascript
// Old schema (v1)
const v1Schema = { /* ... */ };

// New response should still match old schema
pm.test("Backward compatible with v1", function() {
    pm.response.to.have.jsonSchema(v1Schema);
});
```

### Use Case 3: Multi-environment Testing

```javascript
// Same schema across Dev, Staging, Prod
pm.test("Response structure consistent", function() {
    pm.response.to.have.jsonSchema(standardSchema);
});
```

## 18. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ JSON Schema validates data structure
- ✅ Define schemas với types, required fields, formats
- ✅ Validate responses trong Postman
- ✅ Number, string, array, object constraints
- ✅ Nested objects và complex schemas
- ✅ Reusable schemas
- ✅ Contract testing use cases
- ✅ Best practices

## Tài Nguyên

- [JSON Schema Spec](https://json-schema.org/)
- [Understanding JSON Schema](https://json-schema.org/understanding-json-schema/)
- [Postman Schema Validation](https://learning.postman.com/docs/writing-scripts/script-references/test-examples/#validating-response-structure)

---

**🎉 Chúc mừng! Bạn đã hoàn thành Chương 6: Testing Nâng Cao!**

---

[⬅️ Newman CLI](./newman-cli.md) | [Tổng Quan Chương 6](./README.md) | [Tiếp Theo: Chương 7 ➡️](../07-best-practices/README.md)
