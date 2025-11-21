# Chương 8: Dự Án Thực Tế

Đây là phần quan trọng nhất! Áp dụng tất cả những gì đã học vào 3 dự án thực tế từ dễ đến khó.

## Tổng Quan

| Dự Án | Độ Khó | Thời Gian | Mô Tả |
|-------|--------|-----------|-------|
| **TODO List API** | ⭐ Dễ | 2-3 giờ | CRUD cơ bản, perfect cho người mới |
| **User Management** | ⭐⭐ Trung bình | 4-6 giờ | Authentication, authorization, workflows |
| **E-commerce API** | ⭐⭐⭐ Khó | 8-10 giờ | Complex workflows, multiple entities |

## Dự Án 1: TODO List API

### Mô Tả

Xây dựng test suite hoàn chỉnh cho một TODO list API.

### Mục Tiêu Học

- CRUD operations
- Filtering và search
- Status management
- Basic validation

### API Endpoints

**Base URL:** `https://jsonplaceholder.typicode.com`

```
GET    /todos          # Lấy tất cả todos
GET    /todos/{id}     # Lấy 1 todo
POST   /todos          # Tạo todo mới
PUT    /todos/{id}     # Update todo
PATCH  /todos/{id}     # Update một phần
DELETE /todos/{id}     # Xóa todo
```

### Yêu Cầu

#### Phase 1: Basic CRUD (30 phút)

**Tasks:**
1. Tạo collection "TODO API Tests"
2. Create environment với base_url
3. Test GET all todos
   - Verify status 200
   - Verify returns array
   - Verify has 200 todos
4. Test GET single todo
   - GET /todos/1
   - Verify structure: id, userId, title, completed
5. Test POST create todo
   - Create với data hợp lệ
   - Verify status 201
   - Verify response has id
6. Test PUT update todo
   - Update todo
   - Verify updated fields
7. Test DELETE todo
   - Delete todo
   - Verify status 200

#### Phase 2: Query Parameters (45 phút)

**Tasks:**
1. GET todos by userId
   - `/todos?userId=1`
   - Verify all todos have userId=1
2. GET completed todos
   - `/todos?completed=true`
   - Verify all have completed=true
3. GET incomplete todos
   - `/todos?completed=false`
4. Multiple filters
   - `/todos?userId=1&completed=false`

#### Phase 3: Validation Tests (45 phút)

**Negative Tests:**
1. GET todo không tồn tại (ID=999999)
   - Expect 404
2. POST với body rỗng
   - Expect 400/201 (JSONPlaceholder trả 201, nhưng real API sẽ 400)
3. POST với invalid data
   - completed = "yes" (should be boolean)
4. PUT với ID không tồn tại

#### Phase 4: Automation (45 phút)

1. Viết test scripts cho tất cả requests
2. Sử dụng variables để chain requests
3. Setup Collection Runner
4. Run toàn bộ collection
5. Export collection và environment

### Test Cases Mẫu

```javascript
// GET all todos
pm.test("Status code is 200", () => {
    pm.response.to.have.status(200);
});

pm.test("Response is an array", () => {
    const todos = pm.response.json();
    pm.expect(todos).to.be.an('array');
});

pm.test("First todo has correct structure", () => {
    const todo = pm.response.json()[0];
    pm.expect(todo).to.have.property('id');
    pm.expect(todo).to.have.property('userId');
    pm.expect(todo).to.have.property('title');
    pm.expect(todo).to.have.property('completed');
});
```

### Deliverables

- [ ] Collection với 15+ requests
- [ ] 30+ test cases
- [ ] Environment setup
- [ ] Documentation trong collection
- [ ] All tests passing

---

## Dự Án 2: User Management System

### Mô Tả

Test suite cho hệ thống quản lý users với authentication và authorization.

### Mục Tiêu Học

- Authentication flow
- Token management
- User roles
- Complex workflows
- Error handling

### API Endpoints

**Base URL:** `https://reqres.in/api`

```
POST   /register       # Đăng ký user
POST   /login          # Đăng nhập
GET    /users          # Danh sách users
GET    /users/{id}     # User detail
POST   /users          # Tạo user
PUT    /users/{id}     # Update user
PATCH  /users/{id}     # Partial update
DELETE /users/{id}     # Xóa user
```

### Yêu Cầu

#### Phase 1: Authentication (1 giờ)

**Tasks:**
1. Test Registration
   ```json
   POST /api/register
   {
     "email": "eve.holt@reqres.in",
     "password": "pistol"
   }
   ```
   - Verify status 200
   - Verify returns token
   - Save token to environment

