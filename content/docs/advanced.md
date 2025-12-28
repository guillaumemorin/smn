---
title: Advanced Usage
weight: 3
prev: /docs/core-features
next: /docs/configuration
---

# Advanced Usage

Take your hibi.is experience to the next level with these advanced features and techniques.

## Advanced Features

### Custom Domains

Use your own domain with hibi.is:

1. **Configure DNS:**
   ```dns
   CNAME hibi your-domain.com.hibi.is
   ```

2. **Verify Ownership:**
   - Add TXT record to your DNS
   - Complete verification in settings

3. **Enable HTTPS:**
   - Automatic SSL certificate generation
   - Force HTTPS redirects

### Automation & Workflows

#### Automated Content Creation

Create content programmatically using the API:

```bash
curl -X POST https://hibi.is/api/v1/create \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "content": "Your content here",
    "type": "text",
    "visibility": "public"
  }'
```

Response:
```json
{
  "id": "abc123",
  "url": "https://hibi.is/abc123",
  "created_at": "2024-01-15T10:30:00Z"
}
```

#### Webhook Integration

Set up webhooks to trigger actions:

```json
{
  "url": "https://your-server.com/webhook",
  "events": ["create", "update", "delete"],
  "secret": "your_webhook_secret"
}
```

Event payload example:
```json
{
  "event": "create",
  "timestamp": "2024-01-15T10:30:00Z",
  "data": {
    "id": "abc123",
    "type": "text",
    "url": "https://hibi.is/abc123"
  }
}
```

### Batch Operations

Process multiple items efficiently:

#### Bulk Creation

```javascript
const items = [
  { content: "Item 1", type: "text" },
  { content: "Item 2", type: "text" },
  { content: "Item 3", type: "text" }
];

const results = await fetch('https://hibi.is/api/v1/batch/create', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({ items })
});
```

#### Bulk Updates

```python
import requests

items_to_update = [
    {"id": "abc123", "visibility": "private"},
    {"id": "def456", "tags": ["important", "archived"]}
]

response = requests.post(
    "https://hibi.is/api/v1/batch/update",
    headers={"Authorization": "Bearer YOUR_API_KEY"},
    json={"items": items_to_update}
)
```

### Advanced Search

Use powerful search operators:

```plaintext
Search Operators:
- tag:important          - Filter by tag
- type:code              - Filter by content type
- created:2024-01       - Filter by creation date
- visibility:public      - Filter by visibility
- title:"exact match"    - Exact phrase search
```

Example searches:
```plaintext
tag:work type:text created:2024-01
title:"API documentation" tag:important
type:code language:python
```

### Content Templates

Create reusable templates:

```yaml
# template.yaml
name: "API Endpoint Documentation"
type: "markdown"
template: |
  # {endpoint_name}
  
  ## Description
  {description}
  
  ## Request
  ```http
  {method} {path}
  ```
  
  ## Response
  ```json
  {response_example}
  ```
  
  ## Parameters
  {parameters}
```

Use template:
```javascript
const content = renderTemplate('api-endpoint', {
  endpoint_name: 'Create User',
  description: 'Creates a new user account',
  method: 'POST',
  path: '/api/users',
  response_example: '{"id": "123", "status": "created"}',
  parameters: '- name: string (required)\n- email: string (required)'
});
```

### Version Control

Track changes to your content:

{{< callout type="info" >}}
Version history is available for Pro and Enterprise plans.
{{< /callout >}}

- **Automatic Versioning:** Every edit creates a new version
- **Diff View:** Compare versions side-by-side
- **Rollback:** Restore previous versions
- **History Export:** Download complete version history

```bash
# List versions
curl https://hibi.is/api/v1/items/abc123/versions \
  -H "Authorization: Bearer YOUR_API_KEY"

# Get specific version
curl https://hibi.is/api/v1/items/abc123/versions/5 \
  -H "Authorization: Bearer YOUR_API_KEY"

# Rollback to version
curl -X POST https://hibi.is/api/v1/items/abc123/rollback \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -d '{"version": 5}'
```

### Custom Styling

Apply custom CSS to your content:

```css
/* Custom theme */
.hibi-content {
  font-family: 'Your Font', sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: #ffffff;
}

.hibi-content h1 {
  font-size: 2.5rem;
  border-bottom: 2px solid #ffffff;
}

.hibi-content code {
  background: rgba(0, 0, 0, 0.2);
  padding: 0.2em 0.4em;
  border-radius: 3px;
}
```

### Performance Optimization

#### Caching Strategies

```javascript
// Set cache headers
await createContent({
  content: "Your content",
  cache: {
    maxAge: 3600,        // 1 hour
    staleWhileRevalidate: 86400  // 1 day
  }
});
```

#### CDN Integration

Leverage CDN for faster delivery:

1. Enable CDN in settings
2. Configure cache policies
3. Set appropriate TTL values
4. Monitor cache hit rates

### Collaboration Features

#### Shared Workspaces

Create collaborative spaces:

```javascript
const workspace = await createWorkspace({
  name: "Team Documentation",
  members: [
    { email: "user1@example.com", role: "editor" },
    { email: "user2@example.com", role: "viewer" }
  ],
  permissions: {
    create: ["owner", "editor"],
    edit: ["owner", "editor"],
    delete: ["owner"]
  }
});
```

#### Real-time Collaboration

Enable real-time editing:

- **Concurrent Editing:** Multiple users can edit simultaneously
- **Conflict Resolution:** Automatic merge of changes
- **Presence Indicators:** See who's online
- **Change Notifications:** Get notified of updates

### Integration Examples

#### Slack Integration

```javascript
// Send to Slack when content is created
webhook.on('create', async (event) => {
  await fetch(SLACK_WEBHOOK_URL, {
    method: 'POST',
    body: JSON.stringify({
      text: `New content created: ${event.data.url}`,
      attachments: [{
        title: event.data.title,
        text: event.data.preview,
        color: '#36a64f'
      }]
    })
  });
});
```

#### GitHub Actions

```yaml
name: Deploy to hibi.is
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Deploy to hibi.is
        run: |
          curl -X POST https://hibi.is/api/v1/create \
            -H "Authorization: Bearer ${{ secrets.HIBI_API_KEY }}" \
            -H "Content-Type: application/json" \
            -d @content.json
```

## Performance Tips

1. **Use Compression:** Enable gzip/brotli compression
2. **Optimize Images:** Compress images before upload
3. **Lazy Loading:** Load content on demand
4. **Batch Requests:** Combine multiple operations
5. **Cache Aggressively:** Use appropriate cache headers

## Security Best Practices

1. **API Key Rotation:** Rotate keys regularly
2. **Least Privilege:** Grant minimum required permissions
3. **Input Validation:** Validate all user input
4. **Rate Limiting:** Respect API rate limits
5. **HTTPS Only:** Always use encrypted connections

## Next Steps

{{< cards >}}
  {{< card link="/docs/configuration" title="Configuration" icon="cog" >}}
  {{< card link="/api" title="API Reference" icon="code" >}}
  {{< card link="/docs/best-practices" title="Best Practices" icon="check-circle" >}}
{{< /cards >}}
