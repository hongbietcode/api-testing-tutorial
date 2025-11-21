# Dự Án 1: TODO List API

**Độ khó:** ⭐ Dễ
**Thời gian:** 2-3 giờ
**Mục tiêu:** Làm quen với CRUD operations và test automation cơ bản

## Tổng Quan Dự Án

Bạn sẽ xây dựng một test suite hoàn chỉnh cho TODO List API, bao gồm:
- ✅ Tất cả CRUD operations
- ✅ Query parameters và filtering
- ✅ Validation tests
- ✅ Automated test scripts
- ✅ Collection organization

## Setup

### API Information

**Base URL:** `https://jsonplaceholder.typicode.com`

**Endpoints:**
```
GET    /todos          - Lấy tất cả todos
GET    /todos/{id}     - Lấy 1 todo
POST   /todos          - Tạo todo mới
PUT    /todos/{id}     - Update toàn bộ todo
PATCH  /todos/{id}     - Update một phần todo
DELETE /todos/{id}     - Xóa todo
```

### Postman Setup

1. **Create Collection:** "TODO API Tests"
2. **Create Environment:** "JSONPlaceholder"
   ```
   base_url = https://jsonplaceholder.typicode.com
   ```
3. **Create Folders:**
   ```
   📁 TODO API Tests
     📁 01-Basic-CRUD
     📁 02-Query-Parameters
     📁 03-Validation
     📁 04-Workflows
   ```

---

## Phase 1: Basic CRUD (30-45 phút)

### Request 1: GET All Todos

**Folder:** `01-Basic-CRUD`
**Name:** `GET All Todos`

**Request:**
```
GET {{base_url}}/todos
```

**Expected Response:**
- Status: 200 OK
- Array of 200 todos
- Each todo has: id, userId, title, completed

**Tests:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Response is an array", function () {
    const todos = pm.response.json();
    pm.expect(todos).to.be.an('array');
});

pm.test("Has 200 todos", function () {
    const todos = pm.response.json();
    pm.expect(todos).to.have.lengthOf(200);
});

pm.test("Each todo has required fields", function () {
    const todos = pm.response.json();
    const firstTodo = todos[0];

    pm.expect(firstTodo).to.have.property('id');
    pm.expect(firstTodo).to.have.property('userId');
    pm.expect(firstTodo).to.have.property('title');
    pm.expect(firstTodo).to.have.property('completed');
});

pm.test("completed is boolean", function () {
    const todo = pm.response.json()[0];
    pm.expect(todo.completed).to.be.a('boolean');
});

pm.test("Response time < 500ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(500);
});

// Save first todo ID for later use
pm.environment.set("todoId", pm.response.json()[0].id);
```

### Request 2: GET Single Todo

**Name:** `GET Todo by ID`

**Request:**
```
GET {{base_url}}/todos/{{todoId}}
```

**Tests:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Response is an object", function () {
    const todo = pm.response.json();
    pm.expect(todo).to.be.an('object');
});

pm.test("Todo has correct ID", function () {
    const todo = pm.response.json();
    const expectedId = parseInt(pm.environment.get("todoId"));
    pm.expect(todo.id).to.equal(expectedId);
});

pm.test("Has all required fields", function () {
    const todo = pm.response.json();

    pm.expect(todo).to.have.all.keys('id', 'userId', 'title', 'completed');
});
```

### Request 3: POST Create Todo

**Name:** `POST Create Todo`

**Request:**
```
POST {{base_url}}/todos
Content-Type: application/json

Body:
{
  "userId": 1,
  "title": "Learn API Testing",
  "completed": false
}
```

**Tests:**
```javascript
pm.test("Status code is 201", function () {
    pm.response.to.have.status(201);
});

pm.test("Response has id", function () {
    const todo = pm.response.json();
    pm.expect(todo).to.have.property('id');
    pm.expect(todo.id).to.be.a('number');
});

pm.test("Created todo matches request", function () {
    const todo = pm.response.json();

    pm.expect(todo.title).to.equal("Learn API Testing");
    pm.expect(todo.completed).to.be.false;
    pm.expect(todo.userId).to.equal(1);
});

// Save created todo ID
pm.environment.set("createdTodoId", pm.response.json().id);
```

### Request 4: PUT Update Todo

**Name:** `PUT Update Todo`

**Request:**
```
PUT {{base_url}}/todos/1
Content-Type: application/json

Body:
{
  "userId": 1,
  "title": "Updated Todo Title",
  "completed": true
}
```

