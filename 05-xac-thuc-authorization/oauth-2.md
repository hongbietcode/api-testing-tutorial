# 5.4 OAuth 2.0 Cơ Bản

OAuth 2.0 là authorization framework phức tạp nhất và mạnh mẽ nhất, được sử dụng rộng rãi cho social logins và enterprise applications.

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Hiểu OAuth 2.0 là gì và tại sao cần
- ✅ Phân biệt Authentication vs Authorization
- ✅ Hiểu các OAuth 2.0 flows
- ✅ Biết các roles trong OAuth
- ✅ Hiểu Access Token và Refresh Token
- ✅ Nhận biết OAuth trong thực tế

> **⚠️ Lưu ý:** OAuth 2.0 rất phức tạp. Chương này chỉ giới thiệu **concepts cơ bản**. Để implement thực tế, cần học sâu hơn.

## 1. OAuth 2.0 Là Gì?

### Định Nghĩa

**OAuth 2.0** là **authorization framework** cho phép applications lấy **limited access** đến user accounts trên HTTP services.

### Không Phải Authentication!

❌ **OAuth is NOT authentication**
✅ **OAuth is authorization**

- **Authentication**: Xác minh "bạn là ai"
- **Authorization**: Xác định "bạn có quyền làm gì"

### Ví Dụ Thực Tế

Bạn muốn dùng app "Photo Printer" để in ảnh từ Google Photos:

```
1. Photo Printer: "Cho phép truy cập Google Photos của bạn?"
2. Bạn: "OK" (đồng ý)
3. Google: "Photo Printer có thể xem ảnh của bạn"
4. Photo Printer: Lấy token từ Google
5. Photo Printer: Dùng token để access ảnh
```

**Quan trọng:**
- Photo Printer **KHÔNG biết** Google password của bạn
- Photo Printer chỉ có **limited access** (xem ảnh, không xóa account)
- Bạn có thể **revoke access** bất cứ lúc nào

## 2. Tại Sao Cần OAuth 2.0?

### Vấn Đề Trước OAuth

**Scenario:** App muốn access Gmail của bạn

**Cách cũ (Không an toàn):**
```
1. App: "Nhập Gmail username/password"
2. Bạn: Nhập credentials
3. App: Lưu password và login vào Gmail
```

**Vấn đề:**
- ❌ App biết password của bạn
- ❌ App có **full access** Gmail
- ❌ Không thể revoke access mà không đổi password
- ❌ Nếu app bị hack, password bị lộ

### Giải Pháp: OAuth 2.0

```
1. App redirect bạn đến Gmail
2. Bạn login trên Gmail (app không thấy password)
3. Gmail hỏi: "App muốn đọc emails. Allow?"
4. Bạn: "OK"
5. Gmail đưa access token cho App
6. App dùng token để đọc emails
```

**Lợi ích:**
- ✅ App không biết password
- ✅ Limited permissions (chỉ đọc email)
- ✅ Có thể revoke bất cứ lúc nào
- ✅ Token có expiration time

## 3. OAuth 2.0 Roles

### 4 Roles Chính

```
┌─────────────┐
│ Resource    │  You (user với Google account)
│ Owner       │
└─────────────┘
       │
       │ authorizes
       ▼
┌─────────────┐         ┌─────────────┐
│ Client      │────────▶│ Authorization│
│ Application │ requests│ Server       │  Google
│             │◀────────│              │
└─────────────┘  token  └─────────────┘
  Photo Printer              │
       │                     │ protects
       │ uses token          ▼
       │              ┌─────────────┐
       └─────────────▶│ Resource    │
                      │ Server      │  Google Photos API
                      └─────────────┘
```

#### 1. Resource Owner (Chủ sở hữu)
- **Bạn** - người dùng
- Có quyền grant access

#### 2. Client (Ứng dụng)
- **Photo Printer app**
- Muốn access resources
- Có Client ID và Client Secret

#### 3. Authorization Server
- **Google OAuth Server**
- Xác thực user
- Issue access tokens

#### 4. Resource Server
- **Google Photos API**
- Lưu trữ protected resources
- Accept tokens để cho access

## 4. OAuth 2.0 Grant Types (Flows)

OAuth có nhiều "flows" khác nhau cho different use cases.

### 1. Authorization Code Flow

**Phổ biến nhất, cho web apps.**

```
User → Client App → Authorization Server → Client App → Resource Server

1. User clicks "Login with Google"
2. App redirects to Google
3. User login và consent
4. Google redirects back với authorization code
5. App đổi code lấy access token
6. App dùng token để call API
```

**Ví dụ URLs:**

**Step 1: Redirect to authorize**
```
https://accounts.google.com/o/oauth2/v2/auth?
  client_id=YOUR_CLIENT_ID&
  redirect_uri=https://yourapp.com/callback&
  response_type=code&
  scope=email profile&
  state=random_string
```

