# 7.3 Writing Maintainable Tests

Viết tests dễ maintain giúp team làm việc hiệu quả lâu dài.

## Mục Tiêu

- ✅ DRY principle
- ✅ Clear test names
- ✅ Helper functions
- ✅ Good vs bad examples

## 1. Clear Test Names

### ✅ GOOD
```javascript
pm.test("User registration returns 201 Created", () => {
    pm.response.to.have.status(201);
});

pm.test("Response contains user ID", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('id');
});
```

### ❌ BAD
```javascript
pm.test("Test 1", () => {
    pm.response.to.have.status(201);
});

pm.test("check", () => {
    pm.expect(pm.response.json().id).to.exist;
});
```

## 2. DRY Principle

### Create Helper Functions

**Collection Pre-request:**
```javascript
pm.globals.set("validateUser", `
    function(user) {
        pm.expect(user).to.have.property('id');
        pm.expect(user).to.have.property('email');
        pm.expect(user).to.have.property('name');
        pm.expect(user.id).to.be.a('number');
    }
`);
```

**Use in Tests:**
```javascript
const user = pm.response.json();
eval(pm.globals.get("validateUser"))(user);
```

> **📸 HÌNH ẢNH:** DRY vs Repetitive Code
> - File: `dry-principle-comparison.png`
> - Nội dung: Side-by-side code comparison: Left (repetitive validation code in multiple tests) vs Right (helper function used across tests)

<!-- IMAGE_PLACEHOLDER: dry-principle-comparison.png -->

## 3. Test One Thing

### ✅ GOOD
```javascript
pm.test("Status is 200", () => {
    pm.response.to.have.status(200);
});

pm.test("Has user data", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('name');
});

pm.test("Name is string", () => {
    const response = pm.response.json();
    pm.expect(response.name).to.be.a('string');
});
```

### ❌ BAD
```javascript
pm.test("Everything", () => {
    pm.response.to.have.status(200);
    pm.expect(pm.response.json().name).to.be.a('string');
    pm.expect(pm.response.json().email).to.include('@');
    // Too many assertions!
});
```

## Tổng Kết

- ✅ Descriptive test names
- ✅ DRY với helper functions
- ✅ Test one thing per test
- ✅ Maintainable long-term

---

[⬅️ Environments](./environments-management.md) | [Tổng Quan Chương 7](./README.md) | [Tiếp Theo: Error Handling ➡️](./error-handling.md)
