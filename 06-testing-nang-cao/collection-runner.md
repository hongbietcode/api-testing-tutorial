# 6.4 Collection Runner

Collection Runner cho phép chạy toàn bộ collection tự động, hỗ trợ iterations, data-driven testing, và automation workflows.

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Chạy collections tự động
- ✅ Configure iterations và delays
- ✅ Data-driven testing với CSV/JSON
- ✅ Analyze test results
- ✅ Export results

## 1. Collection Runner Là Gì?

**Collection Runner** chạy tất cả requests trong một collection **theo thứ tự**, tự động execute tests và thu thập kết quả.

> **📸 HÌNH ẢNH:** Collection Runner Interface
> - File: `collection-runner-interface.png`
> - Nội dung: Screenshot Collection Runner window showing: collection selector, environment dropdown, iterations input, delay input, data file upload, và Run button

<!-- IMAGE_PLACEHOLDER: collection-runner-interface.png -->

### Khi Nào Dùng

- ✅ Chạy toàn bộ test suite
- ✅ Regression testing
- ✅ Data-driven testing (nhiều test data)
- ✅ Load testing (iterations)
- ✅ CI/CD automation

## 2. Mở Collection Runner

### Cách 1: Từ Collection

1. Hover vào collection
2. Click icon **"..."** (3 dots)
3. Chọn **"Run collection"**

### Cách 2: Từ Runner Button

1. Click **Runner** button ở header
2. Select collection từ sidebar

## 3. Run Configuration

### Basic Configuration

**1. Select Collection**
- Choose collection muốn chạy

**2. Select Environment**
- Chọn environment (Dev, Staging, Prod)
- Hoặc "No Environment"

**3. Iterations**
- Số lần chạy toàn bộ collection
- Default: 1
- Useful cho load testing

**4. Delay**
- Delay giữa các requests (milliseconds)
- Default: 0
- Recommend: 100-500ms để tránh overwhelm server

**5. Data File** (Optional)
- Upload CSV hoặc JSON file
- Cho data-driven testing

### Example Configuration

```
Collection: API Test Suite
Environment: Development
Iterations: 3
Delay: 200ms
Data File: test-data.csv
```

## 4. Running Collections

### Bước 1: Configure

Set up các options như trên

### Bước 2: Select Requests

- Default: Chạy tất cả requests
- Có thể uncheck requests không muốn chạy

### Bước 3: Run

Click nút **"Run [Collection Name]"**

### Bước 4: Monitor Progress

> **📸 HÌNH ẢNH:** Runner Progress
> - File: `runner-progress.png`
> - Nội dung: Screenshot showing runner in progress: requests being executed, progress bar, passed/failed count updating in real-time

<!-- IMAGE_PLACEHOLDER: runner-progress.png -->

Xem real-time:
- Requests đang chạy
- Tests passed/failed
- Response times
- Errors

## 5. Viewing Results

> **📸 HÌNH ẢNH:** Runner Results Summary
> - File: `runner-results-summary.png`
> - Nội dung: Screenshot results page showing: summary stats (X passed, Y failed), list of requests with status indicators, response times, và test results

<!-- IMAGE_PLACEHOLDER: runner-results-summary.png -->

### Results Summary

Sau khi run xong, bạn thấy:

```
Summary:
- Total Requests: 15
- Passed Tests: 42
- Failed Tests: 3
- Avg Response Time: 234ms
```

### Detailed Results

Click vào mỗi request để xem:
- Request details
- Response
- Test results (passed ✅ / failed ❌)
- Console logs

### Failed Tests

```
❌ Status code is 200
  Expected: 200
  Actual: 404

❌ Response has user data
  Expected property 'name' to exist
```

## 6. Data-Driven Testing

Chạy cùng requests với data khác nhau.

### CSV Format

**test-users.csv:**
```csv
firstName,lastName,email,age
John,Doe,john@test.com,25
Jane,Smith,jane@test.com,30
Bob,Johnson,bob@test.com,28
```

### JSON Format

**test-users.json:**
```json
[
  {
    "firstName": "John",
    "lastName": "Doe",
    "email": "john@test.com",
    "age": 25
  },
  {
    "firstName": "Jane",
    "lastName": "Smith",
    "email": "jane@test.com",
    "age": 30
  }
]
```

### Using Data File

**1. Upload File**
- Click "Select File" trong Runner
- Choose CSV hoặc JSON

**2. Reference trong Request**
```json
{
  "firstName": "{{firstName}}",
  "lastName": "{{lastName}}",
  "email": "{{email}}",
  "age": {{age}}
}
```

**3. Access trong Scripts**
```javascript
// Get data file values
const firstName = pm.iterationData.get("firstName");
const email = pm.iterationData.get("email");

console.log("Testing user:", firstName, email);

pm.test("User created", () => {
    const response = pm.response.json();
    pm.expect(response.email).to.eql(email);
});
```

**4. Run**

Collection chạy **một lần cho mỗi row** trong data file!

```
Iteration 1: John Doe, john@test.com
Iteration 2: Jane Smith, jane@test.com
Iteration 3: Bob Johnson, bob@test.com
```

> **📸 HÌNH ẢNH:** Data-Driven Testing Results
> - File: `data-driven-results.png`
> - Nội dung: Results showing 3 iterations, mỗi iteration với different data from CSV, showing which data row was used

<!-- IMAGE_PLACEHOLDER: data-driven-results.png -->

## 7. Advanced Iteration Control

### Iteration Info

