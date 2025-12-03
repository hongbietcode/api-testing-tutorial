# Dự Án 3: E-commerce API Testing

**Độ khó:** ⭐⭐⭐ Khó (Advanced)  
**Thời gian ước tính:** 8-10 giờ  
**API:** Fake Store API - https://fakestoreapi.com

## Mục Tiêu Học

Sau khi hoàn thành project này, bạn sẽ:
- ✅ Test multi-entity systems (Products, Cart, Orders)
- ✅ Handle complex business logic
- ✅ Test complete transaction workflows
- ✅ State management trong API testing
- ✅ Performance và stress testing
- ✅ Integration testing strategies

> **📸 HÌNH ẢNH:** E-commerce System Overview
> - File: `ecommerce-system-diagram.png`
> - Nội dung: Architecture diagram showing Products → Cart → Orders flow với database icons và arrows

<!-- IMAGE_PLACEHOLDER: ecommerce-system-diagram.png -->

## API Overview

**Base URL:** `https://fakestoreapi.com`

### Entities

1. **Products:** Catalog management
2. **Carts:** Shopping cart operations
3. **Users:** User management
4. **Auth:** Authentication

### All Endpoints

```
# Products
GET    /products
GET    /products/{id}
GET    /products/categories
GET    /products/category/{category}
POST   /products
PUT    /products/{id}
PATCH  /products/{id}
DELETE /products/{id}

# Carts
GET    /carts
GET    /carts/{id}
GET    /carts/user/{userId}
POST   /carts
PUT    /carts/{id}
PATCH  /carts/{id}
DELETE /carts/{id}

# Users
GET    /users
GET    /users/{id}
POST   /users
PUT    /users/{id}
DELETE /users/{id}

# Auth
POST   /auth/login
```

## Phase 1: Product Management (2 giờ)

### Task 1.1: Browse Products

**Request:** GET `/products`

**Expected Response:**
```json
[
  {
    "id": 1,
    "title": "Fjallraven Backpack",
    "price": 109.95,
    "description": "Your perfect pack...",
    "category": "men's clothing",
    "image": "https://fakestoreapi.com/img/81fPKd-2AYL._AC_SL1500_.jpg",
    "rating": {
      "rate": 3.9,
      "count": 120
    }
  }
]
```

**Tests:**
```javascript
pm.test("Status code is 200", () => {
    pm.response.to.have.status(200);
});

pm.test("Response is array of products", () => {
    const products = pm.response.json();
    pm.expect(products).to.be.an('array');
    pm.expect(products.length).to.be.above(0);
});

pm.test("Each product has required fields", () => {
    const products = pm.response.json();
    
    products.forEach(product => {
        pm.expect(product).to.have.all.keys(
            'id', 'title', 'price', 'description', 
            'category', 'image', 'rating'
        );
    });
});

pm.test("Product prices are valid", () => {
    const products = pm.response.json();
    
    products.forEach(product => {
        pm.expect(product.price).to.be.a('number');
        pm.expect(product.price).to.be.above(0);
    });
});

pm.test("Images are valid URLs", () => {
    const products = pm.response.json();
    
    products.forEach(product => {
        pm.expect(product.image).to.match(/^https?:\/\/.+/);
    });
});

// Save products for later use
pm.environment.set("allProducts", JSON.stringify(pm.response.json()));
```

### Task 1.2: Get Categories

**Request:** GET `/products/categories`

**Expected Response:**
```json
[
  "electronics",
  "jewelery",
  "men's clothing",
  "women's clothing"
]
```

**Tests:**
```javascript
pm.test("Categories retrieved", () => {
    pm.response.to.have.status(200);
});

pm.test("Categories is array of strings", () => {
    const categories = pm.response.json();
    pm.expect(categories).to.be.an('array');
    
    categories.forEach(category => {
        pm.expect(category).to.be.a('string');
        pm.expect(category.length).to.be.above(0);
    });
});

pm.test("Expected categories present", () => {
    const categories = pm.response.json();
    const expectedCategories = ["electronics", "jewelery", "men's clothing", "women's clothing"];
    
    expectedCategories.forEach(expected => {
        pm.expect(categories).to.include(expected);
    });
});

// Save for filtering tests
pm.environment.set("categories", JSON.stringify(pm.response.json()));
```

### Task 1.3: Filter by Category

**Request:** GET `/products/category/electronics`

