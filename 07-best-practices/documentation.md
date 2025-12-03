# 7.5 Documentation

Documentation tốt giúp team members hiểu API, sử dụng collections đúng cách, và onboard nhanh chóng.

## Mục Tiêu

- ✅ Collection descriptions
- ✅ Request documentation
- ✅ Markdown support
- ✅ Published documentation

## 1. Collection Description

### Add Description

**Trong Postman:**
1. Click collection name
2. Click **Documentation** tab
3. Write description using Markdown

**Example:**
```markdown
# User Management API

## Overview
Complete API for user CRUD operations with authentication.

## Base URL
- Development: `http://localhost:3000`
- Staging: `https://staging-api.example.com`
- Production: `https://api.example.com`

## Authentication
All endpoints require Bearer token except:
- POST /auth/register
- POST /auth/login

## Quick Start
1. Select environment (Dev/Staging/Prod)
2. Run "01-Authentication" folder to get token
3. Token automatically saved to `authToken` variable
4. Run other requests

## Endpoints Summary
- **Authentication**: Register, Login, Logout
- **Users**: CRUD operations
- **Profiles**: Get, Update user profiles

## Author
API Team - Last updated: 2024-01-15
```

> **📸 HÌNH ẢNH:** Collection Description Editor
> - File: `collection-description-editor.png`
> - Nội dung: Screenshot of Postman Documentation tab showing markdown editor với preview pane

<!-- IMAGE_PLACEHOLDER: collection-description-editor.png -->

## 2. Request Description

### Document Each Request

**Click request → Documentation tab:**

```markdown
# GET User by ID

## Description
Retrieves detailed information for a specific user.

## Endpoint
```
GET /api/users/:id
```

## Path Parameters
- `id` (integer, required): User ID

## Headers
- `Authorization` (string, required): Bearer token

## Success Response (200)
```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "role": "user",
  "createdAt": "2024-01-15T10:30:00Z"
}
```

## Error Responses
- `400 Bad Request`: Invalid user ID
- `401 Unauthorized`: Missing or invalid token
- `404 Not Found`: User does not exist

## Example
```bash
curl -X GET https://api.example.com/users/1 \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## Tests
This request validates:
- Status code is 200
- Response contains user data
- User ID matches request parameter
```

## 3. Folder Descriptions

```markdown
# 01-Authentication

## Purpose
Authentication flow including registration, login, and token management.

## Prerequisites
None - these are public endpoints.

## Execution Order
1. **POST Register**: Create new account
2. **POST Login**: Get access token
3. **POST Refresh Token**: Renew expired token
4. **POST Logout**: Invalidate token

## Variables Set
- `authToken`: JWT access token (auto-set after login)
- `refreshToken`: Refresh token for renewal
- `userId`: Current user ID

## Notes
- Tokens expire after 1 hour
- Use refresh token to get new access token
- Logout invalidates both tokens
```

## 4. Markdown Features

### Headers

```markdown
# H1 - Main Title
## H2 - Section
### H3 - Subsection
#### H4 - Detail
```

### Text Formatting

```markdown
**Bold text**
*Italic text*
`Inline code`
~~Strikethrough~~
```

### Lists

```markdown
**Unordered:**
- Item 1
- Item 2
  - Nested item

**Ordered:**
1. First
2. Second
3. Third
```

### Code Blocks

````markdown
```javascript
pm.test("Status is 200", () => {
    pm.response.to.have.status(200);
});
```

```json
{
  "key": "value"
}
```
````

### Links

```markdown
[Link text](https://example.com)
[Internal link](#section-name)
```

### Tables

```markdown
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /users | Get all users |
| POST | /users | Create user |
| PUT | /users/:id | Update user |
| DELETE | /users/:id | Delete user |
```

### Images

```markdown
![Alt text](https://example.com/image.png)
```

### Blockquotes

```markdown
> **Note:** This endpoint requires admin privileges.

> **Warning:** Rate limited to 100 requests/hour.
```

## 5. Examples in Documentation

### Request Example

```markdown
## Example Request

**Endpoint:**
```
POST /api/users
```

**Headers:**
```
Content-Type: application/json
Authorization: Bearer abc123...
```

**Body:**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "securePass123"
}
```

**Response (201 Created):**
```json
{
  "id": 42,
  "name": "John Doe",
  "email": "john@example.com",
  "createdAt": "2024-01-15T10:30:00Z"
}
```
```

### Variables Example

```markdown
## Using Variables

This request uses the following variables:

- `{{baseUrl}}`: API base URL
- `{{authToken}}`: Bearer token from login
- `{{userId}}`: Current user ID

**Example:**
```
GET {{baseUrl}}/users/{{userId}}
Authorization: Bearer {{authToken}}
```

Make sure to:
1. Select correct environment
2. Run login request first to set `authToken`
```

## 6. Best Practices

### Collection Level

```markdown
# API Test Suite

## Overview
Brief description of what this collection does.

## Setup
Step-by-step setup instructions.

## Authentication
How authentication works.

## Running Tests
How to run the collection.

## Troubleshooting
Common issues and solutions.
```

### Request Level

```markdown
# Request Name

## Description
What this endpoint does.

## Parameters
All parameters with types and descriptions.

## Response
Expected response format.

## Error Cases
Possible errors and meanings.

