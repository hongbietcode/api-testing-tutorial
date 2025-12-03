# Dự Án 2: User Management System

**Độ khó:** ⭐⭐ Trung bình  
**Thời gian ước tính:** 4-6 giờ  
**API:** ReqRes.in - https://reqres.in/api

## Mục Tiêu Học

Sau khi hoàn thành project này, bạn sẽ:
- ✅ Hiểu authentication flow
- ✅ Quản lý tokens
- ✅ Test user operations với pagination
- ✅ Chain complex workflows
- ✅ Data-driven testing
- ✅ Schema validation

> **📸 HÌNH ẢNH:** Project Structure
> - File: `user-management-structure.png`
> - Nội dung: Screenshot Postman collection structure với 4 folders: Authentication, User CRUD, Workflows, Advanced Tests

<!-- IMAGE_PLACEHOLDER: user-management-structure.png -->

## API Overview

**Base URL:** `https://reqres.in/api`

### Endpoints

| Method | Endpoint | Mô tả |
|--------|----------|-------|
| POST | `/register` | Đăng ký user mới |
| POST | `/login` | Đăng nhập |
| GET | `/users` | Danh sách users (paginated) |
| GET | `/users/{id}` | Chi tiết 1 user |
| POST | `/users` | Tạo user mới |
| PUT | `/users/{id}` | Cập nhật toàn bộ |
| PATCH | `/users/{id}` | Cập nhật một phần |
| DELETE | `/users/{id}` | Xóa user |

## Phase 1: Authentication (1-1.5 giờ)

### Task 1.1: User Registration

**Endpoint:** POST `/api/register`

**Request Body:**
```json
{
  "email": "eve.holt@reqres.in",
  "password": "pistol"
}
```

**Expected Response (200):**
```json
{
  "id": 4,
  "token": "QpwL5tke4Pnpja7X4"
}
```

**Tests cần viết:**
```javascript
pm.test("Registration successful", () => {
    pm.response.to.have.status(200);
});

pm.test("Response contains token", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('token');
    pm.expect(response.token).to.be.a('string');
    pm.expect(response.token.length).to.be.above(0);
});

pm.test("Response contains user ID", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('id');
    pm.expect(response.id).to.be.a('number');
});

// Save token for later use
pm.environment.set("authToken", pm.response.json().token);
pm.environment.set("userId", pm.response.json().id);
```

### Task 1.2: User Login

**Endpoint:** POST `/api/login`

**Request Body:**
```json
{
  "email": "eve.holt@reqres.in",
  "password": "cityslicka"
}
```

**Tests:**
```javascript
pm.test("Login successful", () => {
    pm.response.to.have.status(200);
});

pm.test("Token received", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('token');
    
    // Update environment token
    pm.environment.set("authToken", response.token);
});

pm.test("Response time acceptable", () => {
    pm.expect(pm.response.responseTime).to.be.below(1000);
});
```

> **📸 HÌNH ẢNH:** Login Flow
> - File: `login-flow-diagram.png`
> - Nội dung: Flow diagram: POST /register → Get Token → POST /login → Update Token → Use Token for subsequent requests

<!-- IMAGE_PLACEHOLDER: login-flow-diagram.png -->

### Task 1.3: Error Cases

**Test Case 1: Missing Email**
```json
POST /api/register
{
  "password": "pistol"
}
```

Expected: 400 Bad Request

**Test Case 2: Missing Password**
```json
POST /api/register
{
  "email": "test@test.com"
}
```

Expected: 400 Bad Request

**Tests:**
```javascript
pm.test("Missing email returns 400", () => {
    pm.response.to.have.status(400);
});

pm.test("Error message present", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('error');
    pm.expect(response.error).to.be.a('string');
});
```

## Phase 2: User CRUD Operations (2-2.5 giờ)

### Task 2.1: List Users với Pagination

**Endpoint:** GET `/api/users?page=1`

**Expected Response:**
```json
{
  "page": 1,
  "per_page": 6,
  "total": 12,
  "total_pages": 2,
  "data": [
    {
      "id": 1,
      "email": "george.bluth@reqres.in",
      "first_name": "George",
      "last_name": "Bluth",
      "avatar": "https://reqres.in/img/faces/1-image.jpg"
    }
  ],
  "support": {
    "url": "https://reqres.in/#support-heading",
    "text": "..."
  }
}
```