**Tests:**
```javascript
pm.test("Products filtered by category", () => {
    pm.response.to.have.status(200);
});

pm.test("All products belong to requested category", () => {
    const products = pm.response.json();
    const requestedCategory = pm.request.url.getPath().split('/').pop();
    
    products.forEach(product => {
        pm.expect(product.category).to.equal(requestedCategory);
    });
});

pm.test("Category products match expected count", () => {
    const products = pm.response.json();
    pm.expect(products.length).to.be.above(0);
});
```

### Task 1.4: Get Product Details

**Request:** GET `/products/1`

**Tests:**
```javascript
pm.test("Product found", () => {
    pm.response.to.have.status(200);
});

pm.test("Product structure valid", () => {
    const product = pm.response.json();
    
    pm.expect(product).to.have.property('id');
    pm.expect(product).to.have.property('title');
    pm.expect(product).to.have.property('price');
    pm.expect(product).to.have.property('description');
    pm.expect(product).to.have.property('category');
    pm.expect(product).to.have.property('image');
    pm.expect(product).to.have.property('rating');
});

pm.test("Rating structure valid", () => {
    const rating = pm.response.json().rating;
    
    pm.expect(rating).to.have.property('rate');
    pm.expect(rating).to.have.property('count');
    pm.expect(rating.rate).to.be.a('number');
    pm.expect(rating.rate).to.be.within(0, 5);
    pm.expect(rating.count).to.be.a('number');
    pm.expect(rating.count).to.be.at.least(0);
});

// Save for cart operations
pm.environment.set("selectedProductId", pm.response.json().id);
pm.environment.set("selectedProductPrice", pm.response.json().price);
```

> **📸 HÌNH ẢNH:** Product Catalog Tests
> - File: `product-catalog-tests.png`
> - Nội dung: Screenshot showing test results for product endpoints với green checkmarks, response previews

<!-- IMAGE_PLACEHOLDER: product-catalog-tests.png -->

### Task 1.5: Sort and Limit

**Request:** GET `/products?limit=5&sort=desc`

**Tests:**
```javascript
pm.test("Limited results returned", () => {
    const products = pm.response.json();
    const limit = parseInt(pm.request.url.query.get("limit"));
    pm.expect(products.length).to.equal(limit);
});

pm.test("Products sorted correctly", () => {
    const products = pm.response.json();
    const sortOrder = pm.request.url.query.get("sort");
    
    if (sortOrder === 'desc') {
        for (let i = 1; i < products.length; i++) {
            pm.expect(products[i-1].id).to.be.above(products[i].id);
        }
    }
});
```

## Phase 2: Shopping Cart Operations (2 giờ)

### Task 2.1: Create Cart

**Request:** POST `/carts`

**Body:**
```json
{
  "userId": 1,
  "date": "2024-01-15",
  "products": [
    {"productId": 1, "quantity": 2},
    {"productId": 5, "quantity": 1}
  ]
}
```

**Expected Response:**
```json
{
  "id": 11,
  "userId": 1,
  "date": "2024-01-15",
  "products": [
    {"productId": 1, "quantity": 2},
    {"productId": 5, "quantity": 1}
  ]
}
```

**Tests:**
```javascript
pm.test("Cart created successfully", () => {
    pm.response.to.have.status(200);
});

pm.test("Cart has ID assigned", () => {
    const cart = pm.response.json();
    pm.expect(cart).to.have.property('id');
    pm.expect(cart.id).to.be.a('number');
});

pm.test("Cart products match request", () => {
    const cart = pm.response.json();
    const requestBody = JSON.parse(pm.request.body.raw);
    
    pm.expect(cart.products.length).to.equal(requestBody.products.length);
    
    cart.products.forEach((product, index) => {
        pm.expect(product.productId).to.equal(requestBody.products[index].productId);
        pm.expect(product.quantity).to.equal(requestBody.products[index].quantity);
    });
});

pm.test("Calculate and verify cart total", () => {
    const cart = pm.response.json();
    const allProducts = JSON.parse(pm.environment.get("allProducts"));
    
    let expectedTotal = 0;
    cart.products.forEach(item => {
        const product = allProducts.find(p => p.id === item.productId);
        if (product) {
            expectedTotal += product.price * item.quantity;
        }
    });
    
    console.log(`Expected cart total: $${expectedTotal.toFixed(2)}`);
    pm.environment.set("cartTotal", expectedTotal);
});

// Save cart ID
pm.environment.set("activeCartId", pm.response.json().id);
```

