# 5.1 API Key Authentication

API Key là phương thức authentication đơn giản và phổ biến nhất cho third-party APIs.

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Hiểu API Key là gì và cách hoạt động
- ✅ Gửi API Key qua Headers
- ✅ Gửi API Key qua Query Parameters
- ✅ Quản lý API Keys an toàn trong Postman
- ✅ Test APIs yêu cầu API Key

## 1. API Key Là Gì?

**API Key** là một chuỗi ký tự ngẫu nhiên dùng để xác định và authenticate API client.

### Ví Dụ API Key

```
sk_live_51AbC123DeF456...
AIzaSyD_abcdef123456789...
Bearer pk_test_xyz789...
```

### Cách Hoạt Động

```
Client Request:
GET /api/data
X-API-Key: your_api_key_123

Server:
1. Check API key hợp lệ không?
2. Check key còn active không?
3. Check rate limits
4. Trả về data hoặc error
```

## 2. API Key vs Other Auth Methods

| Đặc điểm | API Key | Bearer Token | OAuth 2.0 |
|----------|---------|--------------|-----------|
| **Độ phức tạp** | Đơn giản | Trung bình | Phức tạp |
| **Expiration** | Thường không | Có (15min-1h) | Có |
| **Refresh** | Không | Refresh token | Refresh token |
| **Use case** | Third-party APIs | Modern apps | Social login |

## 3. Gửi API Key Qua Headers

### Cách Phổ Biến Nhất

**Header names thường gặp:**
- `X-API-Key`
- `X-API-KEY`
- `api-key`
- `apiKey`
- `Authorization`

### Trong Postman - Cách 1: Tab Headers

```
Key: X-API-Key
Value: your_api_key_12345
```

### Trong Postman - Cách 2: Tab Authorization

1. Chọn tab **Authorization**
2. Type: **API Key**
3. Cấu hình:
   - **Key**: X-API-Key (hoặc tên header API yêu cầu)
   - **Value**: `{{apiKey}}` (dùng variable)
   - **Add to**: Header

Postman sẽ tự động thêm header!

### Sử Dụng Variables

**Environment:**
```
apiKey = sk_test_abc123xyz789
```

**Request Header:**
```
X-API-Key: {{apiKey}}
```

## 4. Gửi API Key Qua Query Parameters

Một số APIs yêu cầu API key trong URL.

### Ví Dụ

```
GET https://api.example.com/data?api_key=your_key_123
```

### Trong Postman - Tab Params

```
Key: api_key
Value: {{apiKey}}
```

URL sẽ tự động thành:
```
{{baseUrl}}/data?api_key={{apiKey}}
```

### ⚠️ Security Warning

**Query parameters kém bảo mật hơn headers vì:**
- ✅ Headers: Không hiển thị trong browser history
- ❌ Query params: Lưu trong logs, browser history, được cache

**Best practice:** Ưu tiên gửi qua Headers!

## 5. Thực Hành: OpenWeather API

OpenWeatherMap cung cấp free API key để test.

### Bước 1: Đăng Ký API Key

1. Truy cập: https://openweathermap.org/api
2. Sign up free account
3. Lấy API key từ: https://home.openweathermap.org/api_keys

**API key format:**
```
abc123def456ghi789jkl012
```

### Bước 2: Setup Environment

**Environment: Weather API**
```
baseUrl = https://api.openweathermap.org/data/2.5
apiKey = YOUR_ACTUAL_API_KEY_HERE
```

### Bước 3: Get Current Weather

**Request:**
```
Method: GET
URL: {{baseUrl}}/weather

Params:
  q = London
  appid = {{apiKey}}
```

**Expected Response:**
```json
{
  "coord": {
    "lon": -0.1257,
    "lat": 51.5085
  },
  "weather": [
    {
      "main": "Clouds",
      "description": "overcast clouds"
    }
  ],
  "main": {
    "temp": 280.15,
    "feels_like": 278.15,
    "humidity": 81
  },
  "name": "London"
}
```

### Bước 4: Test Invalid API Key

**Request:**
```
Params:
  q = London
  appid = invalid_key_123
```

**Expected:**
```
Status: 401 Unauthorized

{
  "cod": 401,
  "message": "Invalid API key. Please see http://openweathermap.org/faq#error401 for more info."
}
```

### Tests

```javascript
pm.test("Status code is 200", function() {
    pm.response.to.have.status(200);
});

pm.test("Has valid API key", function() {
    const response = pm.response.json();
    pm.expect(response.cod).to.not.eql(401);
});

pm.test("Response has weather data", function() {
    const response = pm.response.json();
    pm.expect(response).to.have.property("weather");
    pm.expect(response).to.have.property("main");
});
```

## 6. Collection-Level API Key

Để không phải thêm API key cho mỗi request, set cho cả collection.

### Cách Setup

1. Click vào **Collection**
2. Tab **Authorization**
3. Type: **API Key**
4. Config:
   ```
   Key: X-API-Key
   Value: {{apiKey}}
   Add to: Header
   ```
5. Save

Bây giờ **tất cả requests** trong collection tự động có API key!

### Override Cho Request Cụ Thể

Nếu 1 request cần API key khác:
1. Vào request đó
2. Tab Authorization
3. Type: **API Key** (override collection)
4. Điền API key riêng

