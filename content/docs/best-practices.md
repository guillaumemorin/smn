---
title: Best Practices
weight: 6
prev: /docs/troubleshooting
---

# Best Practices

Follow these guidelines to get the most out of hibi.is and ensure a smooth experience.

## Content Organization

### Use Meaningful Tags

Organize content with descriptive tags:

```javascript
// Good
createContent({
  content: "API documentation",
  tags: ["docs", "api", "v2", "reference"]
});

// Less helpful
createContent({
  content: "API documentation",
  tags: ["stuff", "thing", "doc"]
});
```

**Best Practices:**
- Use lowercase tags
- Keep tags short (1-2 words)
- Be consistent across content
- Create a tagging convention

**Example Convention:**
```plaintext
Type:     doc, code, note, link
Priority: high, medium, low
Project:  projectname
Status:   draft, review, published
```

### Folder Structure

Organize content hierarchically:

```plaintext
/
├── projects/
│   ├── project-a/
│   │   ├── docs/
│   │   ├── code/
│   │   └── assets/
│   └── project-b/
├── personal/
│   ├── notes/
│   └── bookmarks/
└── shared/
    └── team/
```

### Naming Conventions

Use clear, descriptive names:

```plaintext
✓ Good:
  - api-v2-documentation
  - user-authentication-flow
  - 2024-q1-planning-notes

✗ Avoid:
  - doc1
  - stuff
  - untitled-123
```

## Security Best Practices

### API Key Management

{{< callout type="error" >}}
**Never** commit API keys to version control or share them publicly.
{{< /callout >}}

**Do:**
```javascript
// Use environment variables
const apiKey = process.env.HIBI_API_KEY;

// Store in secure vaults
const apiKey = await secretManager.getSecret('hibi-api-key');
```

**Don't:**
```javascript
// Hard-code keys
const apiKey = 'hb_live_abc123...'; // ❌

// Commit to git
git add config.js  // Contains API keys ❌
```

### Key Rotation

Rotate API keys regularly:

1. **Generate new key** in settings
2. **Update applications** to use new key
3. **Test** that new key works
4. **Revoke old key** after transition period

```bash
# Automate key rotation
#!/bin/bash
NEW_KEY=$(curl -X POST https://hibi.is/api/v1/keys \
  -H "Authorization: Bearer $CURRENT_KEY" \
  | jq -r '.key')

# Update environment
echo "HIBI_API_KEY=$NEW_KEY" > .env

# Wait for propagation
sleep 300

# Revoke old key
curl -X DELETE https://hibi.is/api/v1/keys/$OLD_KEY_ID \
  -H "Authorization: Bearer $NEW_KEY"
```

### Content Privacy

Choose appropriate visibility:

| Content Type | Recommended Visibility |
|--------------|------------------------|
| Public blog posts | Public |
| Team documentation | Unlisted |
| Personal notes | Private |
| Temporary shares | Unlisted + Expiration |
| Sensitive data | Private + Encryption |

### Password Protection

Add passwords to sensitive content:

```javascript
createContent({
  content: "Sensitive information",
  visibility: "unlisted",
  password: "strong-unique-password",
  passwordHint: "Company motto"
});
```

**Password Guidelines:**
- Minimum 12 characters
- Mix of letters, numbers, symbols
- Unique per item
- Store securely

## Performance Optimization

### Optimize Content Size

**Images:**
```bash
# Compress images before upload
convert original.jpg -quality 85 -resize 1920x1080 optimized.jpg

# Use appropriate formats
- Photos: JPEG (quality 85)
- Graphics: PNG or WebP
- Icons: SVG
- Animations: WebP or compressed GIF
```

**Code:**
```bash
# Minify before uploading
terser script.js -o script.min.js
csso style.css -o style.min.css

# Remove comments in production
# Enable compression
```

**Text:**
```markdown
<!-- Use efficient formats -->
- Markdown for rich text
- Plain text for simple content
- JSON for structured data
```

### Caching Strategy

