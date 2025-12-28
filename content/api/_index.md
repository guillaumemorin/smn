---
title: API Reference
cascade:
  type: docs
---

# API Reference

Complete reference documentation for the hibi.is API.

## Base URL

```
https://hibi.is/api/v1
```

## Authentication

All API requests require authentication using an API key in the Authorization header:

```bash
curl https://hibi.is/api/v1/items \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Getting an API Key

1. Log in to [hibi.is](https://hibi.is)
2. Navigate to Settings → API
3. Click "Generate New API Key"
4. Copy and store securely

{{< callout type="error" >}}
**Security:** Never expose your API key in client-side code or public repositories.
{{< /callout >}}

## Rate Limits

| Plan | Requests per Hour | Requests per Second |
|------|-------------------|---------------------|
| Free | 60 | 1 |
| Pro | 1,000 | 10 |
| Enterprise | 10,000 | 100 |

**Rate Limit Headers:**
```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 995
X-RateLimit-Reset: 1642252800
```

## Endpoints

### Content

#### Create Content

**POST** `/create`

Create new content on hibi.is.

**Request Body:**
```json
{
  "content": "Your content here",
  "type": "text",
  "visibility": "public",
  "tags": ["example", "api"],
  "expiresAt": "2024-12-31T23:59:59Z",
  "password": "optional-password",
  "metadata": {
    "title": "My Document",
    "description": "Description here"
  }
}
```

**Parameters:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `content` | string | Yes | The content to create |
| `type` | string | No | Content type: `text`, `markdown`, `code`, `link` |
| `visibility` | string | No | Visibility: `public`, `unlisted`, `private` |
| `tags` | array | No | Array of tags for organization |
| `expiresAt` | string | No | ISO 8601 timestamp for expiration |
| `password` | string | No | Password to protect content |
| `metadata` | object | No | Additional metadata |

**Response:**
```json
{
  "id": "abc123",
  "url": "https://hibi.is/abc123",
  "type": "text",
  "visibility": "public",
  "createdAt": "2024-01-15T10:30:00Z",
  "expiresAt": null
}
```

**Example:**
```bash
curl -X POST https://hibi.is/api/v1/create \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "content": "Hello, World!",
    "type": "text",
    "visibility": "public",
    "tags": ["greeting", "example"]
  }'
```

#### Get Content

**GET** `/items/:id`

Retrieve a specific item by ID.

**Parameters:**
- `id` (path): The unique identifier of the content

**Response:**
```json
{
  "id": "abc123",
  "url": "https://hibi.is/abc123",
  "content": "Hello, World!",
  "type": "text",
  "visibility": "public",
  "tags": ["greeting", "example"],
  "createdAt": "2024-01-15T10:30:00Z",
  "updatedAt": "2024-01-15T10:30:00Z",
  "views": 42,
  "metadata": {}
}
```

**Example:**
```bash
curl https://hibi.is/api/v1/items/abc123 \
  -H "Authorization: Bearer YOUR_API_KEY"
```

#### List Items

**GET** `/items`

List all your items with optional filtering.

**Query Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `page` | integer | Page number (default: 1) |
| `perPage` | integer | Items per page (default: 30, max: 100) |
| `type` | string | Filter by content type |
| `visibility` | string | Filter by visibility |
| `tags` | string | Comma-separated tags |
| `search` | string | Search in content |
| `sortBy` | string | Sort field: `created`, `updated`, `views` |
| `order` | string | Sort order: `asc`, `desc` |

**Response:**
```json
{
  "items": [
    {
      "id": "abc123",
      "url": "https://hibi.is/abc123",
      "type": "text",
      "visibility": "public",
      "createdAt": "2024-01-15T10:30:00Z",
      "views": 42
    }
  ],
  "pagination": {
    "page": 1,
    "perPage": 30,
    "total": 150,
    "totalPages": 5,
    "hasMore": true
  }
}
```

**Example:**
```bash
curl "https://hibi.is/api/v1/items?page=1&perPage=50&tags=important&sortBy=created&order=desc" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

#### Update Content

**PATCH** `/items/:id`

Update an existing item.

**Request Body:**
```json
{
  "content": "Updated content",
  "tags": ["updated", "edited"],
  "visibility": "unlisted",
  "metadata": {
    "lastModified": "2024-01-16T14:20:00Z"
  }
}
```