### Task 2.2: Get Cart Details

**Request:** GET `/carts/5`

**Tests:**
```javascript
pm.test("Cart retrieved", () => {
    pm.response.to.have.status(200);
});

pm.test("Cart structure valid", () => {
    const cart = pm.response.json();
    pm.expect(cart).to.have.property('id');
    pm.expect(cart).to.have.property('userId');
    pm.expect(cart).to.have.property('date');
    pm.expect(cart).to.have.property('products');
});

pm.test("Products array not empty", () => {
    const cart = pm.response.json();
    pm.expect(cart.products).to.be.an('array');
    pm.expect(cart.products.length).to.be.above(0);
});

pm.test("Each product has required fields", () => {
    const cart = pm.response.json();
    
    cart.products.forEach(product => {
        pm.expect(product).to.have.property('productId');
        pm.expect(product).to.have.property('quantity');
        pm.expect(product.quantity).to.be.above(0);
    });
});
```

### Task 2.3: Update Cart

**Request:** PUT `/carts/7`

**Body:**
```json
{
  "userId": 3,
  "date": "2024-01-15",
  "products": [
    {"productId": 2, "quantity": 3}
  ]
}
```

**Tests:**
```javascript
pm.test("Cart updated", () => {
    pm.response.to.have.status(200);
});

pm.test("Updated cart returned", () => {
    const cart = pm.response.json();
    const requestBody = JSON.parse(pm.request.body.raw);
    
    pm.expect(cart.userId).to.equal(requestBody.userId);
    pm.expect(cart.products.length).to.equal(requestBody.products.length);
});
```

### Task 2.4: Get User's Carts

**Request:** GET `/carts/user/1`

**Tests:**
```javascript
pm.test("User carts retrieved", () => {
    pm.response.to.have.status(200);
});

pm.test("All carts belong to user", () => {
    const carts = pm.response.json();
    const userId = parseInt(pm.request.url.getPath().split('/').pop());
    
    carts.forEach(cart => {
        pm.expect(cart.userId).to.equal(userId);
    });
});

pm.test("Carts sorted by date", () => {
    const carts = pm.response.json();
    
    if (carts.length > 1) {
        for (let i = 1; i < carts.length; i++) {
            const date1 = new Date(carts[i-1].date);
            const date2 = new Date(carts[i].date);
            pm.expect(date1).to.be.at.least(date2);
        }
    }
});
```

> **📸 HÌNH ẢNH:** Shopping Cart State Diagram
> - File: `shopping-cart-states.png`
> - Nội dung: State diagram: Empty → Items Added → Updated → Checked Out → Completed với transitions

<!-- IMAGE_PLACEHOLDER: shopping-cart-states.png -->

## Phase 3: Complete Purchase Workflow (3 giờ)

### Workflow: End-to-End E-commerce Transaction

**Step 1: User Authentication**

**Pre-request Script:**
```javascript
// Initialize workflow
pm.environment.set("workflow", JSON.stringify({
    step: "auth",
    userId: null,
    token: null,
    selectedProducts: [],
    cartId: null,
    total: 0
}));
```

**Request:** POST `/auth/login`

**Body:**
```json
{
  "username": "mor_2314",
  "password": "83r5^_"
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
    
    // Update workflow
    const workflow = JSON.parse(pm.environment.get("workflow"));
    workflow.token = response.token;
    workflow.step = "browse";
    pm.environment.set("workflow", JSON.stringify(workflow));
});
```

**Step 2: Browse and Select Products**

**Request:** GET `/products?limit=10`

**Tests:**
```javascript
pm.test("Products loaded", () => {
    pm.response.to.have.status(200);
});

pm.test("Select 3 products for cart", () => {
    const products = pm.response.json();
    const workflow = JSON.parse(pm.environment.get("workflow"));
    
    // Select first 3 products
    workflow.selectedProducts = products.slice(0, 3).map(p => ({
        productId: p.id,
        quantity: Math.floor(Math.random() * 3) + 1,  // 1-3 items
        price: p.price
    }));
    
    workflow.step = "addToCart";
    pm.environment.set("workflow", JSON.stringify(workflow));
    
    console.log("Selected products:", workflow.selectedProducts);
});
```

**Step 3: Add to Cart**

**Request:** POST `/carts`