2. Test Login
   ```json
   POST /api/login
   {
     "email": "eve.holt@reqres.in",
     "password": "cityslicka"
   }
   ```
   - Verify status 200
   - Save token
   - Use token for subsequent requests

3. Error Cases
   - Missing email → 400
   - Missing password → 400
   - Invalid credentials → 400

#### Phase 2: User Operations (2 giờ)

**Tasks:**
1. List Users với Pagination
   - GET /users?page=1
   - Verify pagination data
   - Loop through all pages

2. Get Single User
   - GET /users/2
   - Verify user data
   - Save user ID for later use

3. Create User
   ```json
   POST /users
   {
     "name": "John Doe",
     "job": "Tester"
   }
   ```
   - Verify status 201
   - Verify returns created user with ID
   - Save userID

4. Update User (PUT)
   - Update name and job
   - Verify all fields updated

5. Partial Update (PATCH)
   - Update only name
   - Verify only name changed

6. Delete User
   - DELETE user created earlier
   - Verify status 204

#### Phase 3: Complex Workflows (2 giờ)

**Workflow 1: Complete User Lifecycle**
1. Register new user
2. Login with credentials
3. Get user profile
4. Update profile
5. Delete user

**Workflow 2: List và Filter**
1. Get all users
2. Extract user IDs
3. Get details for each user
4. Aggregate data
5. Validate structure

**Workflow 3: Error Recovery**
1. Try operation with invalid token
2. Refresh token
3. Retry operation
4. Success

#### Phase 4: Advanced Features (1 giờ)

1. **Data-Driven Testing**
   - Create CSV with 10 users
   - Batch create users
   - Verify all created

2. **Response Time Testing**
   - All endpoints < 500ms
   - Critical endpoints < 200ms

3. **Schema Validation**
   - Define JSON schemas
   - Validate all responses

### Test Scripts Mẫu

```javascript
// Registration test
pm.test("Registration successful", () => {
    pm.response.to.have.status(200);

    const response = pm.response.json();
    pm.expect(response).to.have.property('token');

    // Save token
    pm.environment.set("authToken", response.token);
});

// List users with pagination
pm.test("Pagination works correctly", () => {
    const response = pm.response.json();

    pm.expect(response).to.have.property('page');
    pm.expect(response).to.have.property('per_page');
    pm.expect(response).to.have.property('total');
    pm.expect(response).to.have.property('total_pages');

    // Verify data array
    pm.expect(response.data).to.be.an('array');
    pm.expect(response.data.length).to.equal(response.per_page);
});

// Workflow: Create and verify user
pm.test("User created and can be retrieved", () => {
    // Save ID from create response
    const createResponse = pm.response.json();
    const userId = createResponse.id;
    pm.environment.set("createdUserId", userId);

    // Next request will GET this user ID
});
```

### Deliverables

- [ ] Collection với 25+ requests
- [ ] 50+ test cases
- [ ] Complete authentication flow
- [ ] 3 complex workflows
- [ ] Data-driven tests
- [ ] Newman CLI run

---

## Dự Án 3: E-commerce API (Advanced)

### Mô Tả

Test suite hoàn chỉnh cho E-commerce platform với products, cart, orders, payments.

### Mục Tiêu Học

- Multi-entity operations
- Complex business logic
- Transaction workflows
- State management
- Integration testing

### API Endpoints

**Sử dụng:** Fake Store API - `https://fakestoreapi.com`

```
# Authentication
POST   /auth/login

# Products
GET    /products
GET    /products/{id}
GET    /products/categories
GET    /products/category/{category}
POST   /products
PUT    /products/{id}
DELETE /products/{id}

# Cart
GET    /carts
GET    /carts/{id}
POST   /carts
PUT    /carts/{id}
DELETE /carts/{id}
GET    /carts/user/{userId}

# Users
GET    /users
GET    /users/{id}
POST   /users
PUT    /users/{id}
DELETE /users/{id}
```

### Yêu Cầu

#### Phase 1: Product Management (2 giờ)

**Tasks:**
1. Browse Products
   - GET all products
   - GET categories
   - Filter by category
   - Sort by price

2. Product Details
   - GET product by ID
   - Verify structure
   - Check image URLs

3. Search và Filter
   - By category
   - By price range (implement filtering logic)
   - Limit results

#### Phase 2: Shopping Cart (2 giờ)

**Tasks:**
1. Create Cart
   ```json
   POST /carts
   {
     "userId": 1,
     "date": "2024-01-01",
     "products": [
       {"productId": 1, "quantity": 2},
       {"productId": 2, "quantity": 1}
     ]
   }
   ```

2. Add to Cart
   - Add product
   - Update quantity
   - Verify cart total

3. Remove from Cart
   - Remove product
   - Update cart

4. Get User Cart
   - GET carts by user
   - Calculate total

