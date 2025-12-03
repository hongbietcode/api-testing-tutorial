# 7.6 Security Best Practices

Bảo mật trong API testing đảm bảo credentials an toàn, tránh leak sensitive data, và test security vulnerabilities.

## Mục Tiêu

- ✅ Credentials management
- ✅ Environment security
- ✅ .gitignore setup
- ✅ HTTPS requirement
- ✅ Security testing

## 1. Never Hardcode Credentials

### ❌ BAD

```javascript
// Pre-request Script
pm.environment.set("apiKey", "sk_live_abc123xyz789");
pm.environment.set("password", "MySecretPassword123");
```

```json
// Collection
{
  "auth": {
    "type": "bearer",
    "bearer": {
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
    }
  }
}
```

### ✅ GOOD

```javascript
// Use environment variables
const apiKey = pm.environment.get("apiKey");
const password = pm.environment.get("password");
```

**In environment file:**
```json
{
  "values": [
    {
      "key": "apiKey",
      "value": "<YOUR_API_KEY>",
      "enabled": true
    }
  ]
}
```

> **📸 HÌNH ẢNH:** Environment Variable Security
> - File: `environment-security-diagram.png`
> - Nội dung: Diagram showing Initial Value (safe, placeholder like <API_KEY>) vs Current Value (actual secret, local only) với icons for Git/Share

<!-- IMAGE_PLACEHOLDER: environment-security-diagram.png -->

## 2. Initial vs Current Values

### Understanding the Difference

**Initial Value:**
- ✅ Synced when exporting collection
- ✅ Shared với team members
- ✅ Committed to Git
- ⚠️ Use placeholders only

**Current Value:**
- ❌ NOT synced
- ❌ Local machine only
- ✅ Safe for actual secrets

### Example Setup

**Environment configuration:**

| Variable | Initial Value | Current Value |
|----------|---------------|---------------|
| `baseUrl` | `https://api.example.com` | `https://api.example.com` |
| `apiKey` | `<YOUR_API_KEY>` | `sk_live_abc123xyz789` |
| `password` | `<YOUR_PASSWORD>` | `MySecretPass123!` |
| `adminToken` | `<ADMIN_TOKEN>` | `eyJhbGci...` |

**✅ Safe to commit:**
- baseUrl (public info)
- apiKey Initial Value (placeholder)

**❌ Never commit:**
- Current Values với actual credentials

## 3. Git Best Practices

### .gitignore Setup

```gitignore
# Postman
*.postman_environment.json
*-environment.json
environments/
secrets/

# Sensitive files
.env
.env.local
credentials.json
tokens.txt

# Reports might contain sensitive data
newman/
reports/
*.html

# Logs
*.log
npm-debug.log*
```

### What to Commit

**✅ Safe to commit:**
```
✓ Collections (.postman_collection.json)
✓ Environment templates (with placeholders)
✓ Documentation
✓ Test scripts
✓ CI/CD configs (no secrets)
```

**❌ Never commit:**
```
✗ Actual environment files with secrets
✗ API keys, passwords, tokens
✗ Production credentials
✗ Personal access tokens
✗ Private keys
✗ Test data with PII
```

### Environment Template

**Commit this:**
```json
{
  "name": "Development Template",
  "values": [
    {
      "key": "baseUrl",
      "value": "http://localhost:3000",
      "enabled": true
    },
    {
      "key": "apiKey",
      "value": "<YOUR_API_KEY_HERE>",
      "enabled": true
    },
    {
      "key": "adminEmail",
      "value": "<YOUR_ADMIN_EMAIL>",
      "enabled": true
    }
  ]
}
```

**In README:**
```markdown
## Setup

1. Copy `dev-template.json` to `dev-environment.json`
2. Replace placeholders with actual values:
   - `<YOUR_API_KEY_HERE>`: Get from Settings → API Keys
   - `<YOUR_ADMIN_EMAIL>`: Your admin email
3. Import `dev-environment.json` into Postman
```

