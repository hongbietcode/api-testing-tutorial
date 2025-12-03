# 7.1 Tổ Chức Collections

Tổ chức collections tốt giúp team dễ dàng maintain, scale, và collaborate hiệu quả.

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Cấu trúc folders khoa học
- ✅ Naming conventions chuẩn
- ✅ Grouping strategies
- ✅ Maintain large collections

## 1. Folder Structure Best Practices

### Good Structure

```
📁 Project API Tests
  📁 01-Authentication
    ↳ POST Register
    ↳ POST Login
    ↳ POST Refresh Token
    ↳ POST Logout
  📁 02-Users
    ↳ GET All Users
    ↳ GET User by ID
    ↳ POST Create User
    ↳ PUT Update User
    ↳ DELETE User
  📁 03-Products
    ↳ GET All Products
    ↳ GET Product by ID
    ↳ POST Create Product
    ↳ PUT Update Product
    ↳ DELETE Product
  📁 04-Orders
    ↳ CRUD operations...
  📁 99-Smoke Tests
    ↳ Health Check
    ↳ Login Test
    ↳ Critical Endpoints
```

> **📸 HÌNH ẢNH:** Well-organized Collection Structure
> - File: `collection-structure-good.png`
> - Nội dung: Screenshot Postman sidebar showing well-organized collection với numbered folders, clear hierarchy, descriptive names

<!-- IMAGE_PLACEHOLDER: collection-structure-good.png -->

### Bad Structure

```
📁 Tests
  ↳ request1
  ↳ request2
  ↳ test
  ↳ api call
  ↳ new request
  (50+ requests không organized)
```

> **📸 HÌNH ẢNH:** Poorly-organized Collection
> - File: `collection-structure-bad.png`
> - Nội dung: Screenshot showing messy collection với vague names, no folders, random order

<!-- IMAGE_PLACEHOLDER: collection-structure-bad.png -->

## 2. Naming Conventions

### Request Names

**✅ GOOD:**
```
GET All Users
POST Create New User
PUT Update User Profile
DELETE Remove User
PATCH Update User Status
```

**Format:** `{METHOD} {Action} {Resource}`

**❌ BAD:**
```
users
create
update thing
delete1
test request
```

### Folder Names

**✅ GOOD:**
```
01-Authentication
02-User Management
03-Product Catalog
04-Shopping Cart
99-Smoke Tests
```

**❌ BAD:**
```
stuff
tests
folder1
misc
```

## 3. Grouping Strategies

### Strategy 1: By Resource (Recommended)

```
📁 Users
📁 Products
📁 Orders
📁 Payments
```

**Pros:**
- ✅ Clear organization
- ✅ Easy to find endpoints
- ✅ Scalable

### Strategy 2: By Feature/Flow

```
📁 User Registration Flow
  ↳ Register
  ↳ Verify Email
  ↳ Complete Profile
📁 Shopping Flow
  ↳ Browse Products
  ↳ Add to Cart
  ↳ Checkout
  ↳ Payment
```

**Pros:**
- ✅ Test complete workflows
- ✅ Good for E2E tests

### Strategy 3: By HTTP Method

```
📁 GET Requests
📁 POST Requests
📁 PUT/PATCH Requests
📁 DELETE Requests
```

**Pros:**
- ✅ Simple
**Cons:**
- ❌ Hard to find specific resource

### Strategy 4: Hybrid

```
📁 01-Auth
📁 02-Users
  📁 CRUD Operations
  📁 User Workflows
📁 03-Products
📁 99-Tests
  📁 Smoke Tests
  📁 Regression Tests
```

## 4. Numbering Folders

**Benefits:**
- Control execution order
- Easy prioritization
- Visual hierarchy

**Convention:**
```
01-09: Setup/Auth
10-89: Features
90-98: Workflows
99: Smoke/Critical Tests
```

## 5. Collection Descriptions

Add comprehensive documentation:

```markdown
# E-commerce API Test Suite

## Overview
Complete test suite for E-commerce platform APIs.

## Base URL
- Dev: http://localhost:3000
- Staging: https://staging-api.example.com
- Production: https://api.example.com

## Authentication
All endpoints require Bearer token except Auth endpoints.

## Test Coverage
- Unit tests for all CRUD operations
- Integration tests for workflows
- Smoke tests for critical paths

## How to Run
1. Select environment (Dev/Staging/Prod)
2. Run "01-Auth" folder first to get token
3. Run other folders in sequence
4. Check "99-Smoke Tests" for quick validation

## Author
Team API Testing
Last Updated: 2024-01-15
```

## 6. Request Descriptions

```markdown
# GET User by ID

## Endpoint
GET /api/users/:id

## Description
Retrieves detailed information for a specific user.

## Path Parameters
- id (required): User ID (integer)

## Response 200
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "role": "admin"
}

## Error Responses
- 404: User not found
- 401: Unauthorized (no token)

## Tests
- Status is 200
- Response has user data
- User ID matches request
```

## 7. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ Cấu trúc folders khoa học
- ✅ Naming conventions rõ ràng
- ✅ Grouping strategies
- ✅ Documentation best practices

## Next Steps

- **Bài tiếp theo**: [7.2 Environments Management](./environments-management.md)

---

[⬅️ Tổng Quan Chương 7](./README.md) | [Tiếp Theo: Environments ➡️](./environments-management.md)
