# 3.2 Gửi Request Đầu Tiên

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Tạo được request mới trong Postman
- ✅ Chọn đúng HTTP method
- ✅ Gửi request và đọc response
- ✅ Hiểu cấu trúc request/response cơ bản
- ✅ Lưu request để sử dụng lại

## 1. Tạo Request Mới

### Cách 1: Từ New Button

1. Click nút **New** (hoặc **+** tab mới)
2. Chọn **HTTP Request**
3. Một tab mới sẽ mở với request builder

### Cách 2: Từ Keyboard

- Windows/Linux: `Ctrl + N`
- macOS: `Cmd + N`

### Cách 3: Từ Collection

1. Hover vào collection
2. Click icon **"..."** (more actions)
3. Chọn **Add Request**

## 2. Các Thành Phần Của Request

Một HTTP request cơ bản gồm:

```
┌────────────────────────────────────────────────────┐
│ [METHOD ▼]  [URL]                         [Send]   │
└────────────────────────────────────────────────────┘
  │           │                               │
  │           │                               └─ 3. Gửi request
  │           └─ 2. Endpoint URL
  └─ 1. HTTP Method
```

### 2.1 HTTP Method

Chọn method phù hợp với action:

| Method | Mục đích | Ví dụ |
|--------|----------|-------|
| **GET** | Lấy dữ liệu | Xem danh sách users |
| **POST** | Tạo mới | Tạo user mới |
| **PUT** | Cập nhật toàn bộ | Update toàn bộ thông tin user |
| **PATCH** | Cập nhật một phần | Chỉ update email của user |
| **DELETE** | Xóa | Xóa user |

### 2.2 URL (Endpoint)

Cấu trúc URL đầy đủ:

```
https://api.example.com/v1/users?page=1&limit=10
│      │                   │      │
│      │                   │      └─ Query parameters
│      │                   └─ Path
│      └─ Domain
└─ Protocol
```

## 3. Gửi Request Đầu Tiên

Hãy thực hành với **JSONPlaceholder** - một API miễn phí để test.

### Bước 1: Tạo Request Mới

1. Click **New** → **HTTP Request**
2. Hoặc nhấn `Ctrl/Cmd + N`

### Bước 2: Cấu Hình Request

**Method**: Chọn `GET` (mặc định)
**URL**: `https://jsonplaceholder.typicode.com/users`

### Bước 3: Gửi Request

Click nút **Send** (hoặc `Ctrl/Cmd + Enter`)

### Bước 4: Xem Response