**Tests:**
```javascript
pm.test("Status code is 200", () => {
    pm.response.to.have.status(200);
});

pm.test("Pagination data present", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('page');
    pm.expect(response).to.have.property('per_page');
    pm.expect(response).to.have.property('total');
    pm.expect(response).to.have.property('total_pages');
});

pm.test("Data is array with correct length", () => {
    const response = pm.response.json();
    pm.expect(response.data).to.be.an('array');
    pm.expect(response.data.length).to.equal(response.per_page);
});

pm.test("Each user has required fields", () => {
    const users = pm.response.json().data;
    users.forEach(user => {
        pm.expect(user).to.have.property('id');
        pm.expect(user).to.have.property('email');
        pm.expect(user).to.have.property('first_name');
        pm.expect(user).to.have.property('last_name');
        pm.expect(user).to.have.property('avatar');
    });
});

// Save first user ID for later tests
const firstUser = pm.response.json().data[0];
pm.environment.set("testUserId", firstUser.id);
```

### Task 2.2: Get Single User

**Endpoint:** GET `/api/users/2`

**Tests:**
```javascript
pm.test("User found", () => {
    pm.response.to.have.status(200);
});

pm.test("User data structure correct", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('data');
    
    const user = response.data;
    pm.expect(user).to.have.property('id');
    pm.expect(user).to.have.property('email');
    pm.expect(user).to.have.property('first_name');
    pm.expect(user).to.have.property('last_name');
    pm.expect(user).to.have.property('avatar');
});

pm.test("User ID matches request", () => {
    const user = pm.response.json().data;
    const requestedId = pm.request.url.getPath().split('/').pop();
    pm.expect(user.id.toString()).to.equal(requestedId);
});

pm.test("Email format valid", () => {
    const user = pm.response.json().data;
    pm.expect(user.email).to.match(/^[^\s@]+@[^\s@]+\.[^\s@]+$/);
});
```

### Task 2.3: Create User

**Endpoint:** POST `/api/users`

**Request Body:**
```json
{
  "name": "John Doe",
  "job": "QA Engineer"
}
```

**Expected Response (201):**
```json
{
  "name": "John Doe",
  "job": "QA Engineer",
  "id": "123",
  "createdAt": "2024-01-15T10:30:00.000Z"
}
```

**Tests:**
```javascript
pm.test("User created successfully", () => {
    pm.response.to.have.status(201);
});

pm.test("Created user has ID", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('id');
    pm.expect(response.id).to.be.a('string');
});

pm.test("Created user has timestamp", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('createdAt');
    
    // Verify ISO 8601 format
    const timestamp = new Date(response.createdAt);
    pm.expect(timestamp.toString()).to.not.equal('Invalid Date');
});

pm.test("Request data echoed back", () => {
    const response = pm.response.json();
    const requestBody = JSON.parse(pm.request.body.raw);
    
    pm.expect(response.name).to.equal(requestBody.name);
    pm.expect(response.job).to.equal(requestBody.job);
});

// Save created user ID
pm.environment.set("createdUserId", pm.response.json().id);
```

### Task 2.4: Update User (PUT)

**Endpoint:** PUT `/api/users/2`

**Request Body:**
```json
{
  "name": "Jane Smith",
  "job": "Senior QA Engineer"
}
```

**Tests:**
```javascript
pm.test("User updated", () => {
    pm.response.to.have.status(200);
});

pm.test("Updated data returned", () => {
    const response = pm.response.json();
    pm.expect(response.name).to.equal("Jane Smith");
    pm.expect(response.job).to.equal("Senior QA Engineer");
});

pm.test("Update timestamp present", () => {
    const response = pm.response.json();
    pm.expect(response).to.have.property('updatedAt');
});
```

### Task 2.5: Partial Update (PATCH)

**Endpoint:** PATCH `/api/users/2`

**Request Body:**
```json
{
  "job": "Lead QA Engineer"
}
```

**Tests:**
```javascript
pm.test("Partial update successful", () => {
    pm.response.to.have.status(200);
});

pm.test("Only specified field updated", () => {
    const response = pm.response.json();
    pm.expect(response.job).to.equal("Lead QA Engineer");
    pm.expect(response).to.have.property('updatedAt');
});
```

### Task 2.6: Delete User

**Endpoint:** DELETE `/api/users/2`

