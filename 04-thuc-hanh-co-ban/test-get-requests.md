# 4.1 Test GET Requests

GET là HTTP method cơ bản nhất. Hãy thực hành test GET requests với API thực tế!

## Chuẩn Bị

### API Để Thực Hành

Chúng ta sẽ sử dụng **JSONPlaceholder** - fake REST API miễn phí:
- **Base URL:** https://jsonplaceholder.typicode.com
- Không cần authentication
- Hoàn hảo để học

### Setup trong Postman

1. Mở Postman
2. Tạo Collection mới: "JSONPlaceholder Tests"
3. Tạo Environment mới: "JSONPlaceholder"
4. Thêm variable:
   ```
   base_url = https://jsonplaceholder.typicode.com
   ```

---

## Thực Hành 1: GET All Resources

### Mục Tiêu
Lấy danh sách tất cả users

### Steps

**1. Tạo Request Mới**
- Tên: "GET All Users"
- Method: GET
- URL: `{{base_url}}/users`

**2. Gửi Request**
- Click nút "Send"
- Xem response

**3. Expected Response**

Status: `200 OK`

Body:
```json
[
  {
    "id": 1,
    "name": "Leanne Graham",
    "username": "Bret",
    "email": "Sincere@april.biz",
    "address": {
      "street": "Kulas Light",
      "suite": "Apt. 556",
      "city": "Gwenborough",
      "zipcode": "92998-3874",
      "geo": {
        "lat": "-37.3159",
        "lng": "81.1496"
      }
    },
    "phone": "1-770-736-8031 x56442",
    "website": "hildegard.org",
    "company": {
      "name": "Romaguera-Crona",
      "catchPhrase": "Multi-layered client-server neural-net",
      "bs": "harness real-time e-markets"
    }
  },
  // ... 9 more users
]
```

### Verify Manually

✅ **Kiểm tra:**
- [ ] Status code là 200
- [ ] Response là array
- [ ] Có 10 users
- [ ] Mỗi user có id, name, email

---

## Thực Hành 2: GET Single Resource

### Mục Tiêu
Lấy thông tin 1 user cụ thể

### Steps

**1. Tạo Request**
- Tên: "GET User by ID"
- Method: GET
- URL: `{{base_url}}/users/1`

**2. Expected Response**

Status: `200 OK`

```json
{
  "id": 1,
  "name": "Leanne Graham",
  "username": "Bret",
  "email": "Sincere@april.biz",
  "address": {
    "street": "Kulas Light",
    "suite": "Apt. 556",
    "city": "Gwenborough",
    "zipcode": "92998-3874",
    "geo": {
      "lat": "-37.3159",
      "lng": "81.1496"
    }
  },
  "phone": "1-770-736-8031 x56442",
  "website": "hildegard.org",
  "company": {
    "name": "Romaguera-Crona",
    "catchPhrase": "Multi-layered client-server neural-net",
    "bs": "harness real-time e-markets"
  }
}
```

### Verify Manually

✅ **Kiểm tra:**
- [ ] Status code là 200
- [ ] Response là object (không phải array)
- [ ] id = 1
- [ ] name = "Leanne Graham"
- [ ] email có format đúng

---

## Thực Hành 3: GET với Query Parameters

### Mục Tiêu
Filter users theo query parameters

### 3.1 Filter Posts by userId

**Request:**
- Tên: "GET Posts by User"
- Method: GET
- URL: `{{base_url}}/posts?userId=1`

**Query Params trong Postman:**
```
KEY       | VALUE
----------|------
userId    | 1
```

**Expected:**
- Chỉ posts có userId = 1
- Array of 10 posts

### 3.2 Multiple Query Parameters

**Request:**
- URL: `{{base_url}}/comments?postId=1`

Lấy comments của post 1

---

## Thực Hành 4: Viết Tests

Bây giờ hãy thêm automated tests!

### Test 1: Status Code