Bạn sẽ thấy kết quả tương tự:

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
  // ... more users
]
```

## 4. Đọc Response

### 4.1 Status Line

```
Status: 200 OK | Time: 234 ms | Size: 5.2 KB
```

**Giải thích:**
- **200 OK**: Request thành công (xem [HTTP Status Codes](../02-kien-thuc-co-ban/http-status-codes.md))
- **234 ms**: Thời gian phản hồi
- **5.2 KB**: Kích thước response

### 4.2 Response Body

Có nhiều định dạng hiển thị:

**Pretty** (Đẹp, dễ đọc)
```json
{
  "id": 1,
  "name": "Leanne Graham"
}
```

**Raw** (Dữ liệu thô)
```
{"id":1,"name":"Leanne Graham"}
```

**Preview** (Xem trước HTML)
- Hữu ích khi API trả về HTML

### 4.3 Response Headers

Click tab **Headers** để xem:

```
Content-Type: application/json; charset=utf-8
Date: Mon, 01 Jan 2024 10:00:00 GMT
Server: cloudflare
Cache-Control: max-age=43200
```

**Giải thích:**
- **Content-Type**: Loại dữ liệu (JSON, HTML, XML, etc.)
- **Date**: Thời gian server phản hồi
- **Server**: Loại server
- **Cache-Control**: Chính sách cache

## 5. Lưu Request

### Tại sao nên lưu?

- ✅ Sử dụng lại nhiều lần
- ✅ Tổ chức theo collections
- ✅ Chia sẻ với team
- ✅ Version control

### Cách lưu:

#### Bước 1: Click "Save" hoặc `Ctrl/Cmd + S`

#### Bước 2: Điền thông tin

```
Request name: Get All Users
Description: Lấy danh sách tất cả users từ JSONPlaceholder API
```

#### Bước 3: Chọn Collection

- Tạo collection mới: **+ Create Collection**
- Hoặc chọn collection có sẵn

#### Bước 4: Click "Save"

Request sẽ xuất hiện trong collection ở sidebar.

## 6. Thực Hành: Các Request Cơ Bản

Hãy thực hành với các requests sau:

### Request 1: Get All Users ✅

```
Method: GET
URL: https://jsonplaceholder.typicode.com/users
Mô tả: Lấy danh sách 10 users
Expected: 200 OK, array of users
```

### Request 2: Get User by ID

```
Method: GET
URL: https://jsonplaceholder.typicode.com/users/1
Mô tả: Lấy thông tin user có id=1
Expected: 200 OK, single user object
```

**Kết quả mong đợi:**
```json
{
  "id": 1,
  "name": "Leanne Graham",
  "username": "Bret",
  "email": "Sincere@april.biz"
}
```

### Request 3: Get All Posts

```
Method: GET
URL: https://jsonplaceholder.typicode.com/posts
Mô tả: Lấy danh sách tất cả posts
Expected: 200 OK, array of 100 posts
```

### Request 4: Get Posts by User

```
Method: GET
URL: https://jsonplaceholder.typicode.com/posts?userId=1
Mô tả: Lấy tất cả posts của user có id=1
Expected: 200 OK, array of posts
```

**Giải thích Query Parameter:**
- `?userId=1` là query parameter
- Filter posts theo userId
- Có thể có nhiều params: `?userId=1&_limit=5`

### Request 5: Get Comments of Post

```
Method: GET
URL: https://jsonplaceholder.typicode.com/posts/1/comments
Mô tả: Lấy comments của post có id=1
Expected: 200 OK, array of comments
```

## 7. Phân Tích Response

### Bài tập: Phân tích response của "Get User by ID"

#### Câu hỏi:
1. Status code là gì?
2. Response time bao nhiêu?
3. Content-Type header là gì?
4. User có bao nhiêu fields?
5. Địa chỉ email của user là gì?

#### Đáp án:
1. **200 OK**
2. Thường ~200-500ms (tùy network)
3. **application/json; charset=utf-8**
4. **8 fields**: id, name, username, email, address, phone, website, company
5. **Sincere@april.biz**

## 8. Xử Lý Errors

### Request lỗi: Get Non-existent User

```
Method: GET
URL: https://jsonplaceholder.typicode.com/users/999
```

**Kết quả:**
```
Status: 404 Not Found
Body: {}
```

**Giải thích:**
- **404**: Resource không tồn tại
- Body rỗng vì không tìm thấy user

### Common Errors

| Status | Ý nghĩa | Ví dụ |
|--------|---------|-------|
| 400 | Bad Request | URL sai format |
| 401 | Unauthorized | Thiếu authentication |
| 403 | Forbidden | Không có quyền truy cập |
| 404 | Not Found | Resource không tồn tại |
| 500 | Server Error | Lỗi server |

## 9. Tips và Tricks

### Tip 1: Duplicate Request

Để test với params khác nhau:
1. Right-click vào request
2. Chọn **Duplicate**
3. Sửa URL hoặc params

### Tip 2: Xem Raw Response

Trong response, click **Raw** để xem dữ liệu thô (hữu ích khi debug).

### Tip 3: Copy cURL

1. Click icon **Code** (</>) bên phải nút Send
2. Chọn **cURL**
3. Copy để dùng trong terminal

```bash
curl --location 'https://jsonplaceholder.typicode.com/users/1'
```

### Tip 4: Format JSON

Nếu response JSON không format đẹp, Postman tự động format khi chọn **Pretty**.

## 10. Bài Tập Thực Hành

### Bài tập 1: Khám phá JSONPlaceholder API

Gửi các requests sau và quan sát kết quả:

1. Get all albums: `GET /albums`
2. Get album by id: `GET /albums/1`
3. Get photos: `GET /photos`
4. Get todos: `GET /todos`
5. Get user's todos: `GET /todos?userId=1`

**Yêu cầu:**
- Lưu tất cả requests vào collection "JSONPlaceholder Tests"
- Đặt tên rõ ràng cho mỗi request
- Ghi chú expected status code

### Bài tập 2: Phân tích Response

Với request `GET /users/1`, trả lời:

1. User có bao nhiêu fields ở cấp đầu tiên?
2. Object `address` có những fields gì?
3. `geo` coordinates của user là gì?
4. Company name là gì?
5. Response headers có field `Cache-Control` không? Giá trị là gì?

### Bài tập 3: Test Error Cases

1. Request user không tồn tại: `GET /users/9999`
   - Expected status: ?
   - Response body: ?

2. Request với URL sai: `GET /userssss`
   - Expected status: ?
   - Response body: ?

## 11. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ Tạo và gửi request trong Postman
- ✅ Chọn HTTP method phù hợp
- ✅ Đọc và phân tích response
- ✅ Lưu request vào collection
- ✅ Xử lý errors cơ bản
- ✅ Sử dụng query parameters

## 12. Kiến Thức Cần Nhớ

| Khái niệm | Giải thích |
|-----------|------------|
| **Request** | Yêu cầu gửi đến server |
| **Response** | Phản hồi từ server |
| **Status Code** | Mã trạng thái (200, 404, 500, etc.) |
| **Headers** | Metadata của request/response |
| **Body** | Nội dung chính của request/response |
| **Query Params** | Parameters trong URL (?key=value) |

## Next Steps

Bây giờ bạn đã biết gửi request, hãy tiếp tục:
- **Bài tiếp theo**: [3.3 Collections - Tổ Chức Requests](./collections.md)

---

[⬅️ Cài Đặt Postman](./cai-dat-postman.md) | [Tổng Quan Chương 3](./README.md) | [Tiếp Theo: Collections ➡️](./collections.md)