**Tests:**
```javascript
pm.test("User deleted", () => {
    pm.response.to.have.status(204);  // No Content
});

pm.test("No response body", () => {
    pm.expect(pm.response.text()).to.be.empty;
});

// Cleanup environment variable
pm.environment.unset("createdUserId");
```

## Phase 3: Complex Workflows (2 giờ)

### Workflow 1: Complete User Lifecycle

**Flow:**
1. Register user
2. Login
3. Create profile
4. Update profile
5. Get profile
6. Delete profile

**Pre-request Script (Collection level):**
```javascript
// Initialize workflow state
if (!pm.environment.get("workflowState")) {
    pm.environment.set("workflowState", JSON.stringify({
        step: 1,
        userId: null,
        token: null
    }));
}
```

**Tests (example for each step):**
```javascript
// Step 1: Register
pm.test("Step 1: Registration complete", () => {
    pm.response.to.have.status(200);
    const state = JSON.parse(pm.environment.get("workflowState"));
    state.token = pm.response.json().token;
    state.step = 2;
    pm.environment.set("workflowState", JSON.stringify(state));
});

// Step 2: Login
pm.test("Step 2: Login successful", () => {
    pm.response.to.have.status(200);
    const state = JSON.parse(pm.environment.get("workflowState"));
    state.token = pm.response.json().token;
    state.step = 3;
    pm.environment.set("workflowState", JSON.stringify(state));
});

// ... continue for all steps
```

> **📸 HÌNH ẢNH:** User Lifecycle Workflow
> - File: `user-lifecycle-workflow.png`
> - Nội dung: Flowchart showing complete lifecycle: Register → Login → Create → Update → Get → Delete với arrows và status indicators

<!-- IMAGE_PLACEHOLDER: user-lifecycle-workflow.png -->

### Workflow 2: Pagination Loop

**Goal:** Loop through all pages and collect all users

**Pre-request Script:**
```javascript
const currentPage = pm.environment.get("currentPage") || 1;
pm.variables.set("page", currentPage);
```

**Tests:**
```javascript
const response = pm.response.json();
const allUsers = JSON.parse(pm.environment.get("allUsers") || "[]");

// Add current page users
allUsers.push(...response.data);
pm.environment.set("allUsers", JSON.stringify(allUsers));

// Check if more pages exist
const currentPage = parseInt(pm.environment.get("currentPage") || 1);
const totalPages = response.total_pages;

if (currentPage < totalPages) {
    // Move to next page
    pm.environment.set("currentPage", currentPage + 1);
    postman.setNextRequest(pm.info.requestName);  // Repeat this request
} else {
    // All pages collected
    pm.test(`Collected all ${allUsers.length} users from ${totalPages} pages`, () => {
        pm.expect(allUsers.length).to.equal(response.total);
    });
    
    // Cleanup
    pm.environment.unset("currentPage");
    pm.environment.unset("allUsers");
}
```

### Workflow 3: Bulk Operations

**Goal:** Create multiple users from data file

**Data file (users.csv):**
```csv
name,job
Alice Johnson,Developer
Bob Smith,Designer
Carol Davis,Manager
David Wilson,Analyst
Eve Brown,Tester
```

**Request:** POST `/api/users`

**Body:**
```json
{
  "name": "{{name}}",
  "job": "{{job}}"
}
```

**Tests:**
```javascript
pm.test("User created", () => {
    pm.response.to.have.status(201);
});

// Track created users
const createdUsers = JSON.parse(pm.globals.get("createdUsers") || "[]");
createdUsers.push({
    id: pm.response.json().id,
    name: pm.response.json().name,
    job: pm.response.json().job
});
pm.globals.set("createdUsers", JSON.stringify(createdUsers));

// On last iteration
if (pm.info.iteration === pm.info.iterationCount - 1) {
    console.log(`Created ${createdUsers.length} users`);
    pm.globals.unset("createdUsers");
}
```

## Phase 4: Advanced Features (1 giờ)

### Task 4.1: JSON Schema Validation

**Define schema:**
```javascript
const userSchema = {
    type: "object",
    required: ["id", "email", "first_name", "last_name", "avatar"],
    properties: {
        id: { type: "number" },
        email: { 
            type: "string", 
            format: "email" 
        },
        first_name: { 
            type: "string",
            minLength: 1
        },
        last_name: { 
            type: "string",
            minLength: 1
        },
        avatar: { 
            type: "string",
            format: "uri"
        }
    }
};

pm.test("User matches schema", () => {
    const user = pm.response.json().data;
    pm.expect(user).to.have.jsonSchema(userSchema);
});
```

