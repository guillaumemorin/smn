---
title: Integration Examples
weight: 3
prev: /use-cases/advanced-features
next: /use-cases/team-collaboration
---

# Integration Examples

Learn how to integrate hibi.is with popular tools and services to enhance your workflow.

## CI/CD Integrations

### GitHub Actions

Automatically publish documentation or release notes on deployment.

**Basic Example:**

```yaml
name: Publish Docs to hibi.is

on:
  push:
    branches: [main]
    paths:
      - 'docs/**'

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Publish Documentation
        run: |
          DOCS_CONTENT=$(cat docs/README.md)
          
          RESPONSE=$(curl -X POST https://hibi.is/api/v1/create \
            -H "Authorization: Bearer ${{ secrets.HIBI_API_KEY }}" \
            -H "Content-Type: application/json" \
            -d "{
              \"content\": \"$DOCS_CONTENT\",
              \"type\": \"markdown\",
              \"tags\": [\"docs\", \"auto-generated\"],
              \"visibility\": \"public\"
            }")
          
          echo "Published: $(echo $RESPONSE | jq -r '.url')"
```

**Advanced with Multiple Files:**

```yaml
name: Publish Multi-File Documentation

on:
  release:
    types: [published]

jobs:
  publish-docs:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Gather Documentation
        id: gather
        run: |
          # Combine multiple markdown files
          cat docs/*.md > combined-docs.md
          
          # Add release info
          echo "# Release ${{ github.event.release.tag_name }}" | cat - combined-docs.md > temp
          mv temp combined-docs.md
      
      - name: Publish to hibi.is
        run: |
          CONTENT=$(cat combined-docs.md | jq -Rs .)
          
          curl -X POST https://hibi.is/api/v1/create \
            -H "Authorization: Bearer ${{ secrets.HIBI_API_KEY }}" \
            -H "Content-Type: application/json" \
            -d "{
              \"content\": $CONTENT,
              \"type\": \"markdown\",
              \"tags\": [\"release\", \"${{ github.event.release.tag_name }}\"],
              \"visibility\": \"public\",
              \"metadata\": {
                \"version\": \"${{ github.event.release.tag_name }}\",
                \"date\": \"$(date -I)\"
              }
            }"
      
      - name: Update Release Notes
        run: |
          gh release edit ${{ github.event.release.tag_name }} \
            --notes "📖 Documentation: https://hibi.is/docs-${{ github.event.release.tag_name }}"
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### GitLab CI

```yaml
# .gitlab-ci.yml
publish_docs:
  stage: deploy
  script:
    - |
      curl -X POST https://hibi.is/api/v1/create \
        -H "Authorization: Bearer $HIBI_API_KEY" \
        -H "Content-Type: application/json" \
        -d "{
          \"content\": \"$(cat README.md)\",
          \"type\": \"markdown\",
          \"tags\": [\"gitlab\", \"$CI_COMMIT_REF_NAME\"],
          \"visibility\": \"public\"
        }"
  only:
    - main
```

### Jenkins

```groovy
pipeline {
    agent any
    
    stages {
        stage('Publish to hibi.is') {
            steps {
                script {
                    def content = readFile('docs/release-notes.md')
                    
                    sh """
                        curl -X POST https://hibi.is/api/v1/create \
                          -H "Authorization: Bearer ${env.HIBI_API_KEY}" \
                          -H "Content-Type: application/json" \
                          -d '{
                            "content": "${content}",
                            "type": "markdown",
                            "tags": ["jenkins", "${env.BUILD_NUMBER}"]
                          }'
                    """
                }
            }
        }
    }
}
```

## Communication Tools

### Slack Integration

**Incoming Webhook:**

```javascript
// slack-integration.js
const axios = require('axios');

