# 3.3 Collections - Tổ Chức Requests

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Hiểu Collections là gì và tại sao cần dùng
- ✅ Tạo và quản lý collections
- ✅ Tổ chức requests bằng folders
- ✅ Export/Import collections
- ✅ Chia sẻ collections với team

## 1. Collection Là Gì?

**Collection** là một nhóm các requests được tổ chức lại với nhau.

### Tại Sao Cần Collections?

❌ **Không có Collection:**
```
- Get User 1
- Create Post
- Get User 2
- Delete Post
- Update User
- Get Posts
... (rất lộn xộn)
```

✅ **Có Collection:**
```
📁 User Management API
  📁 Users
    - Get All Users
    - Get User by ID
    - Create User
    - Update User
    - Delete User
  📁 Posts
    - Get All Posts
    - Get Post by ID
    - Create Post
    - Update Post
    - Delete Post
```

### Lợi Ích

- ✅ **Tổ chức**: Nhóm requests theo feature hoặc endpoint
- ✅ **Tái sử dụng**: Dễ tìm và sử dụng lại
- ✅ **Chia sẻ**: Export và chia sẻ với team
- ✅ **Documentation**: Tự động generate API docs
- ✅ **Testing**: Chạy toàn bộ collection với Collection Runner
- ✅ **CI/CD**: Integrate với Newman để test tự động

## 2. Tạo Collection Mới

### Cách 1: Từ Sidebar

1. Click vào **Collections** tab
2. Click nút **"+"** hoặc **New Collection**
3. Đặt tên collection
4. Click **Create**

### Cách 2: Từ New Button

1. Click **New** → **Collection**
2. Điền thông tin:
   - **Name**: Tên collection (VD: "JSONPlaceholder API")
   - **Description**: Mô tả ngắn gọn
3. Click **Create**

### Cách 3: Khi Save Request

1. Khi save request lần đầu
2. Chọn **+ Create Collection**
3. Đặt tên và save

## 3. Thêm Requests Vào Collection

### Cách 1: Save Request Mới

1. Tạo request mới
2. Click **Save** (`Ctrl/Cmd + S`)
3. Chọn collection
4. Đặt tên request
5. Click **Save**

### Cách 2: Kéo Thả (Drag & Drop)

1. Kéo request từ History
2. Thả vào collection ở sidebar

### Cách 3: Add Request Trực Tiếp

1. Hover vào collection
2. Click **"..."** → **Add Request**
3. Tạo request mới ngay trong collection

## 4. Tổ Chức Với Folders

### Tạo Folder

1. Hover vào collection
2. Click **"..."** → **Add Folder**
3. Đặt tên folder (VD: "Users", "Posts")
4. Click **Create**

### Cấu Trúc Đề Xuất

#### Cách 1: Theo Resources
```
📁 API Testing Collection
  📁 Users
    - GET All Users
    - GET User by ID
    - POST Create User
    - PUT Update User
    - DELETE User
  📁 Posts
    - GET All Posts
    - GET Post by ID
    - POST Create Post
  📁 Comments
    - GET All Comments
    - POST Create Comment
```

#### Cách 2: Theo HTTP Methods
```
📁 API Testing Collection
  📁 GET Requests
    - Get Users
    - Get Posts
    - Get Comments
  📁 POST Requests
    - Create User
    - Create Post
  📁 PUT/PATCH Requests
    - Update User
    - Update Post
  📁 DELETE Requests
    - Delete User
    - Delete Post
```

#### Cách 3: Theo User Flows
```
📁 E-commerce API
  📁 User Registration Flow
    - Register User
    - Verify Email
    - Login
  📁 Shopping Flow
    - Browse Products
    - Add to Cart
    - Checkout
    - Payment
  📁 Order Management
    - Get Orders
    - Get Order Details
    - Cancel Order
```

### Best Practice: Đặt Tên

✅ **Tốt:**
- `GET All Users`
- `POST Create User`
- `PUT Update User by ID`
- `DELETE Remove User`

❌ **Không tốt:**
- `Request 1`
- `Test`
- `API Call`
- `users`

## 5. Collection Variables

### Tại Sao Cần Variables?

