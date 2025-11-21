# 2.5 JSON và XML

Dữ liệu trong API thường được format ở dạng JSON hoặc XML. JSON là format phổ biến nhất hiện nay.

## JSON (JavaScript Object Notation)

### JSON Là Gì?

**JSON** = **J**ava**S**cript **O**bject **N**otation

- Format dữ liệu text-based
- Dễ đọc cho cả người và máy
- Ngôn ngữ-independent (dùng được với mọi ngôn ngữ)
- Là standard cho APIs hiện đại

### JSON Syntax

**Cơ bản:**
```json
{
  "key": "value"
}
```

**Rules:**
- Keys phải trong dấu ngoặc kép `"key"`
- Values có thể là: string, number, boolean, array, object, null
- Sử dụng `:` giữa key và value
- Sử dụng `,` giữa các pairs
- `{ }` cho objects
- `[ ]` cho arrays

### JSON Data Types

#### 1. String (Chuỗi)
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "address": "123 Main St"
}
```

#### 2. Number (Số)
```json
{
  "age": 30,
  "price": 99.99,
  "quantity": 5,
  "rating": 4.5
}
```

#### 3. Boolean
```json
{
  "active": true,
  "verified": false,
  "isAdmin": true
}
```

#### 4. Null
```json
{
  "middleName": null,
  "deletedAt": null
}
```

#### 5. Array (Mảng)
```json
{
  "tags": ["javascript", "api", "tutorial"],
  "scores": [90, 85, 88, 92],
  "roles": ["user", "admin"]
}
```

#### 6. Object (Đối tượng)
```json
{
  "address": {
    "street": "123 Main St",
    "city": "New York",
    "country": "USA"
  }
}
```

### JSON Structure Examples

#### Simple Object
```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "age": 30
}
```

#### Nested Object
```json
{
  "id": 1,
  "name": "John Doe",
  "contact": {
    "email": "john@example.com",
    "phone": "+1-234-567-8900",
    "address": {
      "street": "123 Main St",
      "city": "New York",
      "zipCode": "10001"
    }
  }
}
```

#### Array of Objects
```json
{
  "users": [
    {
      "id": 1,
      "name": "John Doe",
      "email": "john@example.com"
    },
    {
      "id": 2,
      "name": "Jane Smith",
      "email": "jane@example.com"
    }
  ],
  "total": 2
}
```

#### Complex Example (E-commerce Order)
```json
{
  "orderId": "ORD-12345",
  "orderDate": "2024-01-21T10:00:00Z",
  "customer": {
    "id": 456,
    "name": "John Doe",
    "email": "john@example.com"
  },
  "items": [
    {
      "productId": 789,
      "name": "Laptop",
      "quantity": 1,
      "price": 999.99
    },
    {
      "productId": 790,
      "name": "Mouse",
      "quantity": 2,
      "price": 19.99
    }
  ],
  "subtotal": 1039.97,
  "tax": 103.99,
  "shipping": 10.00,
  "total": 1153.96,
  "status": "pending",
  "shippingAddress": {
    "street": "123 Main St",
    "city": "New York",
    "state": "NY",
    "zipCode": "10001",
    "country": "USA"
  }
}
```

### JSON Best Practices

#### 1. Naming Conventions

**camelCase** (Phổ biến nhất):
```json
{
  "firstName": "John",
  "lastName": "Doe",
  "phoneNumber": "+1-234-5678"
}
```

**snake_case** (Cũng phổ biến):
```json
{
  "first_name": "John",
  "last_name": "Doe",
  "phone_number": "+1-234-5678"
}
```

**Nhất quán trong toàn bộ API!**

#### 2. Meaningful Names
✅ **TỐT:**
```json
{
  "userId": 123,
  "createdAt": "2024-01-21T10:00:00Z",
  "isActive": true
}
```

❌ **TỆ:**
```json
{
  "uid": 123,
  "ts": "2024-01-21T10:00:00Z",
  "act": true
}
```

#### 3. Consistent Structure

✅ **TỐT:**
```json
{
  "data": [...],
  "meta": {
    "page": 1,
    "total": 100
  }
}
```

#### 4. Date/Time Format

Sử dụng ISO 8601:
```json
{
  "createdAt": "2024-01-21T10:00:00Z",
  "updatedAt": "2024-01-21T15:30:00+07:00"
}
```

### Working with JSON in Postman

#### Reading JSON Response

```javascript
// Parse JSON response
const response = pm.response.json();

// Access fields
const userId = response.id;
const userName = response.name;

// Access nested fields
const city = response.address.city;

// Access array
const firstTag = response.tags[0];

// Loop through array
response.users.forEach(user => {
    console.log(user.name);
});
```

#### Testing JSON

```javascript
// Test property exists
pm.test("Response has id", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('id');
});

// Test value
pm.test("Name is John", () => {
    const response = pm.response.json();
    pm.expect(response.name).to.equal("John");
});

// Test type
pm.test("Age is a number", () => {
    const response = pm.response.json();
    pm.expect(response.age).to.be.a('number');
});