### Task 4.2: Response Time Benchmarks

```javascript
// Collection Pre-request: Set SLAs
pm.globals.set("SLA_LIST", 500);
pm.globals.set("SLA_SINGLE", 300);
pm.globals.set("SLA_CREATE", 1000);

// Tests
const endpoint = pm.info.requestName;
let sla;

if (endpoint.includes("List")) {
    sla = pm.globals.get("SLA_LIST");
} else if (endpoint.includes("Get Single")) {
    sla = pm.globals.get("SLA_SINGLE");
} else if (endpoint.includes("Create")) {
    sla = pm.globals.get("SLA_CREATE");
}

pm.test(`Response time under ${sla}ms`, () => {
    pm.expect(pm.response.responseTime).to.be.below(sla);
});
```

### Task 4.3: Error Handling

**Test non-existent user:**
```javascript
// GET /api/users/999
pm.test("Non-existent user returns 404", () => {
    pm.response.to.have.status(404);
});

pm.test("Error response structure", () => {
    const response = pm.response.json();
    pm.expect(response).to.be.empty;  // ReqRes returns {}
});
```

### Task 4.4: Newman Automation

**Create Newman script:**
```bash
#!/bin/bash

echo "Running User Management Tests..."

newman run user-management.postman_collection.json \
  -e user-management-env.json \
  -d users.csv \
  -n 5 \
  --delay-request 100 \
  -r cli,htmlextra \
  --reporter-htmlextra-export reports/user-management-report.html

echo "Tests complete! Report: reports/user-management-report.html"
```

## Deliverables Checklist

### Collection Structure
- [ ] **01-Authentication** folder (3 requests)
  - POST Register
  - POST Login
  - POST Register (error cases)
- [ ] **02-User-Operations** folder (6 requests)
  - GET List Users
  - GET Single User
  - POST Create User
  - PUT Update User
  - PATCH Partial Update
  - DELETE User
- [ ] **03-Workflows** folder (3 workflows)
  - Complete Lifecycle
  - Pagination Loop
  - Bulk Operations
- [ ] **04-Advanced** folder (4 requests)
  - Schema Validation
  - Performance Tests
  - Error Cases
  - Edge Cases

### Tests
- [ ] 50+ test assertions
- [ ] All status codes validated
- [ ] Response structures verified
- [ ] Variables managed properly
- [ ] Error cases covered

### Automation
- [ ] Environment file created
- [ ] Data file for bulk operations
- [ ] Newman run script
- [ ] HTML report generated

### Documentation
- [ ] Collection description
- [ ] Request descriptions
- [ ] Workflow documentation
- [ ] Setup instructions

> **📸 HÌNH ẢNH:** Final Test Report
> - File: `user-management-test-report.png`
> - Nội dung: HTML test report showing all folders, test results, response times, với summary statistics (50+ tests passed)

<!-- IMAGE_PLACEHOLDER: user-management-test-report.png -->

## Tips & Best Practices

### 1. Token Management
```javascript
// Always check if token exists
const token = pm.environment.get("authToken");
if (!token) {
    console.warn("No auth token found. Run login first!");
}
```

### 2. Request Chaining
```javascript
// Save ID from create response
pm.environment.set("lastCreatedId", pm.response.json().id);

// Use in next request
// URL: /users/{{lastCreatedId}}
```

### 3. Cleanup
```javascript
// Collection Tests (runs after all requests)
pm.environment.unset("workflowState");
pm.environment.unset("createdUsers");
pm.globals.unset("SLA_LIST");
```

## Troubleshooting

**Issue:** Tests failing randomly
- **Solution:** Add delays between requests in Collection Runner

**Issue:** Pagination loop runs forever
- **Solution:** Check your `postman.setNextRequest()` logic has exit condition

**Issue:** Token not working
- **Solution:** ReqRes doesn't actually validate tokens; focus on testing the flow

## Next Steps

After completing this project:
1. ✅ Move to Project 3 (E-commerce API)
2. ✅ Share collection with team
3. ✅ Add to CI/CD pipeline
4. ✅ Create monitors for critical workflows

---

[⬅️ Project 1: TODO API](./README.md#dự-án-1-todo-list-api) | [Tổng Quan Chương 8](./README.md) | [Tiếp Theo: Project 3 ➡️](./du-an-3-ecommerce-api.md)
