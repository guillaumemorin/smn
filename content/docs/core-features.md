---
title: Core Features
weight: 2
prev: /docs/getting-started
next: /docs/advanced
---

# Core Features

hibi.is offers a comprehensive set of features designed to enhance your productivity and streamline your workflow.

## Main Features

### 1. Content Management

Create, store, and manage your content with ease.

{{< callout type="info" >}}
All content is stored securely and can be accessed via unique URLs.
{{< /callout >}}

**Key capabilities:**
- Quick content creation
- Unique URL generation
- Persistent storage
- Easy sharing

### 2. URL Generation

Every piece of content gets a unique, short URL that's easy to share and remember.

```plaintext
Example URL format:
https://hibi.is/[unique-id]
```

**Features:**
- Short, memorable URLs
- Custom URL options (if available)
- Permanent links
- No expiration (configurable)

### 3. Privacy & Security

Your content is protected with industry-standard security measures.

- 🔒 Encrypted connections (HTTPS)
- 🛡️ Privacy-first approach
- 🔐 Optional password protection
- 👁️ Configurable visibility settings

### 4. Sharing Options

Multiple ways to share your content:

{{< tabs items="Direct Link,Embed,QR Code,Social Media" >}}

{{< tab >}}
**Direct Link**

Copy and paste the URL directly:
```
https://hibi.is/abc123
```
{{< /tab >}}

{{< tab >}}
**Embed**

Embed content in your website:
```html
<iframe src="https://hibi.is/abc123/embed"></iframe>
```
{{< /tab >}}

{{< tab >}}
**QR Code**

Generate a QR code for easy mobile access:
- Visit your content URL
- Click "QR Code" option
- Share or download the QR code
{{< /tab >}}

{{< tab >}}
**Social Media**

Share directly to social platforms:
- Twitter/X
- Facebook
- LinkedIn
- Email
{{< /tab >}}

{{< /tabs >}}

### 5. Content Types

Support for various content types:

| Type | Description | Use Case |
|------|-------------|----------|
| Text | Plain text content | Notes, snippets, messages |
| Markdown | Formatted text | Documentation, articles |
| Code | Syntax-highlighted code | Code sharing, snippets |
| Links | URL management | Link shortening |
| Files | File uploads | Document sharing |

### 6. Search & Discovery

Find your content quickly:

- **Quick Search:** Search bar in the navigation
- **Filters:** Filter by date, type, or tags
- **Tags:** Organize with custom tags
- **Recent Items:** Access recently created content

### 7. Analytics (Optional)

Track how your content is being used:

- View counts
- Geographic distribution
- Referrer information
- Time-based analytics

{{< callout type="warning" >}}
Analytics features may require opt-in or specific plan levels.
{{< /callout >}}

## Feature Comparison

Different plans may offer different features:

| Feature | Free | Pro | Enterprise |
|---------|------|-----|------------|
| Content Creation | ✅ | ✅ | ✅ |
| Custom URLs | ❌ | ✅ | ✅ |
| Password Protection | ❌ | ✅ | ✅ |
| Analytics | Basic | Advanced | Enterprise |
| API Access | Limited | Full | Full |
| Storage Limit | 100 items | Unlimited | Unlimited |
| Support | Community | Email | Priority |

## Best Practices

To get the most out of hibi.is features:

1. **Use Descriptive Tags:** Help organize and find your content later
2. **Set Appropriate Privacy:** Choose the right visibility for your content
3. **Regular Cleanup:** Remove old or unused content
4. **Leverage Templates:** Create reusable templates for common tasks
5. **Enable Notifications:** Stay informed about important updates

## Next Steps

{{< cards >}}
  {{< card link="/docs/advanced" title="Advanced Usage" icon="sparkles" >}}
  {{< card link="/docs/configuration" title="Configuration" icon="cog" >}}
  {{< card link="/api" title="API Reference" icon="code" >}}
{{< /cards >}}