// Test array
pm.test("Has 3 items", () => {
    const response = pm.response.json();
    pm.expect(response.items).to.have.lengthOf(3);
});
```

### Common JSON Errors

❌ **Missing comma:**
```json
{
  "name": "John"
  "age": 30    ← Missing comma after "John"
}
```

❌ **Trailing comma:**
```json
{
  "name": "John",
  "age": 30,   ← Trailing comma not allowed in JSON
}
```

❌ **Single quotes:**
```json
{
  'name': 'John'   ← Must use double quotes
}
```

❌ **Unquoted keys:**
```json
{
  name: "John"     ← Keys must be in quotes
}
```

✅ **CORRECT:**
```json
{
  "name": "John",
  "age": 30
}
```

---

## XML (eXtensible Markup Language)

### XML Là Gì?

- Markup language (giống HTML)
- Dùng tags `<tag></tag>`
- Self-descriptive
- Ít phổ biến hơn JSON trong APIs hiện đại

### XML Syntax

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root>
  <element>value</element>
</root>
```

### XML Example

**Simple:**
```xml
<?xml version="1.0"?>
<user>
  <id>123</id>
  <name>John Doe</name>
  <email>john@example.com</email>
  <age>30</age>
</user>
```

**Nested:**
```xml
<?xml version="1.0"?>
<user>
  <id>123</id>
  <name>John Doe</name>
  <address>
    <street>123 Main St</street>
    <city>New York</city>
    <country>USA</country>
  </address>
</user>
```

**Multiple Items:**
```xml
<?xml version="1.0"?>
<users>
  <user>
    <id>1</id>
    <name>John Doe</name>
  </user>
  <user>
    <id>2</id>
    <name>Jane Smith</name>
  </user>
</users>
```

### XML Attributes

```xml
<user id="123" active="true">
  <name>John Doe</name>
</user>
```

---

## JSON vs XML

| Feature | JSON | XML |
|---------|------|-----|
| **Syntax** | Đơn giản, gọn | Verbose, nhiều tags |
| **Đọc** | Dễ đọc hơn | Khó đọc hơn |
| **Size** | Nhỏ hơn | Lớn hơn |
| **Parse** | Nhanh | Chậm hơn |
| **Data Types** | String, Number, Boolean, Null, Array, Object | Tất cả là string |
| **Arrays** | Native support | Phải dùng repeated elements |
| **Comments** | Không support | Support `<!-- comment -->` |
| **Phổ biến** | ⭐⭐⭐⭐⭐ | ⭐⭐ |

### Example Comparison

**JSON:**
```json
{
  "users": [
    {"id": 1, "name": "John", "age": 30, "active": true},
    {"id": 2, "name": "Jane", "age": 25, "active": false}
  ]
}
```

**XML:**
```xml
<?xml version="1.0"?>
<users>
  <user>
    <id>1</id>
    <name>John</name>
    <age>30</age>
    <active>true</active>
  </user>
  <user>
    <id>2</id>
    <name>Jane</name>
    <age>25</age>
    <active>false</active>
  </user>
</users>
```

**Nhận xét:**
- JSON: 152 bytes
- XML: 246 bytes
- JSON ngắn hơn ~38%!

---

## Content-Type Headers

### JSON

```http
Content-Type: application/json

{
  "name": "John"
}
```

### XML

```http
Content-Type: application/xml

<?xml version="1.0"?>
<user>
  <name>John</name>
</user>
```

### Accept Header

Client chỉ định format muốn nhận:

```http
GET /users
Accept: application/json
```

hoặc

```http
GET /users
Accept: application/xml
```

---

## Practical Examples

### API Request/Response

**Request:**
```http
POST /api/users HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com",
  "age": 30
}
```

**Response:**
```http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "age": 30,
  "createdAt": "2024-01-21T10:00:00Z",
  "links": {
    "self": "/api/users/123"
  }
}
```

### Error Response

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format"
      },
      {
        "field": "age",
        "message": "Age must be between 18 and 100"
      }
    ]
  },
  "timestamp": "2024-01-21T10:00:00Z",
  "path": "/api/users"
}
```

---

## Tools

### JSON Validators

- **JSONLint** - https://jsonlint.com
- **JSON Formatter** - https://jsonformatter.org
- **Postman** - Built-in JSON validator

### JSON Viewers

**Postman:**
- Pretty view (formatted)
- Raw view
- Preview view

**Browser Extensions:**
- JSON Viewer (Chrome)
- JSONView (Firefox)

---

## Best Practices Summary

### JSON

✅ **DO:**
- Use consistent naming (camelCase or snake_case)
- Provide meaningful keys
- Use proper data types
- Include timestamps in ISO 8601 format
- Nest logically
- Version your API

❌ **DON'T:**
- Mix naming conventions
- Use abbreviations unnecessarily
- Put everything as strings
- Create overly nested structures (> 3-4 levels)

---

## Tổng Kết

- ✅ **JSON** là format phổ biến nhất cho APIs
- ✅ JSON đơn giản, dễ đọc, nhẹ
- ✅ XML verbose hơn, ít dùng trong APIs mới
- ✅ Biết cách đọc và parse JSON trong Postman
- ✅ Test JSON responses với assertions
- ✅ Sử dụng consistent naming conventions

## Tiếp Theo

Chúc mừng! Bạn đã hoàn thành Chương 2. Hãy chuyển sang [Chương 3: Postman Cơ Bản](../03-postman-co-ban/README.md)

---

[⬅️ Request & Response](./request-response.md) | [Chương 2](./README.md) | [Tiếp Theo: Chương 3 ➡️](../03-postman-co-ban/README.md)
