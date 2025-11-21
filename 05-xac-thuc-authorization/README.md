# Chương 5: Xác Thực (Authentication) và Phân Quyền (Authorization)

Hầu hết các API thực tế đều yêu cầu authentication. Chương này sẽ dạy bạn cách làm việc với các phương thức xác thực phổ biến nhất.

## Mục Tiêu Học Tập

Sau khi hoàn thành chương này, bạn sẽ:

- ✅ Hiểu sự khác biệt giữa Authentication và Authorization
- ✅ Sử dụng API Key authentication
- ✅ Sử dụng Bearer Token
- ✅ Sử dụng Basic Auth
- ✅ Hiểu cơ bản về OAuth 2.0
- ✅ Test authentication flows

## Authentication vs Authorization

### Authentication (Xác thực)
**"Bạn là ai?"**
- Xác minh danh tính
- Login với username/password
- Nhận token

### Authorization (Phân quyền)
**"Bạn có quyền làm gì?"**
- Kiểm tra quyền truy cập
- Admin vs User
- Read vs Write

## Nội Dung Chương

### 5.1 No Auth
- API công khai
- Không cần xác thực
- Ví dụ: JSONPlaceholder, công khai data APIs

### 5.2 API Key
- API Key là gì?
- Gửi qua Header
- Gửi qua Query Parameter
- Best practices
- **Thực hành**: Test với API có API Key

### 5.3 Bearer Token (JWT)
- JWT (JSON Web Token) là gì?
- Login flow để lấy token
- Gửi token trong Authorization header
- Token expiration
- **Thực hành**: ReqRes login API

### 5.4 Basic Authentication
- Username:Password base64 encoded
- Khi nào dùng Basic Auth
- Security considerations
- **Thực hành**: HTTPBin basic auth

### 5.5 OAuth 2.0 (Cơ bản)
- OAuth 2.0 là gì?
- Authorization flows
- Access Token và Refresh Token
- Ví dụ: Login với Google, Facebook
- **Tham khảo**: Không thực hành sâu (phức tạp)

## Các Loại Authentication Phổ Biến

| Loại | Độ Phổ Biến | Độ Bảo Mật | Sử Dụng |
|------|-------------|------------|----------|
| **No Auth** | ⭐⭐ | 🔓 Low | Public APIs |
| **API Key** | ⭐⭐⭐⭐⭐ | 🔐 Medium | Third-party APIs |
| **Bearer Token** | ⭐⭐⭐⭐⭐ | 🔐 High | Modern web/mobile apps |
| **Basic Auth** | ⭐⭐⭐ | 🔓 Low-Medium | Internal APIs, simple apps |
| **OAuth 2.0** | ⭐⭐⭐⭐ | 🔐 High | Social logins, enterprise |

## Postman Authentication

Postman hỗ trợ authentication rất tốt:

### Cách Cấu Hình Auth trong Postman

1. Chọn tab "Authorization"
2. Chọn Type:
   - No Auth
   - API Key
   - Bearer Token
   - Basic Auth
   - OAuth 2.0
   - ... và nhiều loại khác
3. Điền thông tin
4. Postman tự động thêm vào requests

### Inheritance

- ✅ Set auth cho cả Collection → tất cả requests dùng chung
- ✅ Override cho từng request nếu cần
- ✅ Sử dụng variables cho tokens

## Bài Tập Thực Hành

### Bài 1: ReqRes Login Flow (Trung bình)

API: https://reqres.in

1. POST /api/register → lấy token
2. POST /api/login → lấy token
3. Lưu token vào environment variable
4. Sử dụng token cho các requests khác

**Steps:**
```
POST https://reqres.in/api/login
Body:
{
  "email": "eve.holt@reqres.in",
  "password": "cityslicka"
}

Response:
{
  "token": "QpwL5tke4Pnpja7X4"
}

→ Lưu token vào variable
→ Dùng cho các requests tiếp theo
```

### Bài 2: HTTPBin Basic Auth (Dễ)

API: https://httpbin.org

1. GET /basic-auth/{user}/{password}
2. Sử dụng Basic Auth trong Postman
3. Verify response 200

**Ví dụ:**
```
GET https://httpbin.org/basic-auth/username/password
Authorization: Basic Auth
Username: username
Password: password
```

### Bài 3: API Key Practice (Dễ)

Đăng ký miễn phí một trong các APIs sau:

1. **OpenWeatherMap** - https://openweathermap.org/api
   - Weather data
   - Free tier: 60 calls/minute

2. **News API** - https://newsapi.org
   - News articles
   - Free tier: 100 requests/day

3. **The Movie DB** - https://www.themoviedb.org/settings/api
   - Movie data
   - Free tier

**Practice:**
- Đăng ký và lấy API key
- Test các endpoints
- Thử gửi sai API key → expect 401

## Security Best Practices

### ❌ KHÔNG NÊN:
- Hard-code API keys/tokens trong code
- Commit keys vào Git
- Share keys công khai
- Sử dụng keys production cho testing
- Lưu passwords plain text

### ✅ NÊN:
- Sử dụng environment variables trong Postman
- Rotate keys định kỳ
- Sử dụng separate keys cho dev/staging/prod
- Revoke keys khi không dùng
- Use HTTPS (không phải HTTP)

## Testing Authentication

### Test Cases Cần Có:

1. **Valid credentials** → 200 OK
2. **Invalid credentials** → 401 Unauthorized
3. **Missing credentials** → 401 Unauthorized
4. **Expired token** → 401 Unauthorized
5. **Invalid token format** → 401 Unauthorized
6. **Revoked token** → 401 Unauthorized

### Test Authorization:

1. **Admin user** → access admin endpoints → 200
2. **Regular user** → access admin endpoints → 403 Forbidden
3. **User A** → access User B data → 403 Forbidden
4. **No authentication** → access protected endpoint → 401

## Common HTTP Status Codes

- **200 OK** - Authentication successful
- **201 Created** - Registration successful
- **400 Bad Request** - Invalid request body
- **401 Unauthorized** - Missing/invalid credentials
- **403 Forbidden** - No permission
- **404 Not Found** - User not found

## Troubleshooting

### Error: 401 Unauthorized

**Kiểm tra:**
- API key/token có đúng không?
- Có gửi trong đúng header không?
- Token có hết hạn không?
- Có gửi "Bearer" prefix không? (Bearer {token})

### Error: 403 Forbidden

**Kiểm tra:**
- User có quyền truy cập không?
- API key có đủ permissions không?

## Thời Gian Học

**Ước tính: 4-5 giờ**

---

[⬅️ Chương 4](../04-thuc-hanh-co-ban/README.md) | [Về Trang Chủ](../README.md) | [Tiếp Theo: Chương 6 ➡️](../06-testing-nang-cao/README.md)
