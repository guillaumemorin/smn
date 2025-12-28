---
title: Configuration
weight: 4
prev: /docs/advanced
next: /docs/troubleshooting
---

# Configuration Guide

Learn how to configure hibi.is to match your specific needs and preferences.

## Account Settings

### Profile Configuration

Set up your profile information:

- **Display Name:** How you appear to others
- **Email:** For notifications and account recovery
- **Avatar:** Profile picture (optional)
- **Bio:** Short description about yourself
- **Website:** Link to your website or portfolio

### Privacy Settings

Control who can see your content:

{{< tabs items="Public,Unlisted,Private,Custom" >}}

{{< tab >}}
**Public**
- Visible to everyone
- Appears in search results
- Can be indexed by search engines
- Anyone with the link can access
{{< /tab >}}

{{< tab >}}
**Unlisted**
- Not visible in public listings
- Not indexed by search engines
- Accessible only via direct link
- Good for semi-private sharing
{{< /tab >}}

{{< tab >}}
**Private**
- Only you can access
- Requires authentication
- Not shared or indexed
- Maximum privacy
{{< /tab >}}

{{< tab >}}
**Custom**
- Share with specific users
- Set access permissions
- Configure expiration
- Granular control
{{< /tab >}}

{{< /tabs >}}

### Notification Settings

Customize your notifications:

```yaml
notifications:
  email:
    enabled: true
    frequency: daily  # instant, daily, weekly
    events:
      - content_shared
      - content_expired
      - storage_limit_warning
  
  browser:
    enabled: true
    events:
      - new_comment
      - mention
  
  webhook:
    url: "https://your-server.com/webhook"
    events:
      - all
```

## Content Settings

### Default Visibility

Set default visibility for new content:

```javascript
const defaults = {
  visibility: 'public',      // public, unlisted, private
  expiresIn: null,           // null, 3600, 86400, 604800
  allowComments: true,
  enableAnalytics: true,
  notifyOnShare: false
};
```

### Content Retention

Configure how long content is kept:

| Setting | Value | Description |
|---------|-------|-------------|
| Never Expire | `null` | Keep forever (default) |
| 1 Hour | `3600` | Auto-delete after 1 hour |
| 1 Day | `86400` | Auto-delete after 24 hours |
| 1 Week | `604800` | Auto-delete after 7 days |
| Custom | `number` | Custom duration in seconds |

{{< callout type="warning" >}}
Expired content is permanently deleted and cannot be recovered.
{{< /callout >}}

### Storage Management

Monitor and manage your storage:

```plaintext
Current Usage: 450 MB / 1 GB (45%)

Storage Breakdown:
- Text Content:     150 MB (33%)
- Code Snippets:    100 MB (22%)
- Images:           150 MB (33%)
- Files:            50 MB  (11%)

Actions:
□ Upgrade storage
□ Delete old content
□ Archive unused items
```

## API Configuration

### API Keys

Generate and manage API keys:

```bash
# Create a new API key
curl -X POST https://hibi.is/api/v1/keys \
  -H "Authorization: Bearer SESSION_TOKEN" \
  -d '{
    "name": "My Application",
    "scopes": ["read", "write"],
    "expiresIn": "90d"
  }'
```

Response:
```json
{
  "key": "hb_live_abc123...",
  "name": "My Application",
  "scopes": ["read", "write"],
  "createdAt": "2024-01-15T10:30:00Z",
  "expiresAt": "2024-04-15T10:30:00Z"
}
```

{{< callout type="error" >}}
**Important:** Store your API key securely. It won't be shown again.
{{< /callout >}}

### API Scopes

Available permission scopes:

| Scope | Permissions |
|-------|-------------|
| `read` | Read content and metadata |
| `write` | Create and update content |
| `delete` | Delete content |
| `admin` | Full account access |
| `analytics` | Access analytics data |
| `webhooks` | Manage webhooks |

### Rate Limits

Understand API rate limits:

```plaintext
Default Limits:
- Free Plan:       60 requests/hour
- Pro Plan:        1000 requests/hour
- Enterprise:      10000 requests/hour

Per Endpoint:
- GET requests:    10 req/second
- POST requests:   5 req/second
- DELETE requests: 2 req/second
```

Check rate limit status:
```bash
curl -I https://hibi.is/api/v1/items

# Response headers:
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 995
X-RateLimit-Reset: 1642252800
```

## Webhook Configuration

### Setting Up Webhooks

Configure webhooks to receive real-time updates:

1. **Create Webhook:**
   ```bash
   curl -X POST https://hibi.is/api/v1/webhooks \
     -H "Authorization: Bearer YOUR_API_KEY" \
     -d '{
       "url": "https://your-server.com/webhook",
       "events": ["create", "update", "delete"],
       "secret": "your_secret_key"
     }'
   ```

2. **Verify Webhook:**
   - hibi.is sends a verification request
   - Respond with the challenge code
   - Webhook is activated