## 7. Multiple API Keys

Một số services cần nhiều keys.

### Ví Dụ: Google Maps API

```
Params:
  key = {{googleMapsApiKey}}

Headers:
  X-Client-ID: {{googleClientId}}
```

### Environment Variables

```
googleMapsApiKey = AIzaSyD_abc123...
googleClientId = gme-company123
```

## 8. API Key Rotation

API keys nên được rotate (thay đổi) định kỳ.

### Scenario: Update API Key

**Bước 1:** Generate new key from service dashboard

**Bước 2:** Update environment
```
apiKey = new_key_xyz789  (thay old_key_abc123)
```

**Bước 3:** Test tất cả requests
```
Collection Runner → Run all requests
```

**Bước 4:** Verify tất cả pass

**Bước 5:** Revoke old key

## 9. Error Handling

### Common API Key Errors

| Error | Nguyên nhân | Giải pháp |
|-------|-------------|-----------|
| **401 Unauthorized** | Invalid key | Check key chính xác |
| **403 Forbidden** | Key hợp lệ nhưng không có quyền | Check permissions |
| **429 Too Many Requests** | Vượt rate limit | Đợi hoặc upgrade plan |
| **400 Bad Request** | Thiếu key | Thêm key vào request |

### Test Invalid Key

```javascript
// Pre-request: Set invalid key
pm.environment.set("apiKey", "invalid_key_123");
```

```javascript
// Tests
pm.test("Returns 401 for invalid key", function() {
    pm.response.to.have.status(401);
});

pm.test("Error message exists", function() {
    const response = pm.response.json();
    pm.expect(response).to.have.property("message");
});
```

## 10. Security Best Practices

### ✅ DO

- **Dùng environment variables** cho API keys
- **Set Current Value only**, để Initial Value trống
- **Không commit** environment files với real keys
- **Rotate keys** định kỳ (3-6 tháng)
- **Dùng different keys** cho Dev/Staging/Prod
- **Gửi qua Headers** thay vì query params
- **Use HTTPS** luôn luôn

### ❌ DON'T

- Hardcode API keys trong requests
- Share keys qua email/chat
- Commit keys vào Git
- Dùng production keys để test
- Expose keys trong screenshots
- Gửi keys qua HTTP (không SSL)

## 11. Environment Setup for Team

### Developer's Environment (Local)

```
baseUrl = https://api.example.com
apiKey = <FILL_YOUR_KEY_HERE>
```

Export và share file này. Mỗi developer tự fill key của mình.

### .gitignore

```
# Postman environments with secrets
*.postman_environment.json

# Except example template
!example.postman_environment.json
```

## 12. Bài Tập Thực Hành

### Bài 1: OpenWeather API Tests

1. Đăng ký OpenWeather API key
2. Create collection "Weather API Tests"
3. Add requests:
   - Get weather by city name
   - Get weather by coordinates
   - Get 5-day forecast
4. Set API key ở collection level
5. Run collection

### Bài 2: Invalid Key Tests

Tạo requests test error cases:

1. No API key → expect 400/401
2. Invalid API key → expect 401
3. Expired API key → expect 401
4. Empty API key → expect 400

### Bài 3: Multiple APIs

Setup environment với multiple API keys:

```
weatherApiKey = <key1>
newsApiKey = <key2>
mapsApiKey = <key3>
```

Create collection với requests đến cả 3 APIs.

### Bài 4: Rate Limiting

1. Gửi request nhiều lần (10-20 lần)
2. Observer khi nào hit rate limit
3. Handle 429 Too Many Requests error

## 13. Popular APIs Với API Key

### Free APIs Để Practice

1. **OpenWeatherMap**
   - https://openweathermap.org/api
   - Free tier: 60 calls/minute
   - Key location: Query param `appid`

2. **News API**
   - https://newsapi.org
   - Free tier: 100 requests/day
   - Key location: Header `X-Api-Key`

3. **The Movie Database (TMDB)**
   - https://www.themoviedb.org/settings/api
   - Free
   - Key location: Query param `api_key`

4. **Alpha Vantage (Stock data)**
   - https://www.alphavantage.co/support/#api-key
   - Free tier: 5 calls/minute
   - Key location: Query param `apikey`

## 14. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ API Key là gì và cách hoạt động
- ✅ Gửi API Key qua Headers (recommended)
- ✅ Gửi API Key qua Query Parameters
- ✅ Set API Key ở collection level
- ✅ Quản lý keys an toàn với environment variables
- ✅ Test với real APIs (OpenWeather)
- ✅ Handle errors (401, 403, 429)
- ✅ Security best practices

## 15. Kiến Thức Cần Nhớ

| Khái niệm | Giải thích |
|-----------|------------|
| **API Key** | String để authenticate API client |
| **X-API-Key** | Common header name cho API key |
| **401** | Invalid hoặc missing API key |
| **429** | Rate limit exceeded |
| **Environment variable** | Nơi lưu API key an toàn |

## Next Steps

Tiếp tục học các phương thức authentication khác:
- **Bài tiếp theo**: [5.2 Bearer Token và JWT](./bearer-token-jwt.md)

---

[⬅️ Tổng Quan Chương 5](./README.md) | [Tiếp Theo: Bearer Token ➡️](./bearer-token-jwt.md)