#### Phase 3: Order Workflow (3 giờ)

**Complete E-commerce Flow:**

1. **User Registration/Login**
   - Register new user
   - Login
   - Save auth token

2. **Browse Products**
   - List products
   - Select 3 products
   - Save product IDs

3. **Add to Cart**
   - Create cart
   - Add products one by one
   - Verify cart contents
   - Calculate expected total

4. **Checkout Process**
   - Get cart summary
   - Validate products available
   - Calculate shipping
   - Calculate tax
   - Calculate final total

5. **Place Order**
   - Create order from cart
   - Verify order created
   - Clear cart

6. **Order Verification**
   - GET order details
   - Verify all products
   - Verify amounts
   - Verify status

#### Phase 4: Advanced Scenarios (2 giờ)

**Scenario 1: Concurrent Operations**
- Multiple users adding same product
- Check inventory updates
- Handle conflicts

**Scenario 2: Invalid Operations**
- Add out-of-stock product
- Exceed quantity limits
- Invalid payment info
- Checkout empty cart

**Scenario 3: Edge Cases**
- Zero quantity
- Negative prices
- Very large quantities
- Special characters in names

**Scenario 4: Performance**
- Load test: 100 concurrent users
- Response time benchmarks
- Stress test cart operations

### Advanced Test Scripts

```javascript
// Complex workflow state management
pm.test("Complete purchase workflow", () => {
    const workflow = pm.environment.get("workflow") || {};

    switch (workflow.step) {
        case "product_selected":
            // Verify product added to cart
            const cart = pm.response.json();
            workflow.cartId = cart.id;
            workflow.step = "cart_created";
            break;

        case "cart_created":
            // Verify cart total
            const cartDetails = pm.response.json();
            const expectedTotal = workflow.expectedTotal;
            pm.expect(cartDetails.total).to.equal(expectedTotal);
            workflow.step = "checkout_ready";
            break;

        case "checkout_ready":
            // Verify order created
            const order = pm.response.json();
            pm.expect(order.status).to.equal("pending");
            workflow.step = "order_created";
            break;
    }

    pm.environment.set("workflow", JSON.stringify(workflow));
});

// Dynamic test generation
const products = pm.response.json();

products.forEach((product, index) => {
    pm.test(`Product ${index + 1} has valid price`, () => {
        pm.expect(product.price).to.be.a('number');
        pm.expect(product.price).to.be.above(0);
    });

    pm.test(`Product ${index + 1} has image`, () => {
        pm.expect(product.image).to.be.a('string');
        pm.expect(product.image).to.match(/^https?:\/\//);
    });
});
```

### Deliverables

- [ ] Collection với 40+ requests
- [ ] 100+ test cases
- [ ] Complete purchase workflow
- [ ] Error handling cho tất cả cases
- [ ] Performance tests
- [ ] Newman integration
- [ ] HTML test report
- [ ] Full documentation

## Đánh Giá và Tiếp Theo

### Self-Assessment Checklist

Sau khi hoàn thành 3 dự án:

- [ ] Tôi có thể test CRUD operations
- [ ] Tôi hiểu authentication flows
- [ ] Tôi có thể viết test scripts
- [ ] Tôi có thể chain requests
- [ ] Tôi hiểu environments và variables
- [ ] Tôi có thể handle errors
- [ ] Tôi có thể run automated tests
- [ ] Tôi biết best practices
- [ ] Tôi tự tin test real APIs

### Next Steps

Nếu bạn đã hoàn thành cả 3 dự án:

🎉 **Chúc mừng! Bạn đã sẵn sàng cho công việc thực tế!**

**Để nâng cao hơn nữa:**
1. Học Newman CI/CD integration
2. Học Performance testing (JMeter, K6)
3. Học API Security testing
4. Học GraphQL API testing
5. Đóng góp vào open source projects
6. Tạo blog chia sẻ kinh nghiệm

## Tài Nguyên Dự Án

### APIs Để Practice

1. **JSONPlaceholder** - https://jsonplaceholder.typicode.com
2. **ReqRes** - https://reqres.in
3. **Fake Store API** - https://fakestoreapi.com
4. **HTTPBin** - https://httpbin.org
5. **RestfulBooker** - https://restful-booker.herokuapp.com
6. **PetStore** - https://petstore.swagger.io
7. **DummyJSON** - https://dummyjson.com

### Collections Mẫu

Tìm kiếm "Postman API Network" để xem collections public từ community.

---

[⬅️ Chương 7](../07-best-practices/README.md) | [Về Trang Chủ](../README.md) | [Tiếp Theo: Tài Liệu Tham Khảo ➡️](../tai-lieu-tham-khao/README.md)
