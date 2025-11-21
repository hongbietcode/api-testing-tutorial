# 2.1 REST API

## REST Là Gì?

**REST** = **RE**presentational **S**tate **T**ransfer

REST là một kiến trúc thiết kế API, được phát triển bởi Roy Fielding năm 2000. Đây là kiểu API phổ biến nhất hiện nay.

**Đơn giản:**
- REST API sử dụng HTTP để giao tiếp
- Dữ liệu được tổ chức thành "resources" (tài nguyên)
- Mỗi resource có một URL duy nhất
- Sử dụng HTTP methods (GET, POST, PUT, DELETE) để thao tác

## 6 Nguyên Tắc RESTful

### 1. Client-Server Architecture

Client và Server tách biệt, độc lập:
- **Client**: Xử lý UI, user experience
- **Server**: Xử lý data, business logic

**Lợi ích:**
- Phát triển độc lập
- Scale riêng biệt
- Một server phục vụ nhiều clients (web, mobile, IoT)

### 2. Stateless (Không trạng thái)

Server KHÔNG lưu trạng thái của client giữa các requests.

**Mỗi request phải chứa tất cả thông tin cần thiết:**

❌ **Stateful (KHÔNG phải REST):**
```
Request 1: Login (username, password)
Server lưu session

Request 2: Get profile (không cần gửi gì)
Server biết bạn là ai từ session
```

✅ **Stateless (REST):**
```
Request 1: Login (username, password)
Server trả về token

Request 2: Get profile + token
Server xác thực từ token trong request
```

**Lợi ích:**
- Server đơn giản hơn
- Dễ scale (bất kỳ server nào cũng xử lý được request)
- Dễ cache

### 3. Cacheable (Có thể cache)

Responses phải chỉ rõ có thể cache hay không.

**Ví dụ:**
```
GET /users/1

Response Headers:
Cache-Control: max-age=3600  ← Cache trong 1 giờ
```

**Lợi ích:**
- Giảm tải server
- Tăng tốc độ
- Tiết kiệm bandwidth

### 4. Uniform Interface (Giao diện thống nhất)

API phải tuân theo các quy tắc nhất quán:

**a) Resource-based (Dựa trên tài nguyên)**

Mọi thứ là resource với URL riêng:
```
/users          ← Collection of users
/users/123      ← Specific user
/users/123/posts ← User's posts
```

**b) HTTP Methods có ý nghĩa rõ ràng**

```
GET     /users      → Lấy danh sách users
POST    /users      → Tạo user mới
GET     /users/123  → Lấy user ID 123
PUT     /users/123  → Update toàn bộ user 123
PATCH   /users/123  → Update một phần user 123
DELETE  /users/123  → Xóa user 123
```

**c) Self-descriptive messages**

Response chứa đủ thông tin để client hiểu:
```json
{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "links": {
    "self": "/users/123",
    "posts": "/users/123/posts"
  }
}
```

### 5. Layered System

Client không biết nó đang kết nối trực tiếp đến server hay qua intermediary.

```
Client → Load Balancer → API Gateway → Server → Database
```

**Lợi ích:**
- Security layers
- Load balancing
- Caching layers
- Proxy servers

### 6. Code on Demand (Tùy chọn)

Server có thể gửi code (JavaScript) cho client thực thi.

Không phổ biến lắm trong REST APIs.

## Resources (Tài Nguyên)

REST xoay quanh khái niệm "resources".

### Resource Là Gì?

Bất cứ thứ gì có thể được đặt tên và thao tác:
- User
- Product
- Order
- Blog Post
- Photo

### Resource Identifier (URI/URL)

Mỗi resource có một URL duy nhất:

```
https://api.example.com/users/123
└──────┬───────────┘ └───┬────┘ └┬┘
    Base URL          Resource  ID
```

### Collection vs Single Resource

**Collection** - Nhóm resources:
```
GET /users          ← Tất cả users
GET /products       ← Tất cả products
GET /orders         ← Tất cả orders
```

**Single Resource** - Một resource cụ thể:
```
GET /users/123      ← User ID 123
GET /products/456   ← Product ID 456
GET /orders/789     ← Order ID 789
```

### Nested Resources

Resources có thể lồng nhau:

```
GET /users/123/posts          ← Posts của user 123
GET /users/123/posts/456      ← Post 456 của user 123
GET /posts/456/comments       ← Comments của post 456
GET /products/789/reviews     ← Reviews của product 789
```

## RESTful Design Best Practices

### 1. Sử Dụng Danh Từ, Không Dùng Động Từ

✅ **TỐT:**
```
GET    /users
POST   /users
GET    /users/123
DELETE /users/123
```

❌ **TỆ:**
```
GET    /getUsers
POST   /createUser
GET    /getUserById/123
POST   /deleteUser/123
```

### 2. Dùng Số Nhiều (Plural)

✅ **TỐT:**
```
/users
/products
/orders
```

❌ **TỆ:**
```
/user
/product
/order
```

### 3. Sử Dụng HTTP Methods Đúng

| Method | Ý Nghĩa | Idempotent? |
|--------|---------|-------------|
| GET | Lấy dữ liệu | ✅ Yes |
| POST | Tạo mới | ❌ No |
| PUT | Update toàn bộ | ✅ Yes |
| PATCH | Update một phần | ❌ No (thường) |
| DELETE | Xóa | ✅ Yes |

**Idempotent:** Gọi nhiều lần, kết quả giống nhau

### 4. Sử Dụng HTTP Status Codes Đúng