**Response:**
```json
{
  "id": "abc123",
  "url": "https://hibi.is/abc123",
  "content": "Updated content",
  "updatedAt": "2024-01-16T14:20:00Z"
}
```

**Example:**
```bash
curl -X PATCH https://hibi.is/api/v1/items/abc123 \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "tags": ["updated"],
    "visibility": "unlisted"
  }'
```

#### Delete Content

**DELETE** `/items/:id`

Delete an item permanently.

**Response:**
```json
{
  "success": true,
  "message": "Item deleted successfully",
  "deletedAt": "2024-01-16T15:00:00Z"
}
```

**Example:**
```bash
curl -X DELETE https://hibi.is/api/v1/items/abc123 \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Batch Operations

#### Batch Create

**POST** `/batch/create`

Create multiple items in a single request.

**Request Body:**
```json
{
  "items": [
    {
      "content": "First item",
      "type": "text",
      "tags": ["batch"]
    },
    {
      "content": "Second item",
      "type": "text",
      "tags": ["batch"]
    }
  ]
}
```

**Response:**
```json
{
  "created": 2,
  "failed": 0,
  "items": [
    {
      "id": "abc123",
      "url": "https://hibi.is/abc123"
    },
    {
      "id": "def456",
      "url": "https://hibi.is/def456"
    }
  ]
}
```

#### Batch Update

**PATCH** `/batch/update`

Update multiple items at once.

**Request Body:**
```json
{
  "items": [
    {
      "id": "abc123",
      "tags": ["archived"]
    },
    {
      "id": "def456",
      "visibility": "private"
    }
  ]
}
```

#### Batch Delete

**POST** `/batch/delete`

Delete multiple items at once.

**Request Body:**
```json
{
  "ids": ["abc123", "def456", "ghi789"]
}
```

**Response:**
```json
{
  "deleted": 3,
  "failed": 0
}
```

### Analytics

#### Get Summary

**GET** `/analytics/summary`

Get analytics summary for your account.

**Query Parameters:**
- `period`: `day`, `week`, `month`, `year`
- `groupBy`: `hour`, `day`, `week`, `month`

**Response:**
```json
{
  "period": "month",
  "totalViews": 5420,
  "totalItems": 150,
  "totalShares": 230,
  "topItems": [
    {
      "id": "abc123",
      "url": "https://hibi.is/abc123",
      "views": 450
    }
  ],
  "topTags": ["api", "docs", "example"],
  "daily": [
    {
      "date": "2024-01-15",
      "views": 180,
      "created": 5
    }
  ]
}
```

#### Get Item Analytics

**GET** `/items/:id/analytics`

Get detailed analytics for a specific item.

**Response:**
```json
{
  "id": "abc123",
  "views": 450,
  "uniqueViews": 320,
  "shares": 45,
  "referrers": [
    {
      "source": "twitter.com",
      "count": 120
    },
    {
      "source": "github.com",
      "count": 80
    }
  ],
  "geography": [
    {
      "country": "US",
      "count": 200
    },
    {
      "country": "UK",
      "count": 100
    }
  ],
  "timeline": [
    {
      "date": "2024-01-15",
      "views": 50
    }
  ]
}
```

### Webhooks

#### Create Webhook

**POST** `/webhooks`

Register a webhook to receive real-time events.

**Request Body:**
```json
{
  "url": "https://your-server.com/webhook",
  "events": ["create", "update", "delete", "share"],
  "secret": "your-secret-key",
  "active": true
}
```

**Response:**
```json
{
  "id": "wh_abc123",
  "url": "https://your-server.com/webhook",
  "events": ["create", "update", "delete", "share"],
  "active": true,
  "createdAt": "2024-01-15T10:30:00Z"
}
```

#### List Webhooks

**GET** `/webhooks`

Get all registered webhooks.

#### Delete Webhook

**DELETE** `/webhooks/:id`

Remove a webhook.

#### Webhook Events

When an event occurs, hibi.is will POST to your webhook URL:

**Payload:**
```json
{
  "id": "evt_abc123",
  "event": "create",
  "timestamp": "2024-01-15T10:30:00Z",
  "data": {
    "id": "abc123",
    "url": "https://hibi.is/abc123",
    "type": "text",
    "createdAt": "2024-01-15T10:30:00Z"
  }
}
```

**Event Types:**
- `create`: New content created
- `update`: Content updated
- `delete`: Content deleted
- `share`: Content shared
- `view`: Content viewed (if enabled)

**Signature Verification:**
```javascript
const crypto = require('crypto');