> **📸 HÌNH ẢNH:** Git Commit Safety
> - File: `git-commit-safety.png`
> - Nội dung: Side-by-side comparison: Left (safe commit với placeholders, green checkmark) vs Right (unsafe commit với actual secrets, red X)

<!-- IMAGE_PLACEHOLDER: git-commit-safety.png -->

## 4. HTTPS Only

### ❌ BAD

```javascript
// HTTP - Unencrypted!
pm.environment.set("baseUrl", "http://api.example.com");
```

### ✅ GOOD

```javascript
// HTTPS - Encrypted
pm.environment.set("baseUrl", "https://api.example.com");
```

### Validate HTTPS

```javascript
pm.test("Request uses HTTPS", () => {
    const url = pm.request.url.toString();
    pm.expect(url).to.match(/^https:\/\//);
});
```

### Exception: Local Development

```javascript
// Only allowed for localhost
const baseUrl = pm.environment.get("baseUrl");
if (baseUrl.includes("localhost") || baseUrl.includes("127.0.0.1")) {
    // OK to use HTTP for local dev
} else {
    pm.expect(baseUrl).to.match(/^https:\/\//);
}
```

## 5. Token Management

### Store Tokens Securely

```javascript
// After login
pm.test("Login successful", () => {
    pm.response.to.have.status(200);
    const response = pm.response.json();
    
    // Store in environment, not global
    pm.environment.set("authToken", response.token);
    pm.environment.set("refreshToken", response.refreshToken);
});
```

### Token Expiration

```javascript
// Check token expiration
const tokenExpiry = pm.environment.get("tokenExpiry");
const now = Date.now();

if (!tokenExpiry || now > tokenExpiry) {
    console.log("Token expired, need to re-login");
    // Redirect to login or refresh token
}
```

### Clear Tokens on Logout

```javascript
// Logout request tests
pm.test("Logout successful", () => {
    pm.response.to.have.status(200);
    
    // Clear sensitive data
    pm.environment.unset("authToken");
    pm.environment.unset("refreshToken");
    pm.environment.unset("userId");
});
```

## 6. Sensitive Data in Logs

### ❌ BAD

```javascript
console.log("Login with:", {
    email: email,
    password: password  // ❌ Password in console!
});

console.log("API Response:", pm.response.json());  // Might contain PII
```

### ✅ GOOD

```javascript
console.log("Login attempt for:", email);
console.log("Password:", "***REDACTED***");

// Log only non-sensitive parts
const response = pm.response.json();
console.log("User ID:", response.id);
// Don't log entire response if it has PII
```

### Sanitize Logs

```javascript
function sanitizeLog(obj) {
    const sanitized = {...obj};
    const sensitiveKeys = ['password', 'token', 'apiKey', 'secret', 'ssn'];
    
    sensitiveKeys.forEach(key => {
        if (sanitized[key]) {
            sanitized[key] = '***REDACTED***';
        }
    });
    
    return sanitized;
}

console.log("Request:", sanitizeLog(requestData));
```

## 7. Security Testing

### Test Authentication

```javascript
// Test missing token
pm.test("Missing token returns 401", () => {
    pm.response.to.have.status(401);
});

// Test invalid token
pm.test("Invalid token returns 401", () => {
    pm.response.to.have.status(401);
});

// Test expired token
pm.test("Expired token returns 401", () => {
    pm.response.to.have.status(401);
});
```

### Test Authorization

```javascript
// Test insufficient permissions
pm.test("Non-admin cannot access admin endpoint", () => {
    pm.response.to.have.status(403);
    const response = pm.response.json();
    pm.expect(response.error).to.include('Forbidden');
});
```

### Input Validation

```javascript
// SQL Injection
pm.test("SQL injection blocked", () => {
    // Input: "'; DROP TABLE users; --"
    pm.response.to.have.status(400);
});

// XSS
pm.test("XSS attempt rejected", () => {
    // Input: "<script>alert('XSS')</script>"
    pm.response.to.have.status(400);
});

// Path Traversal
pm.test("Path traversal blocked", () => {
    // Input: "../../../etc/passwd"
    pm.response.to.have.status(400);
});
```

### Rate Limiting

