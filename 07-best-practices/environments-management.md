# 7.2 Environments Management

Quản lý environments đúng cách giúp switch giữa Dev/Staging/Production dễ dàng và an toàn.

## Mục Tiêu

- ✅ Setup multiple environments
- ✅ Variable best practices
- ✅ Security với Current vs Initial values
- ✅ Switch environments safely

## 1. Standard Environments

### Development
```json
{
  "baseUrl": "http://localhost:3000",
  "apiKey": "dev_key_123",
  "dbUrl": "localhost:5432",
  "timeout": 30000
}
```

### Staging
```json
{
  "baseUrl": "https://staging-api.example.com",
  "apiKey": "staging_key_456",
  "dbUrl": "staging-db.example.com",
  "timeout": 10000
}
```

### Production
```json
{
  "baseUrl": "https://api.example.com",
  "apiKey": "prod_key_789",
  "dbUrl": "prod-db.example.com",
  "timeout": 5000
}
```

> **📸 HÌNH ẢNH:** Multiple Environments
> - File: `environments-dev-staging-prod.png`
> - Nội dung: Screenshot showing 3 environments (Development, Staging, Production) trong Postman với different baseUrl values

<!-- IMAGE_PLACEHOLDER: environments-dev-staging-prod.png -->

## 2. Variable Best Practices

### Initial Value vs Current Value

**Initial Value:**
- ✅ Synced khi export/share
- ✅ Safe để commit vào Git
- ✅ Dùng cho non-sensitive data

**Current Value:**
- ❌ NOT synced
- ❌ Local only
- ✅ Dùng cho sensitive data (API keys, passwords)

**Example:**
```
Variable: apiKey
Initial Value: <YOUR_API_KEY>
Current Value: actual_secret_key_abc123
```

## 3. Security Best Practices

### ✅ DO
- Use Current Value cho credentials
- Add placeholders trong Initial Value
- Document required variables
- Use different keys per environment
- Rotate keys regularly

### ❌ DON'T
- Store passwords trong Initial Value
- Commit environments với secrets
- Share production credentials
- Use same key across environments

## 4. Environment Switcher

> **📸 HÌNH ẢNH:** Environment Switcher
> - File: `environment-switcher-dropdown.png`
> - Nội dung: Screenshot environment dropdown menu showing active environment và list để switch

<!-- IMAGE_PLACEHOLDER: environment-switcher-dropdown.png -->

## Tổng Kết

- ✅ Setup 3 environments: Dev, Staging, Prod
- ✅ Use Initial/Current values correctly
- ✅ Security best practices

---

[⬅️ Tổ Chức Collections](./to-chuc-collections.md) | [Tổng Quan Chương 7](./README.md) | [Tiếp Theo: Maintainable Tests ➡️](./writing-maintainable-tests.md)