async function shareToSlack(hibiUrl, title, description) {
  await axios.post(process.env.SLACK_WEBHOOK_URL, {
    blocks: [
      {
        type: 'header',
        text: {
          type: 'plain_text',
          text: '📄 New Content Shared'
        }
      },
      {
        type: 'section',
        text: {
          type: 'mrkdwn',
          text: `*${title}*\n${description}`
        },
        accessory: {
          type: 'button',
          text: {
            type: 'plain_text',
            text: 'View Content'
          },
          url: hibiUrl,
          action_id: 'view_content'
        }
      }
    ]
  });
}

// Usage
const result = await createOnHibi({
  content: 'Important announcement',
  type: 'text'
});

await shareToSlack(
  result.url,
  'Team Announcement',
  'Please review the latest updates'
);
```

**Slash Command:**

```javascript
// slack-slash-command.js
const express = require('express');
const axios = require('axios');

const app = express();
app.use(express.urlencoded({ extended: true }));

app.post('/hibi-create', async (req, res) => {
  const { text, user_name } = req.body;
  
  // Create content on hibi.is
  const response = await axios.post(
    'https://hibi.is/api/v1/create',
    {
      content: text,
      type: 'text',
      tags: ['slack', user_name],
      visibility: 'unlisted'
    },
    {
      headers: {
        'Authorization': `Bearer ${process.env.HIBI_API_KEY}`
      }
    }
  );
  
  // Respond to Slack
  res.json({
    response_type: 'in_channel',
    text: `✅ Created: ${response.data.url}`
  });
});

app.listen(3000);
```

### Discord Bot

```javascript
// discord-bot.js
const { Client, GatewayIntentBits } = require('discord.js');
const axios = require('axios');

const client = new Client({
  intents: [
    GatewayIntentBits.Guilds,
    GatewayIntentBits.GuildMessages,
    GatewayIntentBits.MessageContent
  ]
});

client.on('messageCreate', async (message) => {
  if (message.content.startsWith('!share ')) {
    const content = message.content.substring(7);
    
    try {
      // Create on hibi.is
      const response = await axios.post(
        'https://hibi.is/api/v1/create',
        {
          content: content,
          type: 'text',
          tags: ['discord', message.author.username],
          visibility: 'unlisted'
        },
        {
          headers: {
            'Authorization': `Bearer ${process.env.HIBI_API_KEY}`
          }
        }
      );
      
      // Reply with link
      message.reply(`✅ Shared: ${response.data.url}`);
    } catch (error) {
      message.reply('❌ Failed to share content');
    }
  }
});

client.login(process.env.DISCORD_TOKEN);
```

### Microsoft Teams

```javascript
// teams-webhook.js
async function postToTeams(hibiUrl, title, content) {
  const webhook = process.env.TEAMS_WEBHOOK_URL;
  
  await axios.post(webhook, {
    '@type': 'MessageCard',
    '@context': 'http://schema.org/extensions',
    themeColor: '0076D7',
    summary: title,
    sections: [{
      activityTitle: title,
      activitySubtitle: 'Shared on hibi.is',
      text: content,
      markdown: true
    }],
    potentialAction: [{
      '@type': 'OpenUri',
      name: 'View Content',
      targets: [{
        os: 'default',
        uri: hibiUrl
      }]
    }]
  });
}
```

## Development Tools

### VSCode Extension

```typescript
// extension.ts
import * as vscode from 'vscode';
import axios from 'axios';

export function activate(context: vscode.ExtensionContext) {
  const command = vscode.commands.registerCommand(
    'hibi.shareSelection',
    async () => {
      const editor = vscode.window.activeTextEditor;
      if (!editor) return;
      
      const selection = editor.document.getText(editor.selection);
      const language = editor.document.languageId;
      
      try {
        const response = await axios.post(
          'https://hibi.is/api/v1/create',
          {
            content: selection,
            type: 'code',
            language: language,
            visibility: 'unlisted'
          },
          {
            headers: {
              'Authorization': `Bearer ${getApiKey()}`
            }
          }
        );
        
        vscode.env.clipboard.writeText(response.data.url);
        vscode.window.showInformationMessage(
          `Shared to hibi.is (copied to clipboard)`
        );
      } catch (error) {
        vscode.window.showErrorMessage('Failed to share');
      }
    }
  );
  
  context.subscriptions.push(command);
}

