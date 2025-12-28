---
title: Advanced Features
weight: 2
prev: /use-cases/basic-usage
next: /use-cases/integration
---

# Advanced Features Use Cases

Power user techniques and advanced workflows for making the most of hibi.is.

## Automation with API

### Automated Daily Backups

**Scenario:** Automatically backup important data to hibi.is daily.

```python
#!/usr/bin/env python3
import os
import json
import requests
from datetime import datetime

HIBI_API_KEY = os.getenv('HIBI_API_KEY')
HIBI_API_URL = 'https://hibi.is/api/v1'

def backup_data():
    # Gather data to backup
    data = {
        'date': datetime.now().isoformat(),
        'metrics': get_daily_metrics(),
        'logs': get_important_logs(),
        'status': get_system_status()
    }
    
    # Create backup on hibi.is
    response = requests.post(
        f'{HIBI_API_URL}/create',
        headers={
            'Authorization': f'Bearer {HIBI_API_KEY}',
            'Content-Type': 'application/json'
        },
        json={
            'content': json.dumps(data, indent=2),
            'type': 'json',
            'tags': ['backup', 'automated', datetime.now().strftime('%Y-%m')],
            'visibility': 'private',
            'metadata': {
                'backup_date': datetime.now().isoformat(),
                'retention_days': 30
            }
        }
    )
    
    if response.status_code == 201:
        backup_url = response.json()['url']
        print(f'Backup created: {backup_url}')
        notify_team(backup_url)
    else:
        print(f'Backup failed: {response.text}')
        alert_ops_team(response.text)

if __name__ == '__main__':
    backup_data()
```

**Cron Schedule:**
```bash
# Run daily at 2 AM
0 2 * * * /usr/local/bin/backup-to-hibi.py
```

### CI/CD Integration

**Scenario:** Automatically publish release notes on deployment.

```yaml
# .github/workflows/deploy.yml
name: Deploy and Publish Release Notes

on:
  push:
    tags:
      - 'v*'

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Generate Release Notes
        id: release_notes
        run: |
          NOTES=$(git log --pretty=format:"- %s" $(git describe --tags --abbrev=0 HEAD^)..HEAD)
          echo "notes<<EOF" >> $GITHUB_OUTPUT
          echo "$NOTES" >> $GITHUB_OUTPUT
          echo "EOF" >> $GITHUB_OUTPUT
      
      - name: Publish to hibi.is
        run: |
          RESPONSE=$(curl -X POST https://hibi.is/api/v1/create \
            -H "Authorization: Bearer ${{ secrets.HIBI_API_KEY }}" \
            -H "Content-Type: application/json" \
            -d '{
              "content": "# Release ${{ github.ref_name }}\n\n## Changes\n${{ steps.release_notes.outputs.notes }}",
              "type": "markdown",
              "tags": ["release", "${{ github.ref_name }}"],
              "visibility": "public"
            }')
          
          URL=$(echo $RESPONSE | jq -r '.url')
          echo "Release notes: $URL"
          
          # Update GitHub release with link
          gh release create ${{ github.ref_name }} \
            --notes "Full release notes: $URL"
```

## Webhook-Driven Workflows

### Slack Notifications

**Scenario:** Notify team in Slack when new content is shared.

```javascript
// webhook-handler.js
const express = require('express');
const crypto = require('crypto');
const axios = require('axios');

const app = express();
app.use(express.json());

const WEBHOOK_SECRET = process.env.WEBHOOK_SECRET;
const SLACK_WEBHOOK = process.env.SLACK_WEBHOOK_URL;

app.post('/hibi-webhook', async (req, res) => {
  // Verify webhook signature
  const signature = req.headers['x-hibi-signature'];
  const payload = JSON.stringify(req.body);
  
  const expectedSig = crypto
    .createHmac('sha256', WEBHOOK_SECRET)
    .update(payload)
    .digest('hex');
  
  if (signature !== expectedSig) {
    return res.status(401).send('Invalid signature');
  }
  
  // Process event
  const { event, data } = req.body;
  
  if (event === 'create' && data.tags?.includes('shared')) {
    await notifySlack(data);
  }
  
  res.status(200).send('OK');
});

async function notifySlack(data) {
  await axios.post(SLACK_WEBHOOK, {
    text: '📄 New content shared on hibi.is',
    attachments: [{
      title: data.title || 'Untitled',
      title_link: data.url,
      color: '#36a64f',
      fields: [
        {
          title: 'Type',
          value: data.type,
          short: true
        },
        {
          title: 'Tags',
          value: data.tags.join(', '),
          short: true
        },
        {
          title: 'Created by',
          value: data.author,
          short: true
        }
      ],
      footer: 'hibi.is',
      ts: Math.floor(Date.now() / 1000)
    }]
  });
}

app.listen(3000, () => {
  console.log('Webhook handler listening on port 3000');
});
```