**Tests:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Todo was updated", function () {
    const todo = pm.response.json();

    pm.expect(todo.title).to.equal("Updated Todo Title");
    pm.expect(todo.completed).to.be.true;
});
```

### Request 5: PATCH Partial Update

**Name:** `PATCH Update Completed Status`

**Request:**
```
PATCH {{base_url}}/todos/1
Content-Type: application/json

Body:
{
  "completed": true
}
```

**Tests:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Completed status updated", function () {
    const todo = pm.response.json();
    pm.expect(todo.completed).to.be.true;
});
```

### Request 6: DELETE Todo

**Name:** `DELETE Todo`

**Request:**
```
DELETE {{base_url}}/todos/1
```

**Tests:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Response is empty object", function () {
    const response = pm.response.json();
    pm.expect(Object.keys(response)).to.have.lengthOf(0);
});
```

---

## Phase 2: Query Parameters (45 phút)

### Request 7: Filter by userId

**Folder:** `02-Query-Parameters`
**Name:** `GET Todos by User`

**Request:**
```
GET {{base_url}}/todos?userId=1
```

**Query Params:**
```
userId = 1
```

**Tests:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("All todos belong to userId=1", function () {
    const todos = pm.response.json();

    todos.forEach(todo => {
        pm.expect(todo.userId).to.equal(1);
    });
});

pm.test("Returns multiple todos", function () {
    const todos = pm.response.json();
    pm.expect(todos.length).to.be.above(0);
});
```

### Request 8: Filter Completed Todos

**Name:** `GET Completed Todos`

**Request:**
```
GET {{base_url}}/todos?completed=true
```

**Tests:**
```javascript
pm.test("All todos are completed", function () {
    const todos = pm.response.json();

    todos.forEach(todo => {
        pm.expect(todo.completed).to.be.true;
    });
});
```

### Request 9: Multiple Filters

**Name:** `GET User's Completed Todos`

**Request:**
```
GET {{base_url}}/todos?userId=1&completed=false
```

**Query Params:**
```
userId = 1
completed = false
```

**Tests:**
```javascript
pm.test("Todos match both filters", function () {
    const todos = pm.response.json();

    todos.forEach(todo => {
        pm.expect(todo.userId).to.equal(1);
        pm.expect(todo.completed).to.be.false;
    });
});
```

### Request 10: Pagination

**Name:** `GET Todos with Pagination`

**Request:**
```
GET {{base_url}}/todos?_page=1&_limit=10
```

**Tests:**
```javascript
pm.test("Returns 10 todos", function () {
    const todos = pm.response.json();
    pm.expect(todos).to.have.lengthOf(10);
});

pm.test("Todo IDs are sequential", function () {
    const todos = pm.response.json();
    const firstId = todos[0].id;
    const lastId = todos[9].id;

    pm.expect(lastId - firstId).to.equal(9);
});
```

---

## Phase 3: Validation Tests (45 phút)

### Request 11: Invalid ID

**Folder:** `03-Validation`
**Name:** `GET Todo - Invalid ID`

**Request:**
```
GET {{base_url}}/todos/99999
```

**Tests:**
```javascript
pm.test("Status code is 404", function () {
    pm.response.to.have.status(404);
});

pm.test("Returns empty object", function () {
    const response = pm.response.json();
    pm.expect(Object.keys(response)).to.have.lengthOf(0);
});
```

### Request 12: POST with Missing Fields

**Name:** `POST Todo - Missing Fields`

**Request:**
```
POST {{base_url}}/todos
Content-Type: application/json

Body:
{
  "title": "Incomplete Todo"
}
```

**Tests:**
```javascript
pm.test("Status code is 201", function () {
    // JSONPlaceholder is lenient, but real APIs would return 400
    pm.response.to.have.status(201);
});

// Document expected behavior
pm.test("Note: Real API should return 400", function () {
    console.log("⚠️ JSONPlaceholder accepts incomplete data");
    console.log("Real APIs should validate required fields");
});
```

### Request 13: POST with Invalid Data Type

**Name:** `POST Todo - Invalid Type`

**Request:**
```
POST {{base_url}}/todos
Content-Type: application/json

Body:
{
  "userId": "not-a-number",
  "title": "Test",
  "completed": "not-a-boolean"
}
```

**Tests:**
```javascript
pm.test("Status code is 201", function () {
    // JSONPlaceholder accepts this, but document the issue
    pm.response.to.have.status(201);
});

pm.test("Document data type validation", function () {
    console.log("⚠️ userId should be number, got string");
    console.log("⚠️ completed should be boolean, got string");
    console.log("Real APIs should validate data types");
});
```