function getApiKey(): string {
  return vscode.workspace
    .getConfiguration('hibi')
    .get('apiKey') || '';
}
```

### JetBrains Plugin

```kotlin
// ShareAction.kt
class ShareToHibiAction : AnAction() {
    override fun actionPerformed(e: AnActionEvent) {
        val editor = e.getData(CommonDataKeys.EDITOR) ?: return
        val selectionModel = editor.selectionModel
        val selectedText = selectionModel.selectedText ?: return
        
        val client = HttpClient.newHttpClient()
        val request = HttpRequest.newBuilder()
            .uri(URI.create("https://hibi.is/api/v1/create"))
            .header("Authorization", "Bearer ${getApiKey()}")
            .header("Content-Type", "application/json")
            .POST(HttpRequest.BodyPublishers.ofString("""
                {
                    "content": "${selectedText.replace("\"", "\\\"")}",
                    "type": "code",
                    "visibility": "unlisted"
                }
            """.trimIndent()))
            .build()
        
        client.sendAsync(request, HttpResponse.BodyHandlers.ofString())
            .thenApply { response ->
                val url = parseUrl(response.body())
                CopyPasteManager.getInstance().setContents(StringSelection(url))
                Notifications.Bus.notify(
                    Notification("hibi.is", "Shared", url, NotificationType.INFORMATION)
                )
            }
    }
}
```

## Project Management

### Jira Integration

```python
# jira-integration.py
from jira import JIRA
import requests
import os

jira = JIRA(
    server=os.getenv('JIRA_SERVER'),
    basic_auth=(os.getenv('JIRA_EMAIL'), os.getenv('JIRA_API_TOKEN'))
)

def share_issue_to_hibi(issue_key):
    issue = jira.issue(issue_key)
    
    # Format issue details
    content = f"""
# {issue.fields.summary}

**Status:** {issue.fields.status}
**Priority:** {issue.fields.priority}
**Assignee:** {issue.fields.assignee}

## Description
{issue.fields.description}

## Comments
"""
    
    for comment in issue.fields.comment.comments:
        content += f"\n**{comment.author}** ({comment.created}):\n{comment.body}\n"
    
    # Share on hibi.is
    response = requests.post(
        'https://hibi.is/api/v1/create',
        headers={'Authorization': f'Bearer {os.getenv("HIBI_API_KEY")}'},
        json={
            'content': content,
            'type': 'markdown',
            'tags': ['jira', issue_key],
            'visibility': 'unlisted'
        }
    )
    
    url = response.json()['url']
    
    # Add comment to Jira with link
    jira.add_comment(issue_key, f'Shared on hibi.is: {url}')
    
    return url
```

### Trello Integration

```javascript
// trello-integration.js
const Trello = require('trello');
const axios = require('axios');

const trello = new Trello(
  process.env.TRELLO_KEY,
  process.env.TRELLO_TOKEN
);

async function shareCardToHibi(cardId) {
  // Get card details
  const card = await trello.getCard(cardId);
  const checklists = await trello.getChecklistsOnCard(cardId);
  
  // Format content
  let content = `# ${card.name}\n\n${card.desc}\n\n`;
  
  for (const checklist of checklists) {
    content += `## ${checklist.name}\n`;
    for (const item of checklist.checkItems) {
      const checked = item.state === 'complete' ? 'x' : ' ';
      content += `- [${checked}] ${item.name}\n`;
    }
    content += '\n';
  }
  
  // Share on hibi.is
  const response = await axios.post(
    'https://hibi.is/api/v1/create',
    {
      content: content,
      type: 'markdown',
      tags: ['trello', card.idBoard],
      visibility: 'unlisted'
    },
    {
      headers: {
        'Authorization': `Bearer ${process.env.HIBI_API_KEY}`
      }
    }
  );
  
  // Add link to card
  await trello.addCommentToCard(
    cardId,
    `Shared on hibi.is: ${response.data.url}`
  );
  
  return response.data.url;
}
```

## Monitoring & Analytics

### Datadog Integration

```python
# datadog-integration.py
from datadog import initialize, api
import requests