**Pre-request Script:**
```javascript
const workflow = JSON.parse(pm.environment.get("workflow"));

const cartData = {
    userId: 1,
    date: new Date().toISOString().split('T')[0],
    products: workflow.selectedProducts.map(p => ({
        productId: p.productId,
        quantity: p.quantity
    }))
};

pm.variables.set("cartData", JSON.stringify(cartData));
```

**Body:**
```json
{{cartData}}
```

**Tests:**
```javascript
pm.test("Cart created", () => {
    pm.response.to.have.status(200);
});

pm.test("Calculate total", () => {
    const workflow = JSON.parse(pm.environment.get("workflow"));
    const cart = pm.response.json();
    
    // Calculate total
    let total = 0;
    workflow.selectedProducts.forEach(product => {
        total += product.price * product.quantity;
    });
    
    workflow.cartId = cart.id;
    workflow.total = total;
    workflow.step = "review";
    pm.environment.set("workflow", JSON.stringify(workflow));
    
    console.log(`Cart total: $${total.toFixed(2)}`);
});
```

**Step 4: Review Cart**

**Request:** GET `/carts/{{activeCartId}}`

**Tests:**
```javascript
pm.test("Cart contents verified", () => {
    pm.response.to.have.status(200);
    
    const cart = pm.response.json();
    const workflow = JSON.parse(pm.environment.get("workflow"));
    
    pm.expect(cart.products.length).to.equal(workflow.selectedProducts.length);
});

pm.test("Ready for checkout", () => {
    const workflow = JSON.parse(pm.environment.get("workflow"));
    workflow.step = "checkout";
    pm.environment.set("workflow", JSON.stringify(workflow));
});
```

**Step 5: Checkout (Simulated)**

**Tests:**
```javascript
pm.test("Checkout simulation", () => {
    const workflow = JSON.parse(pm.environment.get("workflow"));
    
    // Calculate final amounts
    const subtotal = workflow.total;
    const tax = subtotal * 0.08;  // 8% tax
    const shipping = 10.00;
    const finalTotal = subtotal + tax + shipping;
    
    console.log(`Subtotal: $${subtotal.toFixed(2)}`);
    console.log(`Tax (8%): $${tax.toFixed(2)}`);
    console.log(`Shipping: $${shipping.toFixed(2)}`);
    console.log(`Final Total: $${finalTotal.toFixed(2)}`);
    
    workflow.subtotal = subtotal;
    workflow.tax = tax;
    workflow.shipping = shipping;
    workflow.finalTotal = finalTotal;
    workflow.step = "complete";
    pm.environment.set("workflow", JSON.stringify(workflow));
    
    pm.expect(finalTotal).to.be.above(0);
});
```

**Step 6: Order Confirmation**

**Tests:**
```javascript
pm.test("Order completed", () => {
    const workflow = JSON.parse(pm.environment.get("workflow"));
    
    console.log("=== ORDER SUMMARY ===");
    console.log(`Cart ID: ${workflow.cartId}`);
    console.log(`Items: ${workflow.selectedProducts.length}`);
    console.log(`Subtotal: $${workflow.subtotal.toFixed(2)}`);
    console.log(`Tax: $${workflow.tax.toFixed(2)}`);
    console.log(`Shipping: $${workflow.shipping.toFixed(2)}`);
    console.log(`Total: $${workflow.finalTotal.toFixed(2)}`);
    console.log("=====================");
    
    pm.expect(workflow.step).to.equal("complete");
});

// Cleanup
pm.environment.unset("workflow");
pm.environment.unset("activeCartId");
```

> **📸 HÌNH ẢNH:** Complete Purchase Workflow
> - File: `purchase-workflow-diagram.png`
> - Nội dung: Detailed flowchart: Login → Browse → Select → Add to Cart → Review → Checkout → Confirm với data flow annotations

<!-- IMAGE_PLACEHOLDER: purchase-workflow-diagram.png -->

## Phase 4: Advanced Testing (2 giờ)

### Task 4.1: Concurrent Cart Operations

**Scenario:** Multiple users adding same product

**Pre-request Script:**
```javascript
// Simulate concurrent users
const users = [1, 2, 3, 4, 5];
const randomUser = users[Math.floor(Math.random() * users.length)];
pm.environment.set("currentUserId", randomUser);
```

**Request:** POST `/carts`

**Body:**
```json
{
  "userId": {{currentUserId}},
  "date": "2024-01-15",
  "products": [
    {"productId": 1, "quantity": 1}
  ]
}
```