```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

**Thêm test:**
1. Click tab "Tests" trong request
2. Copy đoạn code trên
3. Send request
4. Xem "Test Results" tab

✅ Nếu test pass → màu xanh!

### Test 2: Response is Array

```javascript
pm.test("Response is an array", function () {
    const response = pm.response.json();
    pm.expect(response).to.be.an('array');
});
```

### Test 3: Check Array Length

```javascript
pm.test("Returns 10 users", function () {
    const users = pm.response.json();
    pm.expect(users).to.have.lengthOf(10);
});
```

### Test 4: Verify Structure

```javascript
pm.test("First user has correct structure", function () {
    const users = pm.response.json();
    const firstUser = users[0];

    pm.expect(firstUser).to.have.property('id');
    pm.expect(firstUser).to.have.property('name');
    pm.expect(firstUser).to.have.property('email');
    pm.expect(firstUser).to.have.property('phone');
});
```

### Test 5: Verify Data Types

```javascript
pm.test("User data types are correct", function () {
    const user = pm.response.json()[0];

    pm.expect(user.id).to.be.a('number');
    pm.expect(user.name).to.be.a('string');
    pm.expect(user.email).to.be.a('string');
});
```

### Test 6: Email Format

```javascript
pm.test("Email has valid format", function () {
    const user = pm.response.json()[0];
    pm.expect(user.email).to.match(/^[^\s@]+@[^\s@]+\.[^\s@]+$/);
});
```

### Test 7: Response Time

```javascript
pm.test("Response time is less than 500ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(500);
});
```

### Complete Test Suite

Đặt tất cả tests vào tab "Tests":

```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Response is an array", function () {
    const response = pm.response.json();
    pm.expect(response).to.be.an('array');
});

pm.test("Returns 10 users", function () {
    const users = pm.response.json();
    pm.expect(users).to.have.lengthOf(10);
});

pm.test("Each user has required fields", function () {
    const users = pm.response.json();

    users.forEach(user => {
        pm.expect(user).to.have.property('id');
        pm.expect(user).to.have.property('name');
        pm.expect(user).to.have.property('email');
    });
});

pm.test("User IDs are unique", function () {
    const users = pm.response.json();
    const ids = users.map(u => u.id);
    const uniqueIds = [...new Set(ids)];

    pm.expect(ids.length).to.equal(uniqueIds.length);
});

pm.test("Response time is acceptable", function () {
    pm.expect(pm.response.responseTime).to.be.below(500);
});
```

---

## Thực Hành 5: Error Cases

### Test 404 Not Found

**Request:**
- Tên: "GET User - Not Found"
- Method: GET
- URL: `{{base_url}}/users/999999`

**Expected:**
- Status: `404 Not Found`
- Body: `{}`

**Tests:**
```javascript
pm.test("Status code is 404", function () {
    pm.response.to.have.status(404);
});

pm.test("Response is empty object", function () {
    const response = pm.response.json();
    pm.expect(Object.keys(response)).to.have.lengthOf(0);
});
```

---

## Thực Hành 6: Nested Resources

### GET Posts của User

**Request:**
- URL: `{{base_url}}/users/1/posts`

**Expected:**
- Array of posts
- Tất cả posts có userId = 1

**Tests:**
```javascript
pm.test("All posts belong to user 1", function () {
    const posts = pm.response.json();

    posts.forEach(post => {
        pm.expect(post.userId).to.equal(1);
    });
});
```

### GET Comments của Post

**Request:**
- URL: `{{base_url}}/posts/1/comments`

**Expected:**
- Array of comments
- Tất cả comments có postId = 1

---

## Bài Tập Thực Hành

### Bài 1: Albums (Dễ)

**Tasks:**
1. GET all albums: `/albums`
2. Verify có 100 albums
3. GET album by ID: `/albums/1`
4. Verify structure: id, userId, title

<details>
<summary><b>Giải pháp</b></summary>

```javascript
// GET /albums
pm.test("Returns 100 albums", function () {
    const albums = pm.response.json();
    pm.expect(albums).to.have.lengthOf(100);
});

