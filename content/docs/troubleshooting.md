---
title: Troubleshooting
weight: 5
prev: /docs/configuration
next: /docs/best-practices
---

# Troubleshooting Guide

Common issues and their solutions to help you get back on track quickly.

## Common Issues

### Content Not Loading

**Symptom:** Content page shows a blank screen or loading indicator

**Solutions:**

1. **Check URL:** Ensure the URL is correct and complete
   ```plaintext
   ✓ Correct: https://hibi.is/abc123
   ✗ Incorrect: https://hibi.is/abc123/
   ```

2. **Clear Browser Cache:**
   - Chrome: `Ctrl+Shift+Del` (Windows) or `Cmd+Shift+Del` (Mac)
   - Firefox: `Ctrl+Shift+Del` (Windows) or `Cmd+Shift+Del` (Mac)
   - Safari: `Cmd+Option+E`

3. **Try Incognito/Private Mode:**
   - Helps identify browser extension conflicts
   - Rules out cache issues

4. **Check Content Status:**
   ```bash
   curl -I https://hibi.is/abc123
   # Look for HTTP status code
   # 200 = OK
   # 404 = Not found
   # 410 = Expired/Deleted
   ```

{{< callout type="info" >}}
If you get a 410 error, the content has expired or been deleted and cannot be recovered.
{{< /callout >}}

### API Errors

#### 401 Unauthorized

**Cause:** Invalid or missing API key

**Solution:**
```bash
# Verify your API key is correct
curl https://hibi.is/api/v1/auth/verify \
  -H "Authorization: Bearer YOUR_API_KEY"

# Generate a new API key if needed
# Visit: https://hibi.is/settings/api
```

#### 429 Rate Limit Exceeded

**Cause:** Too many requests in a short period

**Solution:**
```javascript
// Implement exponential backoff
async function makeRequest(url, retries = 3) {
  try {
    const response = await fetch(url);
    if (response.status === 429) {
      const retryAfter = response.headers.get('Retry-After');
      await sleep(retryAfter * 1000);
      return makeRequest(url, retries - 1);
    }
    return response;
  } catch (error) {
    if (retries > 0) {
      await sleep(1000 * (4 - retries));
      return makeRequest(url, retries - 1);
    }
    throw error;
  }
}
```

#### 413 Payload Too Large

**Cause:** Upload size exceeds limit

**Solution:**
- **Free Plan:** Max 5MB per upload
- **Pro Plan:** Max 50MB per upload
- **Compress files** before uploading
- **Split large files** into smaller chunks

```bash
# Check file size
ls -lh file.txt

# Compress file
gzip file.txt
```

### Upload Issues

**Symptom:** File upload fails or gets stuck

**Checklist:**

1. ✅ Check file size limits
2. ✅ Verify file type is supported
3. ✅ Ensure stable internet connection
4. ✅ Disable browser extensions
5. ✅ Try a different browser

**Supported File Types:**
```plaintext
Documents: .pdf, .doc, .docx, .txt, .md
Images:    .jpg, .png, .gif, .webp, .svg
Code:      .js, .py, .java, .go, .rs, .c, .cpp
Archives:  .zip, .tar, .gz, .7z
```

### Sharing Problems

#### Link Not Working

**Symptoms:**
- Recipients can't access shared content
- Permission denied errors

**Solutions:**

1. **Check Visibility Settings:**
   ```plaintext
   Private → Only you can access
   Unlisted → Anyone with link can access
   Public → Everyone can access
   ```

2. **Verify Expiration:**
   ```bash
   curl https://hibi.is/api/v1/items/abc123 \
     -H "Authorization: Bearer YOUR_API_KEY" | jq '.expiresAt'
   ```

3. **Check Share Permissions:**
   - Ensure recipient has correct permissions
   - Verify email address is correct
   - Check spam folder for share notifications

### Performance Issues

#### Slow Loading

**Possible Causes:**
- Large file sizes
- Slow network connection
- Server load
- Browser issues

**Solutions:**

1. **Optimize Content:**
   ```bash
   # Compress images
   convert large-image.jpg -quality 85 optimized-image.jpg
   
   # Minify code
   terser input.js -o output.min.js
   ```

2. **Use CDN:**
   - Enable CDN in settings
   - Benefits from edge caching
   - Faster global delivery

3. **Check Network:**
   ```bash
   # Test connection speed
   curl -w "@curl-format.txt" -o /dev/null -s https://hibi.is
   
   # curl-format.txt:
   time_namelookup:  %{time_namelookup}\n
   time_connect:     %{time_connect}\n
   time_starttransfer: %{time_starttransfer}\n
   time_total:       %{time_total}\n
   ```

### Account Issues

#### Can't Log In

**Solutions:**

