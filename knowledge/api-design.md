# API Design Best Practices

TRIGGER: API, REST, endpoint, HTTP, versioning, request, response, GraphQL

## Overview

Well-designed APIs are consistent, intuitive, and maintainable. This guide covers REST API design principles, versioning strategies, error handling, and documentation standards.

## Core Principles

### API-First Design
1. Design the API contract before writing code
2. Review with stakeholders (consumers, architects)
3. Use OpenAPI/Swagger for specification
4. Generate code from spec, not spec from code

### REST Constraints
- **Client-Server**: Separation of concerns
- **Stateless**: Each request contains all needed information
- **Cacheable**: Responses indicate cacheability
- **Uniform Interface**: Consistent resource addressing
- **Layered System**: Intermediaries transparent to client

---

## Resource Design

### URL Structure
Use nouns for resources, not verbs:
```
# GOOD - Resources as nouns
GET    /users           # List users
GET    /users/123       # Get user 123
POST   /users           # Create user
PUT    /users/123       # Update user 123
DELETE /users/123       # Delete user 123

# BAD - Verbs in URL
GET    /getUsers
POST   /createUser
POST   /deleteUser/123
```

### Naming Conventions
| Convention | Example |
|------------|---------|
| Plural nouns | `/users`, `/orders`, `/products` |
| Lowercase | `/user-profiles` not `/UserProfiles` |
| Hyphens for readability | `/user-accounts` not `/user_accounts` |
| No file extensions | `/users` not `/users.json` |

### Nested Resources
Use for clear parent-child relationships:
```
GET /users/123/orders           # User's orders
GET /users/123/orders/456       # Specific order
POST /users/123/orders          # Create order for user
```

Limit nesting depth to 2-3 levels maximum.

### Query Parameters
Use for filtering, sorting, pagination:
```
GET /users?status=active                    # Filter
GET /users?sort=created_at&order=desc       # Sort
GET /users?page=2&limit=20                  # Paginate
GET /users?fields=id,name,email             # Sparse fields
```

---

## HTTP Methods

### Method Semantics

| Method | Purpose | Idempotent | Safe | Request Body |
|--------|---------|------------|------|--------------|
| GET | Retrieve resource(s) | Yes | Yes | No |
| POST | Create resource | No | No | Yes |
| PUT | Replace resource | Yes | No | Yes |
| PATCH | Partial update | No | No | Yes |
| DELETE | Remove resource | Yes | No | Optional |

### Method Usage
```
# GET - Retrieve (never modify data)
GET /users/123
→ Returns user 123

# POST - Create (returns created resource)
POST /users
Body: {"name": "John", "email": "john@example.com"}
→ Returns created user with ID

# PUT - Full replacement (send complete resource)
PUT /users/123
Body: {"name": "John Doe", "email": "john@example.com", "status": "active"}
→ Returns updated user

# PATCH - Partial update (send only changed fields)
PATCH /users/123
Body: {"status": "inactive"}
→ Returns updated user

# DELETE - Remove
DELETE /users/123
→ Returns 204 No Content
```

---

## HTTP Status Codes

### Success Codes (2xx)
| Code | Meaning | Use Case |
|------|---------|----------|
| 200 | OK | Successful GET, PUT, PATCH |
| 201 | Created | Successful POST, resource created |
| 204 | No Content | Successful DELETE |

### Client Error Codes (4xx)
| Code | Meaning | Use Case |
|------|---------|----------|
| 400 | Bad Request | Invalid request body/parameters |
| 401 | Unauthorized | Missing/invalid authentication |
| 403 | Forbidden | Authenticated but not authorized |
| 404 | Not Found | Resource doesn't exist |
| 409 | Conflict | State conflict (duplicate, version) |
| 422 | Unprocessable Entity | Validation errors |
| 429 | Too Many Requests | Rate limit exceeded |

### Server Error Codes (5xx)
| Code | Meaning | Use Case |
|------|---------|----------|
| 500 | Internal Server Error | Unexpected server error |
| 502 | Bad Gateway | Upstream service error |
| 503 | Service Unavailable | Temporary overload/maintenance |
| 504 | Gateway Timeout | Upstream timeout |

### Anti-Pattern
```json
// BAD - 200 with error in body
HTTP 200 OK
{"success": false, "error": "User not found"}

// GOOD - Proper status code
HTTP 404 Not Found
{"error": "User not found"}
```

---

## Error Handling

### RFC 9457 Problem Details Format
Standard error response format:
```json
{
  "type": "https://api.example.com/errors/validation-error",
  "title": "Validation Error",
  "status": 422,
  "detail": "The email address format is invalid",
  "instance": "/users/123",
  "errors": [
    {
      "field": "email",
      "message": "Must be a valid email address"
    }
  ]
}
```

### Error Response Best Practices
1. **Use appropriate HTTP status codes** - Don't wrap errors in 200
2. **Be consistent** - Same error format across all endpoints
3. **Be specific** - Tell clients what went wrong
4. **Be secure** - Don't expose internal details or stack traces
5. **Be actionable** - Help clients fix the problem