### Auto-Archiving

**Scenario:** Automatically archive old, unused content.

```python
#!/usr/bin/env python3
import requests
from datetime import datetime, timedelta

API_KEY = os.getenv('HIBI_API_KEY')
API_URL = 'https://hibi.is/api/v1'

def archive_old_content():
    # Get items older than 90 days with low views
    cutoff_date = datetime.now() - timedelta(days=90)
    
    response = requests.get(
        f'{API_URL}/items',
        headers={'Authorization': f'Bearer {API_KEY}'},
        params={
            'createdBefore': cutoff_date.isoformat(),
            'maxViews': 10,
            'status': 'active'
        }
    )
    
    items = response.json()['items']
    
    for item in items:
        # Update to archived status
        requests.patch(
            f'{API_URL}/items/{item["id"]}',
            headers={'Authorization': f'Bearer {API_KEY}'},
            json={
                'status': 'archived',
                'tags': item['tags'] + ['auto-archived']
            }
        )
        
        print(f'Archived: {item["url"]} (created {item["createdAt"]})')

if __name__ == '__main__':
    archive_old_content()
```

## Batch Processing

### Bulk Content Migration

**Scenario:** Migrate existing content from another service.

```javascript
// migrate-content.js
const fs = require('fs');
const axios = require('axios');

const HIBI_API_KEY = process.env.HIBI_API_KEY;
const BATCH_SIZE = 10;

async function migrateContent(sourceFile) {
  const content = JSON.parse(fs.readFileSync(sourceFile));
  
  for (let i = 0; i < content.length; i += BATCH_SIZE) {
    const batch = content.slice(i, i + BATCH_SIZE);
    
    // Transform to hibi.is format
    const items = batch.map(item => ({
      content: item.body,
      type: detectType(item.content_type),
      tags: [...item.categories, 'migrated'],
      visibility: item.public ? 'public' : 'private',
      metadata: {
        originalId: item.id,
        originalDate: item.created_at,
        migrationDate: new Date().toISOString()
      }
    }));
    
    // Batch create
    const response = await axios.post(
      'https://hibi.is/api/v1/batch/create',
      { items },
      {
        headers: {
          'Authorization': `Bearer ${HIBI_API_KEY}`,
          'Content-Type': 'application/json'
        }
      }
    );
    
    console.log(`Migrated batch ${i / BATCH_SIZE + 1}: ${response.data.created} items`);
    
    // Save mapping for reference
    saveMigrationMapping(batch, response.data.items);
    
    // Rate limiting
    await sleep(1000);
  }
}

function detectType(contentType) {
  if (contentType.includes('code')) return 'code';
  if (contentType.includes('markdown')) return 'markdown';
  return 'text';
}

function saveMigrationMapping(original, migrated) {
  const mapping = original.map((orig, idx) => ({
    oldId: orig.id,
    oldUrl: orig.url,
    newId: migrated[idx].id,
    newUrl: migrated[idx].url
  }));
  
  fs.appendFileSync(
    'migration-map.json',
    JSON.stringify(mapping, null, 2) + ',\n'
  );
}

migrateContent('export.json');
```

## Advanced Sharing

### Expiring Shares with Notification

**Scenario:** Share content that auto-expires and notifies you before deletion.

```javascript
async function createExpiringShare(content, hours = 24) {
  const expiresAt = new Date(Date.now() + hours * 3600000);
  const notifyBefore = 3600; // 1 hour before expiration
  
  const response = await fetch('https://hibi.is/api/v1/create', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${API_KEY}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      content: content,
      visibility: 'unlisted',
      expiresAt: expiresAt.toISOString(),
      notifications: {
        beforeExpiration: notifyBefore,
        channels: ['email', 'webhook']
      },
      metadata: {
        purpose: 'temporary share',
        shareType: 'expiring'
      }
    })
  });
  
  const data = await response.json();
  
  console.log(`
    Content created: ${data.url}
    Expires at: ${expiresAt}
    You'll be notified 1 hour before expiration
  `);
  
  return data;
}

// Usage
createExpiringShare('Confidential meeting notes', 24);
```

### Password-Protected Shares

**Scenario:** Share sensitive content with password protection.

```bash
#!/bin/bash

# Generate strong password
PASSWORD=$(openssl rand -base64 32)

# Create password-protected content
RESPONSE=$(curl -X POST https://hibi.is/api/v1/create \
  -H "Authorization: Bearer $HIBI_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{
    \"content\": \"$(cat sensitive-doc.md)\",
    \"type\": \"markdown\",
    \"visibility\": \"unlisted\",
    \"password\": \"$PASSWORD\",
    \"passwordHint\": \"Shared in team meeting\"
  }")

URL=$(echo $RESPONSE | jq -r '.url')

# Send link and password separately
echo "Link: $URL" | mail -s "Shared Document" recipient@example.com
sleep 60
echo "Password: $PASSWORD" | mail -s "Password for Shared Document" recipient@example.com

echo "Shared securely: $URL"
```