3. **Receive Events:**
   ```javascript
   app.post('/webhook', (req, res) => {
     const signature = req.headers['x-hibi-signature'];
     const payload = req.body;
     
     // Verify signature
     const expectedSignature = crypto
       .createHmac('sha256', WEBHOOK_SECRET)
       .update(JSON.stringify(payload))
       .digest('hex');
     
     if (signature === expectedSignature) {
       // Process event
       console.log('Event:', payload.event);
       console.log('Data:', payload.data);
     }
     
     res.status(200).send('OK');
   });
   ```

### Webhook Events

Available webhook events:

```yaml
Events:
  create:
    description: "Triggered when new content is created"
    payload:
      event: "create"
      data: { id, url, type, created_at }
  
  update:
    description: "Triggered when content is updated"
    payload:
      event: "update"
      data: { id, url, updated_at, changes }
  
  delete:
    description: "Triggered when content is deleted"
    payload:
      event: "delete"
      data: { id, deleted_at }
  
  share:
    description: "Triggered when content is shared"
    payload:
      event: "share"
      data: { id, shared_with, shared_at }
```

## Integration Settings

### Third-Party Integrations

Connect hibi.is with other services:

#### Slack Integration

```json
{
  "type": "slack",
  "config": {
    "webhookUrl": "https://hooks.slack.com/services/...",
    "channel": "#general",
    "events": ["create", "share"],
    "template": "New content: {{title}} - {{url}}"
  }
}
```

#### Discord Integration

```json
{
  "type": "discord",
  "config": {
    "webhookUrl": "https://discord.com/api/webhooks/...",
    "events": ["create"],
    "embedColor": "#5865F2"
  }
}
```

#### GitHub Integration

```json
{
  "type": "github",
  "config": {
    "token": "ghp_...",
    "repository": "owner/repo",
    "autoCreateGist": true,
    "syncOnUpdate": true
  }
}
```

## Custom Domain Configuration

### DNS Setup

Configure DNS for custom domains:

1. **Add CNAME Record:**
   ```dns
   Type: CNAME
   Name: docs (or @)
   Value: custom.hibi.is
   TTL: 3600
   ```

2. **Add Verification TXT Record:**
   ```dns
   Type: TXT
   Name: _hibi-verify
   Value: verification-token-here
   TTL: 3600
   ```

3. **Wait for Propagation:**
   - DNS changes can take 24-48 hours
   - Check status in hibi.is dashboard
   - SSL certificate auto-generated upon verification

### SSL/TLS Configuration

```yaml
ssl:
  autoRenew: true
  forceHttps: true
  tlsVersion: "1.3"
  hstsEnabled: true
  hstsMaxAge: 31536000
```

## Advanced Configuration

### Environment Variables

For self-hosted deployments:

```bash
# Core Settings
HIBI_BASE_URL=https://your-domain.com
HIBI_PORT=3000
HIBI_ENV=production

# Database
HIBI_DB_HOST=localhost
HIBI_DB_PORT=5432
HIBI_DB_NAME=hibi
HIBI_DB_USER=hibi_user
HIBI_DB_PASSWORD=secure_password

# Storage
HIBI_STORAGE_TYPE=s3  # local, s3, gcs
HIBI_STORAGE_BUCKET=my-bucket
HIBI_STORAGE_REGION=us-east-1

# Cache
HIBI_CACHE_TYPE=redis
HIBI_CACHE_HOST=localhost
HIBI_CACHE_PORT=6379

# Security
HIBI_SECRET_KEY=your-secret-key
HIBI_JWT_EXPIRATION=86400
HIBI_ENABLE_RATE_LIMIT=true

# Features
HIBI_ENABLE_ANALYTICS=true
HIBI_ENABLE_WEBHOOKS=true
HIBI_MAX_UPLOAD_SIZE=10485760  # 10MB
```

### Performance Tuning

Optimize performance settings:

```yaml
performance:
  cache:
    enabled: true
    ttl: 3600
    strategy: "lru"
    maxSize: "100MB"
  
  compression:
    enabled: true
    types: ["text", "json", "html"]
    level: 6
  
  rateLimit:
    enabled: true
    window: "15m"
    max: 100
  
  cdn:
    enabled: true
    provider: "cloudflare"
    zones: ["static", "media"]
```

## Backup Configuration

### Automatic Backups

```yaml
backups:
  enabled: true
  frequency: "daily"  # hourly, daily, weekly
  retention: 30       # days
  storage:
    type: "s3"
    bucket: "hibi-backups"
    region: "us-east-1"
  
  includeMetadata: true
  includeMedia: true
  compression: true
  encryption: true
```

### Manual Backup

```bash
# Export all content
curl https://hibi.is/api/v1/export \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -o backup.json

# Export specific items
curl "https://hibi.is/api/v1/export?ids=abc,def,ghi" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -o partial-backup.json
```

## Next Steps

{{< cards >}}
  {{< card link="/docs/troubleshooting" title="Troubleshooting" icon="exclamation-circle" >}}
  {{< card link="/docs/best-practices" title="Best Practices" icon="check-circle" >}}
  {{< card link="/api" title="API Reference" icon="code" >}}
{{< /cards >}}