```javascript
// Test rate limiting
pm.test("Rate limit enforced", () => {
    // After 100 requests
    pm.response.to.have.status(429);  // Too Many Requests
    const response = pm.response.json();
    pm.expect(response).to.have.property('retryAfter');
});
```

## 8. CI/CD Security

### GitHub Actions

**❌ BAD:**
```yaml
- name: Run tests
  run: |
    newman run collection.json \
      --env-var "apiKey=sk_live_abc123"  # ❌ Exposed!
```

**✅ GOOD:**
```yaml
- name: Run tests
  env:
    API_KEY: ${{ secrets.API_KEY }}
    DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
  run: |
    newman run collection.json \
      --env-var "apiKey=$API_KEY" \
      --env-var "dbPassword=$DB_PASSWORD"
```

**Setup GitHub Secrets:**
1. Repository → Settings → Secrets
2. New repository secret
3. Name: `API_KEY`
4. Value: `sk_live_abc123xyz789`

### Environment Variables

```javascript
// Pre-request Script - Safe for CI/CD
const apiKey = pm.environment.get("apiKey") || pm.variables.get("apiKey");

if (!apiKey) {
    throw new Error("API key not found. Set environment variable.");
}
```

## 9. Team Collaboration

### Workspace Security

**Personal Workspace:**
- Private collections
- Personal credentials
- Experimental tests

**Team Workspace:**
- Shared collections
- No credentials
- Template environments

### Share Safely

**✅ Safe to share:**
- Collections
- Environment templates (placeholders)
- Documentation
- Test scripts

**❌ Never share:**
- Production credentials
- API keys
- Passwords
- Personal tokens
- Environment files with secrets

### Documentation

```markdown
## Security Setup

**⚠️ NEVER commit actual credentials!**

### Required Variables

- `apiKey`: Get from Admin Panel → API Keys
- `adminToken`: Request from team lead
- `dbPassword`: Stored in 1Password (search "API DB Password")

### Setup Steps

1. Copy `environment-template.json` to `my-environment.json`
2. Replace all `<PLACEHOLDER>` values
3. Verify `my-environment.json` is in `.gitignore`
4. Import into Postman
5. ✅ Verify Current Value is set, Initial Value is placeholder
```

## 10. Security Checklist

### Before Committing

- [ ] No hardcoded credentials
- [ ] Environment files use placeholders
- [ ] `.gitignore` includes environment files
- [ ] Logs don't contain passwords/tokens
- [ ] HTTPS used for non-local URLs
- [ ] Sensitive data redacted from reports

### Before Sharing

- [ ] Collections don't contain tokens
- [ ] Environment Initial Values are placeholders
- [ ] No PII in test data
- [ ] Documentation explains security setup

### Testing

- [ ] Test missing authentication (401)
- [ ] Test insufficient authorization (403)
- [ ] Test SQL injection protection
- [ ] Test XSS protection
- [ ] Test rate limiting
- [ ] Test expired tokens

## 11. Best Practices Summary

### ✅ DO

- Use environment variables for all credentials
- Set Current Value, not Initial Value for secrets
- Use HTTPS for all non-local requests
- Add comprehensive `.gitignore`
- Store secrets in CI/CD secret manager
- Clear tokens on logout
- Sanitize logs
- Test security vulnerabilities
- Document security setup
- Use template environments

### ❌ DON'T

- Hardcode API keys, passwords, tokens
- Commit environment files with secrets
- Share production credentials
- Log sensitive data
- Use HTTP for production APIs
- Share collections with embedded credentials
- Skip authentication tests
- Ignore security testing
- Store secrets in code or docs

## Tổng Kết

- ✅ Use Initial Value (placeholders) vs Current Value (secrets)
- ✅ Comprehensive `.gitignore`
- ✅ HTTPS only for non-local
- ✅ Token management
- ✅ Security testing
- ✅ CI/CD secret management
- ✅ Safe team collaboration

---

[⬅️ Documentation](./documentation.md) | [Tổng Quan Chương 7](./README.md) | [Tiếp Theo: Performance ➡️](./performance.md)