**Step 2: User login và consent**

**Step 3: Redirect back với code**
```
https://yourapp.com/callback?
  code=AUTHORIZATION_CODE&
  state=random_string
```

**Step 4: Exchange code for token**
```
POST https://oauth2.googleapis.com/token
{
  "client_id": "YOUR_CLIENT_ID",
  "client_secret": "YOUR_CLIENT_SECRET",
  "code": "AUTHORIZATION_CODE",
  "redirect_uri": "https://yourapp.com/callback",
  "grant_type": "authorization_code"
}
```

**Response:**
```json
{
  "access_token": "ya29.abc123...",
  "expires_in": 3600,
  "refresh_token": "1//xyz789...",
  "token_type": "Bearer"
}
```

### 2. Implicit Flow

**Cho browser-based apps (SPA). Ít an toàn hơn.**

```
1. App redirect đến Authorization Server
2. User login
3. Redirect back trực tiếp với access token (no authorization code)
```

**Không recommend** cho apps mới, dùng Authorization Code + PKCE thay thế.

### 3. Client Credentials Flow

**Cho machine-to-machine, không có user.**

```
POST /token
{
  "grant_type": "client_credentials",
  "client_id": "YOUR_CLIENT_ID",
  "client_secret": "YOUR_CLIENT_SECRET"
}
```

**Response:**
```json
{
  "access_token": "abc123...",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

**Use case:**
- Microservices authentication
- Backend jobs
- Server-to-server

### 4. Resource Owner Password Credentials

**User cung cấp username/password cho client. Ít an toàn.**

```json
{
  "grant_type": "password",
  "username": "user@example.com",
  "password": "userpassword",
  "client_id": "CLIENT_ID"
}
```

**Chỉ dùng khi:** Tin tưởng client hoàn toàn (first-party apps).

## 5. Access Token vs Refresh Token

### Access Token

- **Short-lived**: 15 minutes - 1 hour
- Dùng để access APIs
- Gửi trong mỗi request: `Authorization: Bearer <token>`
- Khi expire → 401 Unauthorized

### Refresh Token

- **Long-lived**: 7-90 days
- Dùng để lấy access token mới
- Không gửi đến Resource Server
- Lưu trữ an toàn

### Token Refresh Flow

```
1. Access token expires → API returns 401
2. Client: POST /token với refresh_token
3. Authorization Server: Returns new access_token (và có thể new refresh_token)
4. Client: Retry API call với new access token
```

**Request:**
```
POST /oauth/token
{
  "grant_type": "refresh_token",
  "refresh_token": "xyz789...",
  "client_id": "CLIENT_ID",
  "client_secret": "CLIENT_SECRET"
}
```

**Response:**
```json
{
  "access_token": "new_access_token",
  "expires_in": 3600,
  "refresh_token": "new_refresh_token"
}
```

## 6. OAuth 2.0 Scopes

**Scopes** định nghĩa permissions mà client yêu cầu.

### Ví Dụ Google Scopes

```
scope=email                           # Đọc email address
scope=profile                         # Đọc profile info
scope=https://www.googleapis.com/auth/drive.readonly  # Đọc Google Drive
scope=https://www.googleapis.com/auth/gmail.send      # Gửi email
```

### Multiple Scopes

```
scope=email profile https://www.googleapis.com/auth/drive
```

### User Consent

Khi app yêu cầu scopes, user thấy:

```
Photo Printer muốn:
✓ Xem địa chỉ email
✓ Xem thông tin cơ bản
✓ Xem ảnh trong Google Photos