Set appropriate cache headers:

```javascript
createContent({
  content: "Static documentation",
  cache: {
    maxAge: 86400,              // 24 hours
    staleWhileRevalidate: 604800 // 7 days
  }
});
```

**Cache Duration Guidelines:**

| Content Type | Cache Duration |
|--------------|----------------|
| Static docs | 24 hours |
| Blog posts | 1 hour |
| API responses | 5 minutes |
| Real-time data | No cache |
| Assets (images) | 7 days |

### Batch Operations

Process multiple items efficiently:

```javascript
// ✓ Good: Batch create
const items = [...]; // Multiple items
await batchCreate(items);

// ✗ Avoid: Individual creates in loop
for (const item of items) {
  await createContent(item); // Many API calls
}
```

## Content Management

### Regular Cleanup

Schedule regular content reviews:

```javascript
// Archive old content
const oldItems = await findItems({
  createdBefore: '2023-01-01',
  accessed: { lessThan: 10 }
});

for (const item of oldItems) {
  await archiveContent(item.id);
}
```

**Cleanup Checklist:**
- [ ] Remove expired content
- [ ] Archive unused items
- [ ] Delete duplicates
- [ ] Update outdated information
- [ ] Review and update tags

### Version Control

Keep track of important changes:

```javascript
// Enable versioning for critical content
createContent({
  content: "Important document",
  versioning: {
    enabled: true,
    retainVersions: 10,
    autoSnapshot: 'daily'
  }
});
```

### Backup Strategy

**Recommended Approach:**

1. **Automated Daily Backups:**
   ```bash
   # Daily backup script
   #!/bin/bash
   DATE=$(date +%Y-%m-%d)
   curl https://hibi.is/api/v1/export \
     -H "Authorization: Bearer $API_KEY" \
     -o "backup-$DATE.json"
   
   # Retain 30 days
   find backups/ -name "backup-*.json" -mtime +30 -delete
   ```

2. **Weekly Full Exports:**
   ```bash
   # Weekly full export with media
   curl "https://hibi.is/api/v1/export?includeMedia=true" \
     -H "Authorization: Bearer $API_KEY" \
     -o "full-backup-$(date +%Y-%U).tar.gz"
   ```

3. **Store Off-Site:**
   ```bash
   # Upload to S3
   aws s3 cp backup-$DATE.json \
     s3://my-backups/hibi/$(date +%Y/%m/%d)/
   ```

## API Usage

### Error Handling

Implement robust error handling:

```javascript
async function createContentSafely(data) {
  try {
    const response = await fetch('https://hibi.is/api/v1/create', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${API_KEY}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(data)
    });
    
    if (!response.ok) {
      const error = await response.json();
      throw new Error(`API Error: ${error.message}`);
    }
    
    return await response.json();
  } catch (error) {
    console.error('Failed to create content:', error);
    
    // Retry logic
    if (isRetryable(error)) {
      await sleep(1000);
      return createContentSafely(data);
    }
    
    throw error;
  }
}
```

### Rate Limiting

Respect rate limits:

```javascript
// Implement rate limiter
class RateLimiter {
  constructor(maxRequests, perSeconds) {
    this.maxRequests = maxRequests;
    this.perSeconds = perSeconds;
    this.requests = [];
  }
  
  async acquire() {
    const now = Date.now();
    this.requests = this.requests.filter(
      time => now - time < this.perSeconds * 1000
    );
    
    if (this.requests.length >= this.maxRequests) {
      const oldestRequest = this.requests[0];
      const waitTime = this.perSeconds * 1000 - (now - oldestRequest);
      await sleep(waitTime);
      return this.acquire();
    }
    
    this.requests.push(now);
  }
}

// Usage
const limiter = new RateLimiter(10, 60); // 10 requests per minute

async function makeRequest(url) {
  await limiter.acquire();
  return fetch(url);
}
```

### Pagination

Handle large result sets efficiently:

```javascript
async function getAllItems() {
  const items = [];
  let page = 1;
  let hasMore = true;
  
  while (hasMore) {
    const response = await fetch(
      `https://hibi.is/api/v1/items?page=${page}&perPage=100`,
      { headers: { 'Authorization': `Bearer ${API_KEY}` } }
    );
    
    const data = await response.json();
    items.push(...data.items);
    
    hasMore = data.hasMore;
    page++;
    
    // Be nice to the API
    await sleep(100);
  }
  
  return items;
}
```

## Collaboration

### Team Workflows

**Documentation:**
```markdown
# Team Guidelines

## Content Creation
1. Draft in private workspace
2. Review with team
3. Publish to shared space
4. Notify stakeholders

## Naming Convention
project-name/category/document-name

## Tags
Use: team, project-name, status, priority
```

**Permissions:**
```javascript
// Grant appropriate access
shareContent({
  id: 'abc123',
  users: [
    { email: 'editor@team.com', role: 'editor' },
    { email: 'viewer@team.com', role: 'viewer' }
  ]
});
```

### Code Reviews

For shared code snippets:

```javascript
// Include context
createContent({
  type: 'code',
  language: 'javascript',
  content: codeSnippet,
  metadata: {
    purpose: 'Fix authentication bug',
    issue: '#123',
    reviewers: ['alice', 'bob'],
    status: 'review'
  }
});
```

## Monitoring

### Set Up Alerts

Monitor important content:

```javascript
// Alert on errors
webhook.on('error', async (event) => {
  await sendAlert({
    channel: '#ops',
    message: `Error in hibi.is: ${event.error.message}`,
    severity: 'high'
  });
});

// Alert on quota
webhook.on('quota.warning', async (event) => {
  await sendEmail({
    to: 'admin@company.com',
    subject: 'hibi.is storage warning',
    body: `Storage at ${event.usage}%`
  });
});
```

### Track Usage

Monitor your usage patterns:

```bash
# Monthly report
curl https://hibi.is/api/v1/analytics/summary?period=month \
  -H "Authorization: Bearer $API_KEY" \
  | jq '{
    items_created: .created,
    storage_used: .storage,
    api_calls: .apiCalls,
    popular_tags: .topTags
  }'
```

## Documentation

### Document Your Integration

Maintain integration documentation:

```markdown
# hibi.is Integration

## Setup
1. Create API key: https://hibi.is/settings/api
2. Add to environment: `HIBI_API_KEY=your_key`
3. Install client: `npm install hibi-client`

## Usage
\`\`\`javascript
const hibi = require('hibi-client');
hibi.create({ content: 'Hello' });
\`\`\`

## Troubleshooting
- Check API key is valid
- Verify network access
- Review rate limits
```

### Keep Examples Updated

```javascript
// ✓ Good: Current API version
// API v2.1.0
await hibi.create({ content: 'text' });

// Document deprecations
// ⚠️ Deprecated in v2.0, use create() instead
// await hibi.add({ content: 'text' });
```

## Testing

### Test Before Production

```javascript
// Use test mode
const hibi = new HibiClient({
  apiKey: process.env.HIBI_API_KEY,
  testMode: true  // Won't create real content
});

// Verify before deploying
await hibi.create({ content: 'test' });
console.log('Integration working!');
```

### Automated Testing

```javascript
// Integration tests
describe('hibi.is integration', () => {
  it('should create content', async () => {
    const result = await hibi.create({
      content: 'test content'
    });
    
    expect(result.url).toMatch(/^https:\/\/hibi\.is\//);
    expect(result.id).toBeTruthy();
  });
  
  it('should handle errors', async () => {
    await expect(
      hibi.create({ content: '' })
    ).rejects.toThrow('Content required');
  });
});
```

## Next Steps

{{< cards >}}
  {{< card link="/use-cases" title="Use Cases" icon="light-bulb" >}}
  {{< card link="/api" title="API Reference" icon="code" >}}
  {{< card link="/faq" title="FAQ" icon="question-mark-circle" >}}
{{< /cards >}}