**Tests:**
```javascript
pm.test("Concurrent cart creation handled", () => {
    pm.response.to.have.status(200);
    console.log(`User ${pm.environment.get("currentUserId")} created cart`);
});
```

### Task 4.2: Edge Cases

**Test Case 1: Empty Cart**
```json
{
  "userId": 1,
  "date": "2024-01-15",
  "products": []
}
```

**Test:**
```javascript
pm.test("Empty cart rejected or handled", () => {
    // API might accept or reject empty cart
    pm.expect([200, 400]).to.include(pm.response.code);
});
```

**Test Case 2: Invalid Product ID**
```json
{
  "userId": 1,
  "date": "2024-01-15",
  "products": [
    {"productId": 99999, "quantity": 1}
  ]
}
```

**Test Case 3: Zero Quantity**
```json
{
  "products": [
    {"productId": 1, "quantity": 0}
  ]
}
```

**Test Case 4: Negative Quantity**
```json
{
  "products": [
    {"productId": 1, "quantity": -5}
  ]
}
```

### Task 4.3: Performance Testing

**Load Test Script:**
```javascript
// Pre-request
pm.environment.set("startTime", Date.now());

// Tests
const endTime = Date.now();
const startTime = pm.environment.get("startTime");
const duration = endTime - startTime;

pm.test("Response time under 500ms", () => {
    pm.expect(pm.response.responseTime).to.be.below(500);
});

// Track all response times
const times = JSON.parse(pm.globals.get("responseTimes") || "[]");
times.push(pm.response.responseTime);
pm.globals.set("responseTimes", JSON.stringify(times));

// On last iteration
if (pm.info.iteration === pm.info.iterationCount - 1) {
    const avg = times.reduce((a, b) => a + b, 0) / times.length;
    const max = Math.max(...times);
    const min = Math.min(...times);
    
    console.log(`Performance Summary:`);
    console.log(`  Average: ${avg.toFixed(2)}ms`);
    console.log(`  Min: ${min}ms`);
    console.log(`  Max: ${max}ms`);
    
    pm.globals.unset("responseTimes");
}
```

**Run with Newman:**
```bash
newman run ecommerce-api.json -n 100 --delay-request 50
```

### Task 4.4: Data Integrity Tests

**Verify cart calculations:**
```javascript
pm.test("Cart total matches product prices", () => {
    const cart = pm.response.json();
    const allProducts = JSON.parse(pm.environment.get("allProducts"));
    
    let calculatedTotal = 0;
    cart.products.forEach(item => {
        const product = allProducts.find(p => p.id === item.productId);
        if (product) {
            calculatedTotal += product.price * item.quantity;
        }
    });
    
    const storedTotal = parseFloat(pm.environment.get("cartTotal"));
    pm.expect(calculatedTotal).to.be.closeTo(storedTotal, 0.01);
});
```

**Test product availability:**
```javascript
pm.test("All cart products exist", () => {
    const cart = pm.response.json();
    const allProducts = JSON.parse(pm.environment.get("allProducts"));
    
    cart.products.forEach(item => {
        const product = allProducts.find(p => p.id === item.productId);
        pm.expect(product).to.not.be.undefined;
    });
});
```

## Deliverables Checklist

### Collection Structure
- [ ] **01-Products** folder (8 requests)
  - GET All Products
  - GET Categories
  - GET By Category
  - GET Product Details
  - GET with Sorting/Limiting
  - POST Create Product
  - PUT Update Product
  - DELETE Product
  
- [ ] **02-Shopping-Cart** folder (6 requests)
  - POST Create Cart
  - GET Cart Details
  - GET User Carts
  - PUT Update Cart
  - PATCH Partial Update
  - DELETE Cart
  
- [ ] **03-Purchase-Workflow** folder (6 requests)
  - Login
  - Browse Products
  - Select Products
  - Add to Cart
  - Review Cart
  - Checkout

- [ ] **04-Advanced-Tests** folder (6 requests)
  - Concurrent Operations
  - Edge Cases (4 tests)
  - Performance Tests
  - Data Integrity

### Tests Coverage
- [ ] 100+ test assertions
- [ ] All CRUD operations tested
- [ ] Complete workflow validation
- [ ] Error handling comprehensive
- [ ] Performance benchmarks
- [ ] Edge cases covered