[Cho phép] [Từ chối]
```

## 7. Postman và OAuth 2.0

Postman support OAuth 2.0 rất tốt!

### Setup OAuth 2.0 trong Postman

1. Tab **Authorization**
2. Type: **OAuth 2.0**
3. Click **Get New Access Token**

### Configuration

```
Token Name: Google OAuth Token
Grant Type: Authorization Code
Callback URL: https://oauth.pstmn.io/v1/callback
Auth URL: https://accounts.google.com/o/oauth2/v2/auth
Access Token URL: https://oauth2.googleapis.com/token
Client ID: YOUR_CLIENT_ID
Client Secret: YOUR_CLIENT_SECRET
Scope: email profile
```

4. Click **Request Token**
5. Browser mở → Login Google
6. Grant permissions
7. Postman nhận token
8. Click **Use Token**

### Token Management

Postman tự động:
- Lưu access token
- Lưu refresh token
- Hiển thị expiration time
- Có thể manually refresh

## 8. OAuth trong Thực Tế

### Ví Dụ: Login with Google

Bạn thường thấy buttons:

```
[Continue with Google]
[Sign in with Facebook]
[Login with GitHub]
```

Tất cả dùng OAuth 2.0!

### Flow Thực Tế

1. Click "Login with Google"
2. Redirect đến `accounts.google.com`
3. Login (nếu chưa)
4. Consent screen: "App X muốn access email và profile"
5. Click "Allow"
6. Redirect về app với authorization code
7. App exchange code → access token
8. App call Google API để lấy user info
9. App tạo session cho user

## 9. Security Best Practices

### ✅ DO

- **Use Authorization Code flow** với PKCE
- **Validate redirect_uri** strictly
- **Use state parameter** để prevent CSRF
- **Store tokens securely** (encrypted)
- **Use HTTPS** always
- **Implement token refresh**
- **Minimal scopes** (chỉ yêu cầu cần thiết)
- **Revoke tokens** khi logout

### ❌ DON'T

- Use Implicit flow (deprecated)
- Store tokens in localStorage (XSS vulnerable)
- Hardcode client secrets trong frontend
- Skip state parameter
- Request excessive scopes
- Use HTTP (insecure)
- Share client secrets

## 10. Testing OAuth APIs

### Khi API Dùng OAuth

Nhiều public APIs yêu cầu OAuth:

- **Google APIs** (Gmail, Drive, Calendar, Maps)
- **GitHub API**
- **Facebook Graph API**
- **Twitter API**
- **Microsoft Graph API**
- **Spotify API**

### Test Flow

1. **Register app** tại provider
2. Get **Client ID** và **Client Secret**
3. Configure **Redirect URI**
4. Use Postman OAuth 2.0 helper
5. Get access token
6. Save token to environment
7. Test API endpoints

## 11. OAuth 2.1 (Future)

OAuth 2.1 đang được phát triển với improvements:

- ✅ Bắt buộc PKCE
- ✅ Loại bỏ Implicit flow
- ✅ Loại bỏ Password grant
- ✅ Enhanced security

## 12. Common Errors

| Error | Nguyên nhân | Giải pháp |
|-------|-------------|-----------|
| **invalid_client** | Wrong client_id/secret | Check credentials |
| **invalid_grant** | Code/token expired | Get new code |
| **unauthorized_client** | Client không allowed | Check app config |
| **access_denied** | User từ chối | User cần consent |
| **invalid_scope** | Scope không hợp lệ | Check scope names |

## 13. Tổng Kết

Sau bài học này, bạn đã biết:
- ✅ OAuth 2.0 là authorization framework
- ✅ 4 roles: Resource Owner, Client, Authorization Server, Resource Server
- ✅ Grant types: Authorization Code, Client Credentials, etc.
- ✅ Access Token (short) vs Refresh Token (long)
- ✅ Scopes define permissions
- ✅ How OAuth works với Google, Facebook, GitHub
- ✅ Security best practices

### OAuth 2.0 ≠ Authentication

**Nhớ:**
- OAuth là **authorization** (permissions)
- Để authentication, dùng **OpenID Connect** (built on OAuth)

## 14. So Sánh Auth Methods

| Feature | Basic Auth | API Key | Bearer Token | OAuth 2.0 |
|---------|------------|---------|--------------|-----------|
| **Complexity** | ⭐ | ⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Security** | Low | Medium | High | Highest |
| **Expiration** | No | No | Yes | Yes |
| **Refresh** | No | No | Yes | Yes |
| **Granular permissions** | No | No | No | Yes (scopes) |
| **Third-party access** | No | No | No | Yes |

## 15. Kiến Thức Cần Nhớ

| Khái niệm | Giải thích |
|-----------|------------|
| **OAuth 2.0** | Authorization framework |
| **Grant Type** | Flow để lấy token |
| **Access Token** | Token dùng để call APIs |
| **Refresh Token** | Token để lấy access token mới |
| **Scope** | Permissions được yêu cầu |
| **PKCE** | Security extension cho OAuth |

## Next Steps

Hoàn thành Chương 5! Bạn đã hiểu:
- ✅ API Key
- ✅ Bearer Token / JWT
- ✅ Basic Auth
- ✅ OAuth 2.0

Tiếp tục:
- **Bài tiếp theo**: [5.5 Bài Tập Authentication](./bai-tap-authentication.md)
- **Chương tiếp theo**: [Chương 6: Testing Nâng Cao](../06-testing-nang-cao/README.md)

## Tài Liệu Tham Khảo

- [OAuth 2.0 RFC](https://datatracker.ietf.org/doc/html/rfc6749)
- [OAuth 2.0 Playground](https://developers.google.com/oauthplayground)
- [Auth0 Docs](https://auth0.com/docs/get-started/authentication-and-authorization-flow)

---

[⬅️ Basic Auth](./basic-auth.md) | [Tổng Quan Chương 5](./README.md) | [Tiếp Theo: Bài Tập ➡️](./bai-tap-authentication.md)