Thay vì lặp lại base URL:
```
❌ https://jsonplaceholder.typicode.com/users
❌ https://jsonplaceholder.typicode.com/posts
❌ https://jsonplaceholder.typicode.com/comments
```

Dùng variable:
```
✅ {{baseUrl}}/users
✅ {{baseUrl}}/posts
✅ {{baseUrl}}/comments
```

### Tạo Collection Variable

1. Click vào collection
2. Tab **Variables**
3. Thêm variable:
   - **Variable**: `baseUrl`
   - **Initial Value**: `https://jsonplaceholder.typicode.com`
   - **Current Value**: `https://jsonplaceholder.typicode.com`
4. Click **Save**

### Sử Dụng Variable

Trong URL bar:
```
{{baseUrl}}/users
{{baseUrl}}/posts/{{postId}}
```

Postman sẽ tự thay thế:
```
https://jsonplaceholder.typicode.com/users
https://jsonplaceholder.typicode.com/posts/1
```

## 6. Collection Description và Documentation

### Thêm Description

1. Click vào collection
2. Tab **Overview**
3. Thêm description (hỗ trợ Markdown):

```markdown
# JSONPlaceholder API Collection

## Mô tả
Collection này chứa các requests để test JSONPlaceholder API.

## Base URL
https://jsonplaceholder.typicode.com

## Resources
- Users: /users
- Posts: /posts
- Comments: /comments
- Albums: /albums
- Photos: /photos
- Todos: /todos

## Authentication
Không cần authentication

## Author
[Tên của bạn]
```

### Thêm Documentation Cho Request

1. Click vào request
2. Click icon **Documentation** (📄)
3. Thêm mô tả, examples, tests

## 7. Export Collection

### Tại Sao Export?

- ✅ Backup collection
- ✅ Chia sẻ với team
- ✅ Version control (Git)
- ✅ Import vào môi trường khác

### Cách Export

1. Hover vào collection
2. Click **"..."** → **Export**
3. Chọn version:
   - **Collection v2.1** (recommended)
   - Collection v2
4. Click **Export**
5. Chọn nơi lưu file `.json`

### Exported File

File JSON chứa:
- Tất cả requests
- Folders
- Variables
- Tests
- Scripts

```json
{
  "info": {
    "name": "JSONPlaceholder API",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "item": [
    {
      "name": "Users",
      "item": [
        {
          "name": "Get All Users",
          "request": {
            "method": "GET",
            "url": "{{baseUrl}}/users"
          }
        }
      ]
    }
  ]
}
```

## 8. Import Collection

### Từ File

1. Click **Import** button
2. Tab **File**
3. Click **Choose Files**
4. Chọn file `.json`
5. Click **Import**

### Từ Link

1. Click **Import**
2. Tab **Link**
3. Paste URL của collection (VD: GitHub raw URL)
4. Click **Continue** → **Import**

### Từ Text

1. Click **Import**
2. Tab **Raw text**
3. Paste nội dung JSON
4. Click **Continue** → **Import**

### Từ Code Repository

1. Click **Import**
2. Tab **Code repository**
3. Connect với GitHub/GitLab/Bitbucket
4. Chọn repository và file
5. Click **Import**

## 9. Chia Sẻ Collection

### Cách 1: Export File

1. Export collection thành `.json`
2. Gửi file qua email, Slack, etc.
3. Người khác import vào Postman của họ

### Cách 2: Share Link (Cần Postman Account)

1. Hover vào collection
2. Click **"..."** → **Share**
3. Click **Get Link**
4. Chọn permission:
   - **View**: Chỉ xem
   - **Edit**: Có thể chỉnh sửa
5. Copy link và gửi

### Cách 3: Workspace (Team)

1. Tạo Team Workspace
2. Invite team members
3. Tất cả collections trong workspace sẽ sync tự động

### Cách 4: Publish Documentation

1. Click vào collection
2. Click **"..."** → **Publish Docs**
3. Customize documentation
4. Click **Publish**
5. Share public URL

## 10. Collection Runner

Chạy tất cả requests trong collection một lượt.

### Cách sử dụng:

1. Click vào collection
2. Click **Run** (▶️) button
3. Chọn requests muốn chạy
4. Cấu hình:
   - **Iterations**: Số lần chạy
   - **Delay**: Delay giữa các requests (ms)
   - **Data file**: CSV/JSON data