## Examples
Practical examples.
```

### ✅ DO

- Write clear, concise descriptions
- Include setup instructions
- Document all parameters
- Provide examples
- Explain error responses
- Use consistent formatting
- Update docs when API changes
- Add troubleshooting tips

### ❌ DON'T

- Leave collections undocumented
- Use unclear descriptions
- Skip parameter documentation
- Forget to update docs
- Use inconsistent formatting
- Assume knowledge
- Ignore error cases

## 7. Published Documentation

### Publish to Web

**Steps:**
1. Click collection
2. Click **...** → **Publish Docs**
3. Choose template and theme
4. Click **Publish**
5. Get public URL

**Features:**
- Public or team-only access
- Auto-generated from collection
- Always up-to-date
- Interactive examples
- Try requests in browser

> **📸 HÌNH ẢNH:** Published Documentation View
> - File: `published-documentation.png`
> - Nội dung: Screenshot of published documentation webpage showing navigation sidebar, request details, và "Try it" button

<!-- IMAGE_PLACEHOLDER: published-documentation.png -->

### Custom Domain

Postman allows custom domains for published docs:
```
https://docs.yourcompany.com
```

## 8. Environment Documentation

```markdown
# Development Environment

## Variables

### Base Configuration
- `baseUrl`: http://localhost:3000
- `timeout`: 30000 (ms)

### Authentication
- `authToken`: JWT token (set automatically)
- `refreshToken`: Refresh token (set automatically)

### Test Data
- `testEmail`: test@example.com
- `testPassword`: Test123!

## Setup
1. Select "Development" environment
2. Run authentication flow
3. Tokens auto-populate

## Notes
- Use this for local development
- Database resets daily at 2AM
- Test data is pre-populated
```

## 9. Complete Documentation Example

```markdown
# E-Commerce API Testing Suite

## 📋 Overview
Comprehensive test suite for E-Commerce platform API including authentication, product management, shopping cart, and order processing.

## 🌐 Environments

| Environment | Base URL | Purpose |
|-------------|----------|---------|
| Development | http://localhost:3000 | Local development |
| Staging | https://staging-api.shop.com | Pre-production testing |
| Production | https://api.shop.com | Live environment |

## 🔐 Authentication

All endpoints except login/register require JWT Bearer token.

**Get Token:**
1. Run `POST /auth/login`
2. Token auto-saved to `authToken` variable
3. Token expires in 24 hours

**Example:**
```
Authorization: Bearer eyJhbGc...
```

## 📁 Collection Structure

### 01-Authentication
- POST Register: Create account
- POST Login: Get token
- POST Logout: Invalidate token

### 02-Products
- GET Products: List all products
- GET Product by ID: Single product details
- POST Product: Create new (admin only)
- PUT Product: Update existing (admin only)
- DELETE Product: Remove product (admin only)

### 03-Shopping Cart
- GET Cart: View current cart
- POST Add to Cart: Add product
- PUT Update Quantity: Change quantity
- DELETE Remove from Cart: Remove product

### 04-Orders
- POST Create Order: Place order
- GET Orders: List user orders
- GET Order by ID: Order details

## ⚙️ Setup Instructions

1. **Import Collection**
   - Download `ecommerce-api-tests.json`
   - Import into Postman

2. **Import Environment**
   - Download `dev-environment.json`
   - Import and select environment

3. **Run Authentication**
   - Execute `01-Authentication` folder
   - Verify `authToken` is set

4. **Run Tests**
   - Run entire collection or specific folders
   - Use Collection Runner for full suite

## 🧪 Running Tests

**Single Request:**
- Click request → Send

**Folder:**
- Right-click folder → Run

**Entire Collection:**
- Collection Runner → Select collection → Run

**Data-Driven:**
- Collection Runner → Select data file (CSV/JSON)

## 🚀 CI/CD Integration

```bash
# Install Newman
npm install -g newman

# Run tests
newman run ecommerce-api-tests.json \
  -e dev-environment.json \
  -r html,cli \
  --reporter-html-export report.html
```

## 🐛 Troubleshooting

**Issue: 401 Unauthorized**
- Solution: Run login request to refresh token

**Issue: Tests failing**
- Check environment is selected
- Verify baseUrl is correct
- Check network connectivity

**Issue: Timeout errors**
- Increase timeout in environment
- Check API server status

## 📊 Test Coverage

- Authentication: 100%
- Products CRUD: 100%
- Shopping Cart: 100%
- Orders: 90%
- Error handling: 85%

## 👥 Team

- **Author**: API Testing Team
- **Maintainer**: john@example.com
- **Last Updated**: 2024-01-15
- **Version**: 2.1.0

## 📚 Additional Resources

- [API Documentation](https://docs.shop.com)
- [Postman Workspace](https://workspace.postman.com/...)
- [GitHub Repository](https://github.com/company/api-tests)
```

> **📸 HÌNH ẢNH:** Complete Documentation Example
> - File: `documentation-complete-example.png`
> - Nội dung: Screenshot showing comprehensive collection documentation với table of contents, formatted sections, code examples

<!-- IMAGE_PLACEHOLDER: documentation-complete-example.png -->

## 10. Documentation Templates

### Request Template

```markdown
# [METHOD] [Endpoint Name]

## Description
[What this endpoint does]

## Endpoint
```
[METHOD] [path]
```

## Parameters
[List all parameters]

## Request Body
[Example request body if applicable]

## Response
[Example success response]

## Errors
[Possible error responses]

## Tests
[What tests validate]

## Notes
[Additional information]
```

### Collection Template

```markdown
# [Collection Name]

## Overview
[Brief description]

## Environments
[List environments]

## Authentication
[Auth requirements]

## Structure
[Folder breakdown]

## Setup
[Setup steps]

## Running
[How to run]

## Troubleshooting
[Common issues]

## Contact
[Team contact info]
```

## Tổng Kết

- ✅ Document collections and requests
- ✅ Use Markdown formatting
- ✅ Include examples
- ✅ Publish documentation
- ✅ Keep docs updated
- ✅ Add troubleshooting info

---

[⬅️ Error Handling](./error-handling.md) | [Tổng Quan Chương 7](./README.md) | [Tiếp Theo: Security ➡️](./security-best-practices.md)