### Validation Errors
```json
{
  "type": "https://api.example.com/errors/validation-error",
  "title": "Validation Error",
  "status": 422,
  "errors": [
    {"field": "email", "message": "Required field"},
    {"field": "age", "message": "Must be positive integer"}
  ]
}
```

---

## API Versioning

### Strategies

#### URL Path Versioning (Most Common)
```
GET /v1/users
GET /v2/users
```
**Pros**: Explicit, easy to route, cache-friendly
**Cons**: URL changes between versions

#### Header Versioning
```
GET /users
Accept: application/vnd.api.v2+json
```
**Pros**: Clean URLs
**Cons**: Harder to test, less visible

#### Query Parameter Versioning
```
GET /users?version=2
```
**Pros**: Simple to implement
**Cons**: Clutters query string

### Recommendation
- **Use URL path versioning** for public APIs (most widely understood)
- Version at major breaking changes only
- Provide migration documentation
- Set deprecation timeline with sunset headers

### Deprecation Headers
```
Sunset: Sat, 31 Dec 2025 23:59:59 GMT
Deprecation: Sun, 01 Jan 2025 00:00:00 GMT
Link: <https://api.example.com/v3/users>; rel="successor-version"
```

---

## Pagination

### Offset-Based (Simple)
```
GET /users?offset=40&limit=20
```
**Response**:
```json
{
  "data": [...],
  "pagination": {
    "total": 100,
    "limit": 20,
    "offset": 40,
    "next": "/users?offset=60&limit=20",
    "prev": "/users?offset=20&limit=20"
  }
}
```

### Cursor-Based (Scalable)
```
GET /users?cursor=eyJpZCI6MTIzfQ&limit=20
```
**Response**:
```json
{
  "data": [...],
  "pagination": {
    "next_cursor": "eyJpZCI6MTQzfQ",
    "has_more": true
  }
}
```
**Pros**: Works well with real-time data, no page drift
**Cons**: No random access to pages

---

## Rate Limiting

### Headers
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1640995200
Retry-After: 60
```

### Response on Limit Exceeded
```json
HTTP 429 Too Many Requests

{
  "type": "https://api.example.com/errors/rate-limit-exceeded",
  "title": "Rate Limit Exceeded",
  "status": 429,
  "detail": "You have exceeded the rate limit of 1000 requests per hour",
  "retry_after": 60
}
```

---

## Authentication & Security

### Methods
| Method | Use Case |
|--------|----------|
| API Key | Simple, server-to-server |
| OAuth 2.0 | User authorization, third-party apps |
| JWT | Stateless authentication |

### Security Best Practices
1. **Always use HTTPS** (TLS 1.3 preferred)
2. **Authenticate in headers** - Never in URL query params
3. **Use short-lived tokens** - Refresh tokens for long sessions
4. **Implement rate limiting** - Prevent abuse
5. **Validate all input** - Don't trust client data
6. **Log security events** - Authentication failures, unusual patterns

### Authorization Header
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

---

## Request/Response Format

### Content-Type
```
Content-Type: application/json
Accept: application/json
```

### Request Body Standards
```json
// Dates: ISO 8601
{"created_at": "2024-01-15T10:30:00Z"}

// Enums: lowercase strings
{"status": "active"}

// IDs: strings (avoid integer overflow)
{"user_id": "123456789"}

// Nulls: explicit null for clearing values
{"middle_name": null}
```

### Response Envelope (Optional)
```json
{
  "data": {...},
  "meta": {
    "request_id": "abc123",
    "timestamp": "2024-01-15T10:30:00Z"
  }
}
```

---

## Documentation

### OpenAPI/Swagger
```yaml
openapi: "3.1.0"
info:
  title: User API
  version: "1.0.0"
paths:
  /users:
    get:
      summary: List users
      responses:
        '200':
          description: Successful response
```

### Documentation Must Include
- [ ] Authentication requirements
- [ ] Request/response examples
- [ ] Error responses and codes
- [ ] Rate limits
- [ ] Pagination details
- [ ] Versioning policy

---

## API Design Checklist

### Resource Design
- [ ] Nouns for resources (not verbs)
- [ ] Plural names for collections
- [ ] Consistent naming conventions
- [ ] Sensible nesting depth

### HTTP Semantics
- [ ] Correct methods for operations
- [ ] Appropriate status codes
- [ ] Idempotent operations where expected

### Error Handling
- [ ] Consistent error format (RFC 9457)
- [ ] Specific, actionable messages
- [ ] No internal details exposed

### Versioning & Evolution
- [ ] Versioning strategy in place
- [ ] Deprecation policy defined
- [ ] Backward compatibility considered

### Security
- [ ] HTTPS enforced
- [ ] Authentication in headers
- [ ] Rate limiting implemented
- [ ] Input validation

### Documentation
- [ ] OpenAPI spec maintained
- [ ] Examples for all endpoints
- [ ] Changelog for versions

---

## References

- [Microsoft Azure REST API Guidelines](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)
- [Google Cloud API Design Guide](https://cloud.google.com/apis/design)
- [Zalando RESTful API Guidelines](https://opensource.zalando.com/restful-api-guidelines/)
- [RFC 9457 - Problem Details for HTTP APIs](https://www.rfc-editor.org/rfc/rfc9457)
- [JSON:API Specification](https://jsonapi.org/)
