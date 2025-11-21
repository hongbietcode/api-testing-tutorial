# Chương 4: Thực Hành Cơ Bản

Đây là chương quan trọng nhất! Bạn sẽ thực hành test API thực tế với các HTTP methods khác nhau và làm nhiều bài tập.

## Mục Tiêu Học Tập

Sau khi hoàn thành chương này, bạn sẽ:

- ✅ Test GET requests thành thạo
- ✅ Test POST requests để tạo dữ liệu
- ✅ Test PUT/PATCH để cập nhật dữ liệu
- ✅ Test DELETE để xóa dữ liệu
- ✅ Xử lý các error cases
- ✅ Verify responses chính xác

## Nội Dung Chương

### 4.1 Test GET Requests
- GET all resources
- GET single resource by ID
- GET with query parameters
- GET with filters và pagination
- Verify response data
- **Thực hành**: Test với JSONPlaceholder users API

### 4.2 Test POST Requests
- Tạo resource mới
- Gửi JSON body
- Verify status code 201
- Verify response trả về resource mới tạo
- Test với dữ liệu không hợp lệ
- **Thực hành**: Tạo user, post, todo mới

### 4.3 Test PUT và PATCH
- PUT - Cập nhật toàn bộ resource
- PATCH - Cập nhật một phần
- Phân biệt PUT vs PATCH
- Verify data được update
- **Thực hành**: Update user info

### 4.4 Test DELETE Requests
- Xóa resource
- Verify status code 200/204
- Verify resource không còn tồn tại
- Test xóa resource không tồn tại (404)
- **Thực hành**: Delete operations

### 4.5 Bài Tập Thực Hành Tổng Hợp

## Public APIs Để Thực Hành

### 1. JSONPlaceholder (Khuyên dùng cho người mới)
- URL: https://jsonplaceholder.typicode.com
- Miễn phí, không cần auth
- Có users, posts, comments, todos, albums, photos

### 2. ReqRes
- URL: https://reqres.in
- Fake user data API
- Trả về responses thực tế

### 3. HTTPBin
- URL: https://httpbin.org
- HTTP testing service
- Test mọi loại requests

## Bài Tập Thực Hành

### Bài 1: CRUD Users (Dễ)
Sử dụng: https://jsonplaceholder.typicode.com

1. GET all users → verify có 10 users
2. GET user ID=1 → verify name="Leanne Graham"
3. POST tạo user mới → verify status 201
4. PUT update user → verify data thay đổi
5. DELETE user → verify status 200

### Bài 2: Posts và Comments (Trung bình)
1. GET all posts
2. GET posts của user cụ thể (userId=1)
3. GET comments của post cụ thể
4. POST tạo post mới
5. POST tạo comment cho post

### Bài 3: Todos với Query Parameters (Trung bình)
1. GET all todos
2. GET todos completed=true
3. GET todos của userId=1
4. POST tạo todo mới
5. PATCH update todo status

### Bài 4: Error Handling (Khó)
1. GET user không tồn tại → expect 404
2. POST với body rỗng → expect 400/422
3. PUT với ID không hợp lệ → expect 404
4. DELETE resource đã xóa → expect 404

## Test Scenarios Quan Trọng

### Happy Path (Đường đi đúng)
- Input hợp lệ
- Expected: Success (200, 201, 204)

### Negative Tests (Dữ liệu sai)
- Missing required fields
- Invalid data types
- Out of range values
- Expected: Error (400, 422)

### Boundary Tests (Giới hạn)
- Min/Max values
- Empty strings
- Null values
- Very long strings

### Not Found Tests
- Resource không tồn tại
- Expected: 404

## Tips

- ✅ Luôn kiểm tra status code trước
- ✅ Verify response body structure
- ✅ Check data types
- ✅ Test cả happy path và error cases
- ✅ Lưu requests vào collections
- ✅ Sử dụng environments cho base URLs

## Thời Gian Học

**Ước tính: 6-8 giờ**
- Học lý thuyết: 2 giờ
- Thực hành: 4-6 giờ

---

[⬅️ Chương 3](../03-postman-co-ban/README.md) | [Về Trang Chủ](../README.md) | [Tiếp Theo: Chương 5 ➡️](../05-xac-thuc-authorization/README.md)