initialize(
    api_key=os.getenv('DD_API_KEY'),
    app_key=os.getenv('DD_APP_KEY')
)

def share_incident_report(incident_id):
    # Get incident details from Datadog
    incident = api.Incident.get(incident_id)
    
    # Format report
    report = f"""
# Incident Report: {incident['attributes']['title']}

**Status:** {incident['attributes']['state']}
**Severity:** {incident['attributes']['severity']}
**Created:** {incident['attributes']['created']}

## Timeline
"""
    
    for event in incident['attributes']['timeline']:
        report += f"- {event['timestamp']}: {event['message']}\n"
    
    # Share on hibi.is
    response = requests.post(
        'https://hibi.is/api/v1/create',
        headers={'Authorization': f'Bearer {os.getenv("HIBI_API_KEY")}'},
        json={
            'content': report,
            'type': 'markdown',
            'tags': ['incident', 'datadog'],
            'visibility': 'private'
        }
    )
    
    # Update incident with link
    api.Incident.update(
        incident_id,
        fields={
            'additional_properties': {
                'hibi_url': response.json()['url']
            }
        }
    )
```

### Grafana Webhook

```javascript
// grafana-webhook.js
app.post('/grafana-alert', async (req, res) => {
  const alert = req.body;
  
  // Format alert
  const content = `
# Alert: ${alert.title}

**State:** ${alert.state}
**Message:** ${alert.message}

## Metrics
${alert.evalMatches.map(m => `- ${m.metric}: ${m.value}`).join('\n')}

**Dashboard:** ${alert.ruleUrl}
  `;
  
  // Share on hibi.is
  const response = await axios.post(
    'https://hibi.is/api/v1/create',
    {
      content: content,
      type: 'markdown',
      tags: ['alert', 'grafana'],
      visibility: 'private',
      expiresIn: 86400 // 24 hours
    },
    {
      headers: {
        'Authorization': `Bearer ${process.env.HIBI_API_KEY}`
      }
    }
  );
  
  // Send to Slack with link
  await notifySlack(alert.title, response.data.url);
  
  res.status(200).send('OK');
});
```

## Cloud Platforms

### AWS Lambda

```python
# lambda_function.py
import json
import boto3
import requests
import os

def lambda_handler(event, context):
    # Process S3 event or other trigger
    content = generate_report(event)
    
    # Share on hibi.is
    response = requests.post(
        'https://hibi.is/api/v1/create',
        headers={'Authorization': f'Bearer {os.environ["HIBI_API_KEY"]}'},
        json={
            'content': content,
            'type': 'markdown',
            'tags': ['aws', 'lambda'],
            'visibility': 'private'
        }
    )
    
    # Store URL in DynamoDB or send notification
    url = response.json()['url']
    send_sns_notification(url)
    
    return {
        'statusCode': 200,
        'body': json.dumps({'url': url})
    }
```

### Google Cloud Functions

```javascript
// index.js
const axios = require('axios');

exports.shareToHibi = async (req, res) => {
  const { content, type } = req.body;
  
  try {
    const response = await axios.post(
      'https://hibi.is/api/v1/create',
      {
        content: content,
        type: type || 'text',
        tags: ['gcp', 'cloud-function'],
        visibility: 'unlisted'
      },
      {
        headers: {
          'Authorization': `Bearer ${process.env.HIBI_API_KEY}`
        }
      }
    );
    
    res.json({ url: response.data.url });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};
```

## Next Steps

{{< cards >}}
  {{< card link="/use-cases/team-collaboration" title="Team Collaboration" icon="users" >}}
  {{< card link="/use-cases/automation" title="Automation" icon="cog" >}}
  {{< card link="/api" title="API Reference" icon="code" >}}
{{< /cards >}}