// GET /albums/1
pm.test("Album has correct structure", function () {
    const album = pm.response.json();
    pm.expect(album).to.have.property('id');
    pm.expect(album).to.have.property('userId');
    pm.expect(album).to.have.property('title');
});
```
</details>

### Bài 2: Todos (Trung bình)

**Tasks:**
1. GET all todos
2. GET completed todos: `/todos?completed=true`
3. Verify tất cả có completed = true
4. Count số lượng completed todos

<details>
<summary><b>Giải pháp</b></summary>

```javascript
// GET /todos?completed=true
pm.test("All todos are completed", function () {
    const todos = pm.response.json();

    todos.forEach(todo => {
        pm.expect(todo.completed).to.be.true;
    });
});

pm.test("Count completed todos", function () {
    const todos = pm.response.json();
    console.log(`Completed todos: ${todos.length}`);
});
```
</details>

### Bài 3: Photos Pagination (Khó)

**Tasks:**
1. GET photos với pagination: `/photos?_page=1&_limit=10`
2. Verify có đúng 10 photos
3. GET page 2, verify khác page 1
4. Viết test để loop qua tất cả pages

<details>
<summary><b>Giải pháp</b></summary>

```javascript
// GET /photos?_page=1&_limit=10
pm.test("Returns exactly 10 photos", function () {
    const photos = pm.response.json();
    pm.expect(photos).to.have.lengthOf(10);
});

pm.test("First photo has required fields", function () {
    const photo = pm.response.json()[0];
    pm.expect(photo).to.have.property('id');
    pm.expect(photo).to.have.property('title');
    pm.expect(photo).to.have.property('url');
    pm.expect(photo).to.have.property('thumbnailUrl');
});

// Save first photo ID for comparison
pm.environment.set("page1_firstId", pm.response.json()[0].id);

// In page 2 request, verify different:
pm.test("Page 2 has different photos", function () {
    const page1FirstId = pm.environment.get("page1_firstId");
    const page2FirstId = pm.response.json()[0].id;
    pm.expect(page2FirstId).to.not.equal(page1FirstId);
});
```
</details>

---

## Tips và Best Practices

### 1. Organize Tests Logically

```javascript
// 1. Status checks
pm.test("Status code is 200", ...);

// 2. Structure checks
pm.test("Response is array", ...);

// 3. Data validation
pm.test("Data types correct", ...);

// 4. Business logic
pm.test("User IDs unique", ...);

// 5. Performance
pm.test("Response time OK", ...);
```

### 2. Descriptive Test Names

✅ **TỐT:**
```javascript
pm.test("Returns 10 users with valid email formats", ...);
```

❌ **TỆ:**
```javascript
pm.test("Test 1", ...);
```

### 3. Console Logging

```javascript
const response = pm.response.json();
console.log("Total users:", response.length);
console.log("First user:", response[0].name);
```

View logs: View → Show Postman Console

### 4. Save Important Data

```javascript
// Save to environment
const userId = pm.response.json()[0].id;
pm.environment.set("userId", userId);

// Use in next request
// URL: {{base_url}}/users/{{userId}}/posts
```

---

## Checklist

Hoàn thành Thực Hành 4.1 khi bạn có thể:

- [ ] GET all resources và verify response
- [ ] GET single resource by ID
- [ ] GET với query parameters
- [ ] Viết tests cho status code
- [ ] Viết tests cho response structure
- [ ] Viết tests cho data types
- [ ] Test error cases (404)
- [ ] Test nested resources
- [ ] Hoàn thành 3 bài tập

---

## Tổng Kết

Trong bài này bạn đã học:
- ✅ Gửi GET requests với Postman
- ✅ Verify responses manually
- ✅ Viết automated tests
- ✅ Test với query parameters
- ✅ Handle error cases
- ✅ Test nested resources

## Tiếp Theo

Bây giờ bạn đã thành thạo GET, hãy học [Test POST Requests](./test-post-requests.md)

---

[⬅️ Chương 4](./README.md) | [Tiếp Theo: Test POST Requests ➡️](./test-post-requests.md)