---

## Phase 4: Workflows (45 phút)

### Workflow 1: Complete Todo Lifecycle

**Folder:** `04-Workflows`

Tạo một series of requests chạy theo thứ tự:

**1. Create New Todo**
```javascript
// POST /todos
// Save ID to environment
pm.environment.set("workflowTodoId", pm.response.json().id);
```

**2. Get Created Todo**
```javascript
// GET /todos/{{workflowTodoId}}
// Verify it exists
pm.test("Todo exists", function () {
    pm.expect(pm.response.json().id).to.exist;
});
```

**3. Mark as Completed**
```javascript
// PATCH /todos/{{workflowTodoId}}
// Body: { "completed": true }
pm.test("Todo marked complete", function () {
    pm.expect(pm.response.json().completed).to.be.true;
});
```

**4. Verify Update**
```javascript
// GET /todos/{{workflowTodoId}}
pm.test("Completion persisted", function () {
    pm.expect(pm.response.json().completed).to.be.true;
});
```

**5. Delete Todo**
```javascript
// DELETE /todos/{{workflowTodoId}}
pm.test("Todo deleted", function () {
    pm.response.to.have.status(200);
});
```

---

## Running Collection

### Manual Run
1. Click "..." on collection
2. "Run collection"
3. Select environment
4. Click "Run TODO API Tests"

### Expected Results
- ✅ All tests pass
- ✅ 50+ assertions
- ✅ < 5 seconds total time

### Collection Runner Configuration
```
Environment: JSONPlaceholder
Iterations: 1
Delay: 0ms
Data file: (none for now)
```

---

## Data-Driven Testing (Bonus)

### Create Test Data CSV

**File: `todos.csv`**
```csv
userId,title,completed
1,Buy groceries,false
1,Walk the dog,false
2,Finish report,true
2,Call mom,false
```

### Update POST Request

```javascript
// Request body uses CSV variables
{
  "userId": {{userId}},
  "title": "{{title}}",
  "completed": {{completed}}
}

// Tests
pm.test("Created todo matches CSV data", function () {
    const todo = pm.response.json();
    const expectedTitle = pm.iterationData.get("title");

    pm.expect(todo.title).to.equal(expectedTitle);
});
```

### Run with CSV
1. Collection Runner
2. "Select File" → upload `todos.csv`
3. Iterations = 4 (number of rows)
4. Run

---

## Export và Share

### Export Collection
1. Right-click collection
2. "Export"
3. Collection v2.1 (recommended)
4. Save as `TODO-API-Tests.postman_collection.json`

### Export Environment
1. Click environment
2. Export
3. Save as `JSONPlaceholder.postman_environment.json`

### Share with Team
- Email JSON files
- Or use Postman workspace
- Or commit to Git (không commit environment nếu có secrets!)

---

## Success Criteria

Dự án hoàn thành khi:

- [ ] Có 13+ requests
- [ ] Tất cả requests có tests
- [ ] 50+ test assertions
- [ ] Tất cả tests pass
- [ ] Collection organized với folders
- [ ] Environment setup đúng
- [ ] Workflows chạy thành công
- [ ] Có thể run toàn bộ collection
- [ ] Exported collection và environment

---

## Next Steps

Sau khi hoàn thành dự án này:

1. ✅ Review test results
2. ✅ Refactor duplicate test code
3. ✅ Add more validation tests
4. ✅ Try data-driven testing với CSV
5. ✅ Document findings
6. ➡️ Chuyển sang [Dự Án 2: User Management](./du-an-2-user-management.md)

---

## Troubleshooting

### Tests Failing?
- Check environment selected
- Verify base_url variable
- Check internet connection
- Review test logic

### Variables Not Working?
- Double check variable names: `{{base_url}}`
- Verify environment is selected
- Check variable scope (environment vs global)

### Workflow Broken?
- Run requests individually first
- Check variable saving in scripts
- Verify request order

---

## Summary

Trong dự án này bạn đã:
- ✅ Thực hành tất cả CRUD operations
- ✅ Test với query parameters
- ✅ Viết comprehensive test scripts
- ✅ Organize collection professionally
- ✅ Create workflows
- ✅ Understand API testing lifecycle

**Chúc mừng! 🎉 Bạn đã hoàn thành dự án đầu tiên!**

---

[⬅️ Chương 8](./README.md) | [Tiếp Theo: Dự Án 2 ➡️](./du-an-2-user-management.md)