1. **Reset Password:**
   - Visit [hibi.is/reset-password](https://hibi.is/reset-password)
   - Enter your email
   - Check inbox for reset link

2. **Check Email:**
   - Verify email address is correct
   - Check spam/junk folder
   - Add noreply@hibi.is to contacts

3. **Clear Cookies:**
   ```javascript
   // In browser console
   document.cookie.split(";").forEach(c => {
     document.cookie = c.replace(/^ +/, "")
       .replace(/=.*/, "=;expires=" + new Date().toUTCString() + ";path=/");
   });
   ```

#### Account Locked

**Cause:** Multiple failed login attempts

**Solution:**
- Wait 15 minutes before trying again
- Contact support if issue persists
- Use password reset if you forgot credentials

### Integration Issues

#### Webhook Not Receiving Events

**Debugging Steps:**

1. **Verify Webhook URL:**
   ```bash
   # Test webhook endpoint
   curl -X POST https://your-server.com/webhook \
     -H "Content-Type: application/json" \
     -d '{"test": true}'
   ```

2. **Check Webhook Logs:**
   ```bash
   curl https://hibi.is/api/v1/webhooks/webhook-id/logs \
     -H "Authorization: Bearer YOUR_API_KEY"
   ```

3. **Verify SSL Certificate:**
   ```bash
   curl -I https://your-server.com/webhook
   # Webhook endpoint must have valid SSL
   ```

4. **Check Firewall:**
   - Allow incoming requests from hibi.is IPs
   - Ensure port is open (usually 443 for HTTPS)

**Example Webhook Handler:**
```javascript
const express = require('express');
const crypto = require('crypto');

app.post('/webhook', express.json(), (req, res) => {
  const signature = req.headers['x-hibi-signature'];
  const payload = JSON.stringify(req.body);
  
  // Verify signature
  const expectedSignature = crypto
    .createHmac('sha256', process.env.WEBHOOK_SECRET)
    .update(payload)
    .digest('hex');
  
  if (signature !== expectedSignature) {
    return res.status(401).send('Invalid signature');
  }
  
  // Process event
  console.log('Received event:', req.body.event);
  
  // Respond quickly (within 5 seconds)
  res.status(200).send('OK');
  
  // Process async
  processEvent(req.body);
});
```

## Error Messages

### Common Error Codes

| Code | Message | Solution |
|------|---------|----------|
| 400 | Bad Request | Check request format and parameters |
| 401 | Unauthorized | Verify API key or login credentials |
| 403 | Forbidden | Check permissions and access rights |
| 404 | Not Found | Verify resource ID or URL |
| 410 | Gone | Content has expired or been deleted |
| 413 | Payload Too Large | Reduce file size or upgrade plan |
| 422 | Validation Error | Check input data format |
| 429 | Rate Limit Exceeded | Slow down requests or upgrade plan |
| 500 | Internal Server Error | Try again or contact support |
| 503 | Service Unavailable | Server maintenance, try again later |

### Error Response Format

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": {
      "field": "email",
      "issue": "Invalid email format"
    },
    "timestamp": "2024-01-15T10:30:00Z",
    "requestId": "req_abc123"
  }
}
```

## Browser-Specific Issues

### Chrome

**Issue:** Content not displaying correctly

**Solution:**
```plaintext
1. Clear cache: chrome://settings/clearBrowserData
2. Disable extensions: chrome://extensions
3. Reset settings: chrome://settings/reset
4. Update Chrome to latest version
```

### Firefox

**Issue:** Upload progress not showing

**Solution:**
```plaintext
1. Clear cache: about:preferences#privacy
2. Disable tracking protection for hibi.is
3. Check console: Ctrl+Shift+K
4. Update Firefox to latest version
```

### Safari

**Issue:** Can't upload files

**Solution:**
```plaintext
1. Check Privacy settings
2. Allow pop-ups for hibi.is
3. Clear website data: Safari → Preferences → Privacy
4. Update Safari to latest version
```

## Debug Mode

Enable debug mode for detailed logging:

```javascript
// In browser console
localStorage.setItem('hibi_debug', 'true');
location.reload();

// View debug logs
localStorage.getItem('hibi_debug_logs');
```

## Getting Help

If you're still experiencing issues:

### Self-Service Resources

1. **Search Documentation:** Use the search bar above
2. **FAQ:** Check [Frequently Asked Questions](/faq)
3. **Status Page:** Visit [status.hibi.is](https://status.hibi.is)
4. **Community Forum:** [GitHub Discussions](https://github.com/guillaumemorin/hibi/discussions)

### Contact Support

{{< tabs items="Email,GitHub,Chat" >}}

{{< tab >}}
**Email Support**

- **Free Plan:** Community support only
- **Pro Plan:** support@hibi.is (24-48h response)
- **Enterprise:** priority@hibi.is (4h response)

Include in your email:
- Account email
- Description of issue
- Steps to reproduce
- Screenshots if applicable
- Browser and OS version
{{< /tab >}}

{{< tab >}}
**GitHub Issues**

For bug reports:
1. Search existing issues
2. Create new issue if not found
3. Use issue template
4. Provide reproduction steps
5. Include error messages

[Create Issue →](https://github.com/guillaumemorin/hibi/issues/new)
{{< /tab >}}

{{< tab >}}
**Live Chat**

Available for Pro and Enterprise:
- Monday-Friday, 9 AM - 5 PM EST
- Average response time: 5 minutes
- Access via dashboard

[Open Chat →](https://hibi.is/support/chat)
{{< /tab >}}

{{< /tabs >}}

## System Status

Check current system status:

```bash
# API health check
curl https://hibi.is/api/v1/health

# Response:
{
  "status": "healthy",
  "version": "2.1.0",
  "uptime": 99.99,
  "services": {
    "api": "operational",
    "storage": "operational",
    "database": "operational"
  }
}
```

## Next Steps

{{< cards >}}
  {{< card link="/docs/best-practices" title="Best Practices" icon="check-circle" >}}
  {{< card link="/faq" title="FAQ" icon="question-mark-circle" >}}
  {{< card link="/api" title="API Reference" icon="code" >}}
{{< /cards >}}