```javascript
// Current iteration (0-indexed)
const currentIteration = pm.info.iteration;

// Total iterations
const totalIterations = pm.info.iterationCount;

console.log(`Iteration ${currentIteration + 1} of ${totalIterations}`);

// Different behavior per iteration
if (currentIteration === 0) {
    console.log("First iteration - setup");
} else if (currentIteration === totalIterations - 1) {
    console.log("Last iteration - cleanup");
}
```

### Skip Requests Conditionally

```javascript
// Pre-request Script
const iteration = pm.info.iteration;

// Skip this request in first iteration
if (iteration === 0) {
    postman.setNextRequest(null); // Skip this request
}
```

### Conditional Workflows

```javascript
// Tests tab
const response = pm.response.json();

if (response.status === "pending") {
    // Repeat same request
    postman.setNextRequest(pm.info.requestName);
} else {
    // Continue to next request
    postman.setNextRequest(null);
}
```

## 8. Request Order Control

### Default Order

Requests chạy **top to bottom** trong collection.

### Change Order

```javascript
// Tests tab
// Jump to specific request
postman.setNextRequest("Request Name");

// Skip to end
postman.setNextRequest(null);

// Conditional branching
if (pm.response.code === 401) {
    postman.setNextRequest("Login");
} else {
    postman.setNextRequest("Get User Profile");
}
```

### Loop Pattern

```javascript
// Request: Get User
const response = pm.response.json();

if (response.hasMore) {
    // Loop back to same request
    postman.setNextRequest("Get User");
} else {
    // Done, move to next
    postman.setNextRequest(null);
}
```

## 9. Exporting Results

### Export Run Results

1. Sau khi run xong
2. Click **"Export Results"**
3. Save JSON file

**Exported file contains:**
- All request/response data
- Test results
- Timing information
- Console logs

### Results File Format

```json
{
  "collection": {
    "info": { "name": "API Tests" }
  },
  "run": {
    "stats": {
      "requests": { "total": 10, "pending": 0, "failed": 2 },
      "tests": { "total": 25, "pending": 0, "failed": 3 }
    },
    "timings": {
      "responseAverage": 234,
      "responseMin": 89,
      "responseMax": 567
    }
  },
  "executions": [ /* detailed results */ ]
}
```

## 10. Practical Examples

### Example 1: Regression Test Suite

**Collection: E2E Tests**
```
1. Health Check
2. Login
3. Get User Profile
4. Update Profile
5. Get Orders
6. Create Order
7. Delete Order
8. Logout
```

**Run Config:**
```
Iterations: 1
Delay: 100ms
Environment: Staging
```

**Expected:**
- All 8 requests execute
- All tests pass
- Avg response time < 500ms

### Example 2: Create Multiple Users

**Data File: users.csv**
```csv
name,email,role
Admin User,admin@test.com,admin
Test User,test@test.com,user
Guest User,guest@test.com,guest
```

**Request: Create User**
```json
POST /users
{
  "name": "{{name}}",
  "email": "{{email}}",
  "role": "{{role}}"
}
```

**Tests:**
```javascript
pm.test("User created", () => {
    pm.response.to.have.status(201);
});

const data = pm.iterationData.toObject();
console.log("Created user:", data.name, data.email);
```

**Run:** 3 iterations (một cho mỗi user)

### Example 3: Load Testing

**Collection: Performance Tests**

**Run Config:**
```
Iterations: 100
Delay: 50ms
```

**Tests:**
```javascript
pm.test("Response time < 200ms", () => {
    pm.expect(pm.response.responseTime).to.be.below(200);
});

pm.test("No server errors", () => {
    pm.expect(pm.response.code).to.be.below(500);
});
```

**Goal:** Verify API handles 100 requests without degradation

## 11. Troubleshooting

### Issue: Requests Fail

**Causes:**
- Wrong environment selected
- Missing variables
- Data file format wrong
- Server rate limiting

**Solutions:**
- Check environment variables set
- Validate data file format (CSV headers match variables)
- Add delay between requests
- Check console logs

### Issue: Tests Fail

**Debug:**
1. Run individual request first
2. Check console logs
3. Verify test logic
4. Check expected vs actual values

### Issue: Slow Performance

**Optimize:**
- Reduce iterations
- Increase delay
- Run fewer requests
- Use faster environment

## 12. Best Practices

### ✅ DO

- Run với small iterations first để test
- Use delays để avoid overwhelming server
- Validate data files before running
- Check environment before running
- Export results for record keeping
- Use descriptive collection names
- Clean up test data after run

### ❌ DON'T

- Run với nhiều iterations trên production
- Skip validation tests
- Forget to set proper environment
- Use delays quá nhỏ (rate limiting)
- Ignore failed tests
- Run destructive operations without confirmation

## 13. Integration với Newman

Collection Runner results có thể run với Newman CLI:

```bash
# Export collection và environment
newman run collection.json -e environment.json

# Với data file
newman run collection.json -d test-data.csv

# Với iterations
newman run collection.json -n 10

# Generate report
newman run collection.json -r html
```

(Chi tiết về Newman ở bài tiếp theo)

## 14. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ Chạy collections tự động với Runner
- ✅ Configure iterations, delays, environments
- ✅ Data-driven testing với CSV/JSON
- ✅ Control request order
- ✅ Export và analyze results
- ✅ Troubleshooting common issues
- ✅ Best practices

## Next Steps

- **Bài tiếp theo**: [6.5 Mock Servers](./mock-servers.md)

---

[⬅️ Variables & Data Management](./variables-data-management.md) | [Tổng Quan Chương 6](./README.md) | [Tiếp Theo: Mock Servers ➡️](./mock-servers.md)