## Custom Integrations

### Browser Extension

**Scenario:** Quick-share from any webpage.

```javascript
// content-script.js
chrome.runtime.onMessage.addListener((request, sender, sendResponse) => {
  if (request.action === 'shareSelection') {
    const selection = window.getSelection().toString();
    
    if (selection) {
      shareToHibi(selection, window.location.href);
    }
  }
});

async function shareToHibi(text, sourceUrl) {
  const response = await fetch('https://hibi.is/api/v1/create', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${await getApiKey()}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      content: text,
      type: 'text',
      metadata: {
        source: sourceUrl,
        timestamp: new Date().toISOString()
      },
      tags: ['web-clip', getDomain(sourceUrl)]
    })
  });
  
  const data = await response.json();
  
  // Show notification
  chrome.notifications.create({
    type: 'basic',
    iconUrl: 'icon.png',
    title: 'Shared to hibi.is',
    message: `Link: ${data.url}`
  });
}
```

### Mobile Shortcut (iOS)

**Scenario:** Share from iOS using Shortcuts app.

```plaintext
Shortcut Configuration:
1. Receive "Text" from Share Sheet
2. Get "API Key" from Secure Storage
3. URL: https://hibi.is/api/v1/create
4. Method: POST
5. Headers:
   - Authorization: Bearer [API Key]
   - Content-Type: application/json
6. Body:
   {
     "content": [Shortcut Input],
     "type": "text",
     "tags": ["mobile", "ios"],
     "metadata": {
       "source": "iOS Shortcut",
       "timestamp": "[Current Date]"
     }
   }
7. Get "url" from JSON Response
8. Copy to Clipboard
9. Show Alert: "Shared to hibi.is"
```

## Analytics and Monitoring

### Usage Dashboard

**Scenario:** Create a dashboard to monitor content usage.

```python
#!/usr/bin/env python3
import requests
import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime, timedelta

API_KEY = os.getenv('HIBI_API_KEY')

def generate_usage_report():
    # Get analytics data
    response = requests.get(
        'https://hibi.is/api/v1/analytics/summary',
        headers={'Authorization': f'Bearer {API_KEY}'},
        params={
            'period': 'month',
            'groupBy': 'day'
        }
    )
    
    data = response.json()
    
    # Create DataFrame
    df = pd.DataFrame(data['daily'])
    df['date'] = pd.to_datetime(df['date'])
    
    # Generate visualizations
    fig, axes = plt.subplots(2, 2, figsize=(15, 10))
    
    # Views over time
    axes[0, 0].plot(df['date'], df['views'])
    axes[0, 0].set_title('Daily Views')
    axes[0, 0].set_xlabel('Date')
    axes[0, 0].set_ylabel('Views')
    
    # Content created
    axes[0, 1].bar(df['date'], df['created'])
    axes[0, 1].set_title('Content Created')
    axes[0, 1].set_xlabel('Date')
    axes[0, 1].set_ylabel('Count')
    
    # Popular tags
    tag_data = get_popular_tags()
    axes[1, 0].barh(tag_data['tags'], tag_data['counts'])
    axes[1, 0].set_title('Popular Tags')
    axes[1, 0].set_xlabel('Usage Count')
    
    # Storage usage
    storage = data['storage']
    axes[1, 1].pie(
        [storage['used'], storage['total'] - storage['used']],
        labels=['Used', 'Available'],
        autopct='%1.1f%%'
    )
    axes[1, 1].set_title('Storage Usage')
    
    plt.tight_layout()
    plt.savefig('hibi-usage-report.png')
    
    # Upload report to hibi.is
    upload_report('hibi-usage-report.png')

def get_popular_tags():
    response = requests.get(
        'https://hibi.is/api/v1/analytics/tags',
        headers={'Authorization': f'Bearer {API_KEY}'},
        params={'limit': 10}
    )
    return response.json()

def upload_report(filename):
    with open(filename, 'rb') as f:
        response = requests.post(
            'https://hibi.is/api/v1/upload',
            headers={'Authorization': f'Bearer {API_KEY}'},
            files={'file': f},
            data={
                'type': 'image',
                'tags': ['analytics', 'report', 'auto-generated'],
                'visibility': 'private'
            }
        )
    
    print(f'Report uploaded: {response.json()["url"]}')

if __name__ == '__main__':
    generate_usage_report()
```

## Next Steps

{{< cards >}}
  {{< card link="/use-cases/integration" title="Integration Examples" icon="puzzle" >}}
  {{< card link="/use-cases/automation" title="Automation Workflows" icon="cog" >}}
  {{< card link="/api" title="API Reference" icon="code" >}}
{{< /cards >}}