### Documentation
- [ ] Collection description with workflow diagram
- [ ] Each request documented
- [ ] Environment setup guide
- [ ] Workflow explanations
- [ ] Troubleshooting guide

### Automation
- [ ] Environment file
- [ ] Newman run scripts
- [ ] CI/CD integration ready
- [ ] HTML report template
- [ ] Performance metrics tracking

> **📸 HÌNH ẢNH:** Final Collection Summary
> - File: `ecommerce-collection-summary.png`
> - Nội dung: Screenshot complete collection với all folders expanded, 40+ requests, test counts, và pass rate 100%

<!-- IMAGE_PLACEHOLDER: ecommerce-collection-summary.png -->

## Advanced Techniques

### 1. State Machine Implementation

```javascript
// Collection Pre-request
const StateMachine = {
    states: {
        INIT: 'init',
        BROWSING: 'browsing',
        CART_ACTIVE: 'cart_active',
        CHECKOUT: 'checkout',
        COMPLETE: 'complete'
    },
    
    transition: function(currentState, action) {
        const transitions = {
            'init': { 'browse': 'browsing' },
            'browsing': { 'addToCart': 'cart_active' },
            'cart_active': { 'checkout': 'checkout' },
            'checkout': { 'complete': 'complete' }
        };
        
        return transitions[currentState]?.[action] || currentState;
    }
};

pm.globals.set("StateMachine", StateMachine.toString());
```

### 2. Dynamic Test Generation

```javascript
const products = pm.response.json();

// Generate test for each product dynamically
products.forEach((product, index) => {
    eval(`
        pm.test("Product ${index + 1}: Valid price", () => {
            pm.expect(${product.price}).to.be.above(0);
        });
        
        pm.test("Product ${index + 1}: Has rating", () => {
            pm.expect(${product.rating.rate}).to.be.within(0, 5);
        });
    `);
});
```

### 3. Transaction Rollback Simulation

```javascript
// Save state before operation
pm.environment.set("cartBackup", pm.environment.get("activeCart"));

// If operation fails, rollback
if (pm.response.code !== 200) {
    pm.environment.set("activeCart", pm.environment.get("cartBackup"));
    console.log("Operation failed, state rolled back");
}
```

## Performance Benchmarks

### Target SLAs

| Endpoint Type | Target | Max Acceptable |
|---------------|--------|----------------|
| GET List | < 300ms | 500ms |
| GET Single | < 200ms | 400ms |
| POST Create | < 500ms | 1000ms |
| PUT Update | < 500ms | 1000ms |
| DELETE | < 300ms | 600ms |

### Load Test Results Template

```
=== LOAD TEST RESULTS ===
Total Requests: 100
Duration: 15.2s
Requests/sec: 6.6

Response Times:
  Min: 145ms
  Max: 823ms
  Average: 312ms
  P95: 456ms
  P99: 712ms

Status Codes:
  200 OK: 95
  201 Created: 5
  Errors: 0

Pass Rate: 100%
========================
```

## Troubleshooting

**Issue:** Cart total calculation incorrect
- **Solution:** Ensure products are loaded before cart creation
- **Fix:** Add dependency check in pre-request

**Issue:** Workflow state gets corrupted
- **Solution:** Add state validation in each step
- **Fix:** Implement state machine pattern

**Issue:** Performance degrades with iterations
- **Solution:** Add cleanup between iterations
- **Fix:** Clear environment variables periodically

## Next Steps

After completing this project:

1. **Share & Collaborate**
   - Share collection with team
   - Document lessons learned
   - Create reusable templates

2. **Enhance Testing**
   - Add security tests (SQL injection, XSS)
   - Implement contract testing
   - Add accessibility checks

3. **Production Ready**
   - Set up monitors
   - Create CI/CD pipeline
   - Implement alerting

4. **Continue Learning**
   - GraphQL APIs
   - WebSocket testing
   - gRPC APIs
   - Microservices testing

## Congratulations! 🎉

You've completed the most advanced project in this course. You now have:
- ✅ Experience with complex workflows
- ✅ Multi-entity testing skills
- ✅ Performance testing knowledge
- ✅ Production-ready test suites
- ✅ Advanced automation capabilities

**You're ready for real-world API testing challenges!**

---

[⬅️ Project 2: User Management](./du-an-2-user-management.md) | [Tổng Quan Chương 8](./README.md) | [Về Trang Chủ ➡️](../README.md)