function verifyWebhook(payload, signature, secret) {
  const expectedSignature = crypto
    .createHmac('sha256', secret)
    .update(JSON.stringify(payload))
    .digest('hex');
  
  return signature === expectedSignature;
}
```

### Export & Import

#### Export Data

**GET** `/export`

Export all your data.

**Query Parameters:**
- `format`: `json`, `csv`, `markdown`
- `includeMedia`: `true`, `false`
- `ids`: Comma-separated item IDs (optional)

**Response:**
```json
{
  "exportId": "exp_abc123",
  "status": "processing",
  "downloadUrl": null,
  "expiresAt": "2024-01-22T10:30:00Z"
}
```

Check export status:
```bash
curl https://hibi.is/api/v1/export/exp_abc123 \
  -H "Authorization: Bearer YOUR_API_KEY"
```

#### Import Data

**POST** `/import`

Import data from a file.

**Request:**
```bash
curl -X POST https://hibi.is/api/v1/import \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -F "file=@export.json" \
  -F "format=json"
```

**Response:**
```json
{
  "importId": "imp_abc123",
  "status": "processing",
  "total": 150,
  "processed": 0
}
```

## Error Handling

### Error Response Format

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": {
      "field": "content",
      "issue": "Content cannot be empty"
    },
    "timestamp": "2024-01-15T10:30:00Z",
    "requestId": "req_abc123"
  }
}
```

### Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `VALIDATION_ERROR` | 400 | Invalid input data |
| `UNAUTHORIZED` | 401 | Missing or invalid API key |
| `FORBIDDEN` | 403 | Insufficient permissions |
| `NOT_FOUND` | 404 | Resource not found |
| `GONE` | 410 | Resource expired or deleted |
| `PAYLOAD_TOO_LARGE` | 413 | Request too large |
| `RATE_LIMIT_EXCEEDED` | 429 | Too many requests |
| `SERVER_ERROR` | 500 | Internal server error |
| `SERVICE_UNAVAILABLE` | 503 | Temporary unavailable |

## SDKs and Libraries

### JavaScript/TypeScript

```bash
npm install hibi-client
```

```javascript
const Hibi = require('hibi-client');

const client = new Hibi({
  apiKey: process.env.HIBI_API_KEY
});

// Create content
const result = await client.create({
  content: 'Hello, World!',
  type: 'text'
});

console.log(result.url);
```

### Python

```bash
pip install hibi-python
```

```python
from hibi import HibiClient

client = HibiClient(api_key=os.getenv('HIBI_API_KEY'))

# Create content
result = client.create(
    content='Hello, World!',
    type='text'
)

print(result['url'])
```

### Go

```bash
go get github.com/hibi/hibi-go
```

```go
package main

import (
    "github.com/hibi/hibi-go"
    "os"
)

func main() {
    client := hibi.NewClient(os.Getenv("HIBI_API_KEY"))
    
    result, err := client.Create(hibi.CreateRequest{
        Content: "Hello, World!",
        Type:    "text",
    })
    
    if err != nil {
        panic(err)
    }
    
    println(result.URL)
}
```

## Best Practices

1. **Cache API Responses:** Reduce API calls by caching when appropriate
2. **Use Batch Endpoints:** More efficient than individual requests
3. **Handle Rate Limits:** Implement exponential backoff
4. **Verify Webhooks:** Always verify webhook signatures
5. **Secure API Keys:** Never expose keys in client-side code
6. **Use Pagination:** Don't fetch all items at once
7. **Set Appropriate TTLs:** Use cache headers effectively

## Support

- **Documentation:** [https://hibi.is/docs](https://hibi.is/docs)
- **API Status:** [https://status.hibi.is](https://status.hibi.is)
- **Support:** support@hibi.is
- **GitHub:** [github.com/guillaumemorin/hibi](https://github.com/guillaumemorin/hibi)