5. Click **Run Collection**

### Kết quả:

```
✅ Get All Users - 200 OK (234ms)
✅ Get User by ID - 200 OK (156ms)
✅ Get All Posts - 200 OK (289ms)
❌ Get Invalid User - Failed (404)

3 passed, 1 failed
```

## 11. Thực Hành: Tạo Collection

### Bài tập 1: Tạo JSONPlaceholder Collection

**Yêu cầu:**
1. Tạo collection tên "JSONPlaceholder API Tests"
2. Thêm description và author
3. Tạo collection variable `baseUrl`
4. Tạo 3 folders: Users, Posts, Comments
5. Thêm requests:

**Folder Users:**
- GET All Users: `{{baseUrl}}/users`
- GET User by ID: `{{baseUrl}}/users/1`
- GET User's Posts: `{{baseUrl}}/users/1/posts`
- GET User's Albums: `{{baseUrl}}/users/1/albums`

**Folder Posts:**
- GET All Posts: `{{baseUrl}}/posts`
- GET Post by ID: `{{baseUrl}}/posts/1`
- GET Post's Comments: `{{baseUrl}}/posts/1/comments`

**Folder Comments:**
- GET All Comments: `{{baseUrl}}/comments`
- GET Comments by Post: `{{baseUrl}}/comments?postId=1`

6. Export collection thành file JSON

### Bài tập 2: Test Collection Organization

1. Tạo collection "API Testing Practice"
2. Tạo cấu trúc:
```
📁 API Testing Practice
  📁 Happy Path Tests
    - Các requests success cases
  📁 Error Handling Tests
    - Các requests error cases (404, 400, etc.)
  📁 Performance Tests
    - Các requests để test response time
```

3. Thêm ít nhất 2 requests vào mỗi folder

### Bài tập 3: Import Public Collection

1. Import collection này (paste vào Import → Link):
```
https://www.postman.com/postman/workspace/postman-api/collection/12345678-abcd-efgh
```
_(Hoặc search "Postman API" trong Postman public workspace)_

2. Explore collection structure
3. Chạy một số requests
4. Duplicate collection và customize

## 12. Best Practices

### ✅ DO

- Đặt tên rõ ràng, có ý nghĩa
- Dùng folders để tổ chức
- Thêm description cho collection
- Dùng variables thay vì hardcode URLs
- Export định kỳ để backup
- Version control collections với Git

### ❌ DON'T

- Tạo collection quá lớn (>100 requests)
- Đặt tên mơ hồ: "Test", "Request 1"
- Hardcode URLs, tokens, passwords
- Lưu sensitive data trong collection
- Quên document API changes

## 13. Tips và Tricks

### Tip 1: Duplicate Collection

Test với môi trường khác:
1. Right-click collection
2. **Duplicate**
3. Rename: "API Tests - Staging"

### Tip 2: Search trong Collection

- Nhấn `Ctrl/Cmd + K`
- Gõ tên request hoặc endpoint
- Enter để mở

### Tip 3: Reorder Requests

- Kéo thả requests để sắp xếp
- Đặt requests theo logic flow

### Tip 4: Color Code Folders

Postman không hỗ trợ màu sắc, nhưng bạn có thể dùng emoji:
```
📁 ✅ Completed Tests
📁 🚧 Work In Progress
📁 ❌ Failing Tests
📁 📚 Documentation
```

## 14. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ Collection là gì và tại sao cần dùng
- ✅ Tạo và quản lý collections
- ✅ Tổ chức requests bằng folders
- ✅ Sử dụng collection variables
- ✅ Export/Import collections
- ✅ Chia sẻ collections
- ✅ Chạy collection với Runner

## Next Steps

Bây giờ bạn đã biết tổ chức requests, hãy tiếp tục:
- **Bài tiếp theo**: [3.4 Environments và Variables](./environments-variables.md)

---

[⬅️ Gửi Request Đầu Tiên](./gui-request-dau-tien.md) | [Tổng Quan Chương 3](./README.md) | [Tiếp Theo: Environments & Variables ➡️](./environments-variables.md)