```
200 OK              - GET, PUT, PATCH thành công
201 Created         - POST tạo resource mới
204 No Content      - DELETE thành công
400 Bad Request     - Request sai format
401 Unauthorized    - Chưa đăng nhập
403 Forbidden       - Không có quyền
404 Not Found       - Resource không tồn tại
500 Server Error    - Lỗi server
```

### 5. Filtering, Sorting, Pagination

**Filtering:**
```
GET /users?status=active
GET /products?category=electronics
GET /orders?date=2024-01-01
```

**Sorting:**
```
GET /users?sort=name
GET /products?sort=-price    ← "-" = descending
```

**Pagination:**
```
GET /users?page=1&limit=10
GET /users?offset=20&limit=10
```

### 6. Versioning

```
https://api.example.com/v1/users
https://api.example.com/v2/users
```

Hoặc qua header:
```
Accept: application/vnd.api+json; version=1
```

## Ví Dụ Thực Tế: Blog API

### Resources

- Users (người dùng)
- Posts (bài viết)
- Comments (bình luận)

### Endpoints

**Users:**
```
GET    /users              - Danh sách users
GET    /users/123          - User detail
POST   /users              - Tạo user mới
PUT    /users/123          - Update user
DELETE /users/123          - Xóa user
GET    /users/123/posts    - Posts của user 123
```

**Posts:**
```
GET    /posts              - Danh sách posts
GET    /posts/456          - Post detail
POST   /posts              - Tạo post mới
PUT    /posts/456          - Update post
DELETE /posts/456          - Xóa post
GET    /posts/456/comments - Comments của post 456
```

**Comments:**
```
GET    /comments           - Danh sách comments
GET    /comments/789       - Comment detail
POST   /comments           - Tạo comment mới
PUT    /comments/789       - Update comment
DELETE /comments/789       - Xóa comment
```

### Request/Response Examples

**1. Lấy danh sách posts:**
```
GET /posts?page=1&limit=10

Response 200 OK:
{
  "data": [
    {
      "id": 1,
      "title": "First Post",
      "body": "Content...",
      "userId": 123
    }
  ],
  "meta": {
    "page": 1,
    "limit": 10,
    "total": 100
  }
}
```

**2. Tạo post mới:**
```
POST /posts
Content-Type: application/json

{
  "title": "My New Post",
  "body": "This is the content",
  "userId": 123
}

Response 201 Created:
{
  "id": 456,
  "title": "My New Post",
  "body": "This is the content",
  "userId": 123,
  "createdAt": "2024-01-21T10:00:00Z"
}
```

**3. Update post:**
```
PUT /posts/456
Content-Type: application/json

{
  "title": "Updated Title",
  "body": "Updated content",
  "userId": 123
}

Response 200 OK:
{
  "id": 456,
  "title": "Updated Title",
  "body": "Updated content",
  "userId": 123,
  "updatedAt": "2024-01-21T11:00:00Z"
}
```

**4. Xóa post:**
```
DELETE /posts/456

Response 204 No Content
(Không có body)
```

## REST vs SOAP vs GraphQL

### REST
- ✅ Đơn giản, dễ học
- ✅ Phổ biến nhất
- ✅ Flexible
- ❌ Over-fetching/Under-fetching data

### SOAP
- ✅ Bảo mật cao
- ✅ Standards rõ ràng
- ❌ Phức tạp
- ❌ XML verbose

### GraphQL
- ✅ Client quyết định data cần lấy
- ✅ Một endpoint cho tất cả
- ❌ Phức tạp hơn REST
- ❌ Caching khó hơn

## Kiểm Tra Hiểu Biết

1. REST là gì? Giải thích bằng ngôn ngữ của bạn.
2. 6 nguyên tắc RESTful là gì?
3. Stateless có nghĩa là gì?
4. Resource là gì? Cho ví dụ.
5. Phân biệt Collection và Single Resource.
6. Tại sao nên dùng danh từ số nhiều trong URL?
7. GET /users và POST /users khác nhau như thế nào?

<details>
<summary><b>Đáp án</b></summary>

1. **REST là gì?**
   - Kiến trúc API sử dụng HTTP, tổ chức dữ liệu thành resources, mỗi resource có URL riêng.

2. **6 nguyên tắc:**
   - Client-Server, Stateless, Cacheable, Uniform Interface, Layered System, Code on Demand

3. **Stateless:**
   - Server không lưu trạng thái client. Mỗi request chứa tất cả info cần thiết.

4. **Resource:**
   - Bất cứ thứ gì có thể đặt tên và thao tác. VD: User, Product, Order

5. **Collection vs Single:**
   - Collection: Nhóm resources (/users)
   - Single: Một resource cụ thể (/users/123)

6. **Danh từ số nhiều:**
   - Nhất quán, dễ hiểu. /users cho cả list và single.

7. **GET vs POST:**
   - GET /users: Lấy danh sách users
   - POST /users: Tạo user mới
</details>

## Tổng Kết

- ✅ REST là kiến trúc API phổ biến nhất
- ✅ Sử dụng HTTP methods và URLs
- ✅ Stateless, cacheable
- ✅ Resources với URLs duy nhất
- ✅ Best practices: danh từ số nhiều, HTTP methods đúng, status codes đúng

## Tiếp Theo

Bây giờ bạn đã hiểu REST API, hãy tìm hiểu chi tiết về [HTTP Methods](./http-methods.md)

---

[⬅️ Chương 2](./README.md) | [Tiếp Theo: HTTP Methods ➡️](./http-methods.md)
