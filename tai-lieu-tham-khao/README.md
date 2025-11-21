# Tài Liệu Tham Khảo

Tổng hợp các tài nguyên hữu ích để bạn tiếp tục học và phát triển kỹ năng API testing.

## Mục Lục

- [Glossary - Bảng Thuật Ngữ](#glossary)
- [HTTP Status Codes Reference](#http-status-codes)
- [HTTP Headers Reference](#http-headers)
- [Public APIs để Thực Hành](#public-apis)
- [Tools và Extensions](#tools)
- [Tài Nguyên Học Tập](#learning-resources)
- [Cộng Đồng](#community)
- [Books](#books)

---

## Glossary

### A

**API (Application Programming Interface)**
- Giao diện lập trình ứng dụng
- Cầu nối giữa các ứng dụng

**API Key**
- Mã định danh để xác thực API requests
- Thường gửi qua header hoặc query parameter

**Authentication (Xác thực)**
- Quá trình xác minh danh tính
- "Bạn là ai?"

**Authorization (Phân quyền)**
- Kiểm tra quyền truy cập
- "Bạn có quyền làm gì?"

### B

**Base URL**
- URL gốc của API
- Ví dụ: `https://api.example.com`

**Bearer Token**
- Token xác thực gửi trong Authorization header
- Format: `Bearer {token}`

**Body (Request/Response)**
- Nội dung chính của request hoặc response
- Thường ở dạng JSON hoặc XML

### C

**Client**
- Ứng dụng gọi API
- Có thể là web app, mobile app, hoặc service khác

**Collection (Postman)**
- Nhóm các API requests
- Giúp tổ chức và chia sẻ

**CORS (Cross-Origin Resource Sharing)**
- Cơ chế bảo mật cho web browsers
- Kiểm soát cross-domain requests

**CRUD**
- Create, Read, Update, Delete
- 4 operations cơ bản

### D

**DELETE**
- HTTP method để xóa resource
- Thường trả về 200 hoặc 204

### E

**Endpoint**
- URL cụ thể của một API function
- Ví dụ: `/api/users/123`

**Environment (Postman)**
- Tập hợp variables cho một môi trường
- Dev, Staging, Production

### G

**GET**
- HTTP method để lấy dữ liệu
- Idempotent (gọi nhiều lần, kết quả giống nhau)

### H

**Header**
- Metadata của request hoặc response
- Content-Type, Authorization, Accept...

**HTTP (HyperText Transfer Protocol)**
- Giao thức truyền tải dữ liệu trên web

**HTTPS**
- HTTP Secure - phiên bản mã hóa của HTTP

### I

**Idempotent**
- Operation có thể gọi nhiều lần mà kết quả không thay đổi
- GET, PUT, DELETE là idempotent
- POST không phải idempotent

### J

**JSON (JavaScript Object Notation)**
- Format dữ liệu phổ biến nhất cho APIs
- Dễ đọc, dễ parse

**JWT (JSON Web Token)**
- Token format cho authentication
- Self-contained, chứa user info

### M

**Method**
- Loại hành động HTTP: GET, POST, PUT, DELETE, PATCH

**Mock Server**
- Server giả lập để test
- Không cần backend thật

### N

**Newman**
- Postman command-line tool
- Dùng cho automation và CI/CD

### P

**PATCH**
- HTTP method để update một phần resource

**Path Parameter**
- Tham số trong URL path
- Ví dụ: `/users/{id}`

**POST**
- HTTP method để tạo resource mới

**PUT**
- HTTP method để update toàn bộ resource

### Q

**Query Parameter**
- Tham số trong URL sau dấu `?`
- Ví dụ: `/users?page=1&limit=10`

### R

**Request**
- Yêu cầu gửi từ client đến server

**Response**
- Phản hồi từ server trả về client

**REST (REpresentational State Transfer)**
- Kiến trúc API phổ biến nhất
- Sử dụng HTTP methods

**RESTful**
- API tuân theo các nguyên tắc REST

### S

**Server**
- Máy chủ xử lý API requests

**Status Code**
- Mã số cho biết kết quả của request
- 200 OK, 404 Not Found, 500 Internal Server Error...

### T

**Token**
- Chuỗi ký tự dùng để authentication

### V

**Variable (Postman)**
- Giá trị có thể tái sử dụng
- Global, Environment, Collection, Local

### X

**XML (eXtensible Markup Language)**
- Format dữ liệu kiểu tag-based
- Ít phổ biến hơn JSON

---

## HTTP Status Codes

### 2xx - Success (Thành công)

| Code | Name | Ý Nghĩa | Khi Nào |
|------|------|---------|---------|
| 200 | OK | Request thành công | GET, PUT, PATCH, DELETE thành công |
| 201 | Created | Resource được tạo thành công | POST tạo resource mới |
| 202 | Accepted | Request được chấp nhận nhưng chưa xử lý xong | Async operations |
| 204 | No Content | Thành công nhưng không có data trả về | DELETE thành công |

### 3xx - Redirection (Chuyển hướng)

| Code | Name | Ý Nghĩa |
|------|------|---------|
| 301 | Moved Permanently | Resource đã chuyển vĩnh viễn |
| 302 | Found | Resource tạm thời ở URL khác |
| 304 | Not Modified | Resource không thay đổi (caching) |

### 4xx - Client Errors (Lỗi phía client)

| Code | Name | Ý Nghĩa | Khi Nào |
|------|------|---------|---------|
| 400 | Bad Request | Request không hợp lệ | Sai format, thiếu field |
| 401 | Unauthorized | Chưa xác thực | Thiếu token, sai credentials |
| 403 | Forbidden | Không có quyền truy cập | Authenticated nhưng không được phép |
| 404 | Not Found | Resource không tồn tại | Sai ID, endpoint không tồn tại |
| 405 | Method Not Allowed | HTTP method không được hỗ trợ | POST vào read-only endpoint |
| 409 | Conflict | Xung đột dữ liệu | Email đã tồn tại |
| 422 | Unprocessable Entity | Dữ liệu không hợp lệ | Validation failed |
| 429 | Too Many Requests | Quá nhiều requests | Rate limiting |

### 5xx - Server Errors (Lỗi phía server)

| Code | Name | Ý Nghĩa | Khi Nào |
|------|------|---------|---------|
| 500 | Internal Server Error | Lỗi server | Bug trong code, exception |
| 502 | Bad Gateway | Gateway/proxy lỗi | Server upstream không phản hồi |
| 503 | Service Unavailable | Service tạm thời không khả dụng | Maintenance, overload |
| 504 | Gateway Timeout | Gateway timeout | Upstream server quá chậm |

---

## HTTP Headers

### Request Headers

**Authorization**
- Chứa credentials
- `Bearer {token}` hoặc `Basic {base64}`

**Content-Type**
- Loại dữ liệu gửi đi
- `application/json`, `application/xml`, `multipart/form-data`

**Accept**
- Loại dữ liệu muốn nhận
- `application/json`, `*/*`

**User-Agent**
- Thông tin về client
- Browser, mobile app, etc.

### Response Headers

**Content-Type**
- Loại dữ liệu response
- `application/json; charset=utf-8`

**Content-Length**
- Kích thước response (bytes)

**Cache-Control**
- Quy tắc caching
- `no-cache`, `max-age=3600`

**Set-Cookie**
- Set cookies cho client

---

## Public APIs

### Cho Người Mới Bắt Đầu

1. **JSONPlaceholder** - https://jsonplaceholder.typicode.com
   - Fake REST API
   - Không cần authentication
   - Perfect để học

2. **ReqRes** - https://reqres.in
   - User data API
   - Có authentication
   - Dễ dùng

3. **HTTPBin** - https://httpbin.org
   - HTTP testing service
   - Test mọi loại requests
   - Echo responses

### Free APIs (Cần Đăng Ký)

4. **OpenWeatherMap** - https://openweathermap.org/api
   - Weather data
   - Free tier: 60 calls/minute

5. **News API** - https://newsapi.org
   - News articles
   - Free tier: 100 requests/day

6. **The Movie DB** - https://www.themoviedb.org/settings/api
   - Movie data
   - Free tier

7. **CoinGecko** - https://www.coingecko.com/en/api
   - Cryptocurrency data
   - Generous free tier

8. **REST Countries** - https://restcountries.com
   - Country information
   - Completely free

### Danh Sách APIs

**Public APIs Directory:** https://github.com/public-apis/public-apis
- 1000+ public APIs
- Phân loại theo category
- Có/không có auth

---

## Tools

### API Testing Tools

**Postman** - https://www.postman.com
- Số 1 cho API testing
- Free tier đầy đủ

**Insomnia** - https://insomnia.rest
- Alternative cho Postman
- Đẹp và nhẹ

**Thunder Client** - VS Code Extension
- Test API trong VS Code

**REST Client** - VS Code Extension
- Test bằng text files

### Command Line Tools

**curl** - Built-in
- Universal HTTP client
- Available everywhere

**HTTPie** - https://httpie.io
- Modern curl alternative
- Human-friendly

### Testing Frameworks

**Newman** - Postman CLI
```bash
npm install -g newman
```

**REST Assured** (Java)
- Java library cho API testing

**Requests + Pytest** (Python)
- Python combination

**Supertest** (JavaScript)
- Node.js HTTP testing

---

## Learning Resources

### Official Documentation

**Postman Learning Center**
- https://learning.postman.com
- Comprehensive guides
- Video tutorials

**Postman YouTube Channel**
- https://youtube.com/c/Postman
- Tutorials, tips, webinars

**MDN Web Docs - HTTP**
- https://developer.mozilla.org/en-US/docs/Web/HTTP
- Tài liệu HTTP đầy đủ

### Online Courses

**Postman (Free)**
- Postman Student Expert
- API Testing Fundamentals

**Udemy**
- "Postman: The Complete Guide"
- "API Testing Foundations"

**Test Automation University**
- https://testautomationu.applitools.com
- Free courses

### YouTube Channels

**Automation Step by Step**
- API testing tutorials
- Postman guides

**The Testing Academy**
- API testing for beginners

**Raghav Pal**
- Comprehensive API testing

### Practice Platforms

**API Challenges**
- https://apichallenges.eviltester.com
- Gamified learning

**Restful Booker**
- https://restful-booker.herokuapp.com
- API để practice

---

## Community

### Forums

**Postman Community**
- https://community.postman.com
- Q&A, discussions

**Stack Overflow**
- Tag: `postman`, `api-testing`
- Q&A platform

### Reddit

**r/softwaretesting**
- Testing community

**r/QualityAssurance**
- QA professionals

### Blogs

**Postman Blog**
- https://blog.postman.com

**Ministry of Testing**
- https://www.ministryoftesting.com

---

## Books

**"REST API Design Rulebook"** - Mark Massé
- API design patterns
- Best practices

**"Designing Web APIs"** - Brenda Jin, Saurabh Sahni
- Building APIs
- Understanding design decisions

**"API Testing and Development with Postman"** - Dave Westerveld
- Comprehensive Postman guide

---

## Tips Để Tiếp Tục Học

1. **Thực hành hàng ngày** - 30 phút mỗi ngày
2. **Tham gia community** - Hỏi đáp, chia sẻ
3. **Test real APIs** - Áp dụng vào dự án thực
4. **Đọc API docs** - Học cách APIs được thiết kế
5. **Viết blog** - Chia sẻ những gì học được
6. **Contribute to open source** - GitHub projects
7. **Follow industry experts** - Twitter, LinkedIn
8. **Attend meetups/webinars** - Networking

---

## Chứng Chỉ (Tùy Chọn)

**Postman Student Expert**
- Free certification
- Good for beginners

**ISTQB - API Testing**
- Professional certification
- Recognized globally

---

## Tổng Kết

Học API testing là một hành trình liên tục. Tài liệu này chỉ là điểm bắt đầu. Quan trọng nhất là:

- 🎯 **Practice daily**
- 📚 **Keep learning**
- 👥 **Share knowledge**
- 🚀 **Build projects**

**Chúc bạn thành công trong sự nghiệp API Testing!**

---

[⬅️ Chương 8](../08-du-an-thuc-te/README.md) | [Về Trang Chủ](../README.md)
