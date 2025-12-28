---
title: Basic Usage
weight: 1
prev: /use-cases
next: /use-cases/advanced-features
---

# Basic Usage Examples

Simple, everyday use cases for hibi.is that anyone can implement immediately.

## Quick Text Sharing

### Share Meeting Notes

**Scenario:** You need to quickly share meeting notes with attendees.

**Steps:**
1. Visit hibi.is
2. Paste your notes
3. Click Create
4. Share the generated link

**Example:**
```markdown
# Team Standup - Jan 15, 2024

**Yesterday:**
- Completed user authentication
- Fixed bug #245

**Today:**
- Working on dashboard UI
- Code review

**Blockers:**
- None

Generated link: https://hibi.is/standup-123
```

### Share Quick Ideas

**Scenario:** You have an idea and want to share it quickly.

```plaintext
Product Idea: Dark Mode Toggle

Users are requesting a dark mode option.
Suggested implementation:
- Toggle in settings
- Persist preference
- Apply across all pages

Priority: Medium
Effort: 2 days

Share: https://hibi.is/idea-456
```

## Code Snippet Sharing

### Share a Bug Fix

**Scenario:** You found a bug and want to share the fix with your team.

```javascript
// Before (buggy code)
function calculateTotal(items) {
  return items.reduce((sum, item) => sum + item.price);
  // Missing initial value - returns NaN for empty arrays
}

// After (fixed)
function calculateTotal(items) {
  return items.reduce((sum, item) => sum + item.price, 0);
  // Now correctly returns 0 for empty arrays
}

// Share: https://hibi.is/bugfix-789
```

### Share Configuration

**Scenario:** Share environment configuration with a colleague.

```bash
# Development Environment Setup

# Node version
node -v  # v18.17.0

# Environment variables
export API_URL=http://localhost:3000
export DB_HOST=localhost
export DB_PORT=5432
export DEBUG=true

# Install dependencies
npm install

# Run development server
npm run dev

Share: https://hibi.is/config-abc
```

## Link Management

### Shorten Long URLs

**Scenario:** You have a long URL that's hard to share.

```plaintext
Original URL:
https://docs.example.com/api/v2/reference/authentication/oauth2/grant-types/authorization-code?client_id=123&redirect_uri=https://app.example.com/callback&scope=read%20write&state=abc123

Shortened:
https://hibi.is/auth-docs

Benefits:
✓ Easier to share
✓ Cleaner appearance
✓ Trackable (with analytics)
✓ Memorable
```

### Create Resource Lists

**Scenario:** Curate and share a list of useful resources.

```markdown
# Useful Developer Resources

## Learning
- [MDN Web Docs](https://developer.mozilla.org/)
- [freeCodeCamp](https://www.freecodecamp.org/)
- [Codecademy](https://www.codecademy.com/)

## Tools
- [VS Code](https://code.visualstudio.com/)
- [Postman](https://www.postman.com/)
- [GitHub](https://github.com/)

## Communities
- [Stack Overflow](https://stackoverflow.com/)
- [Dev.to](https://dev.to/)
- [Reddit r/programming](https://reddit.com/r/programming)

Share: https://hibi.is/resources-def
```

## Document Sharing

### Share Instructions

**Scenario:** Create step-by-step instructions for a task.

```markdown
# How to Deploy to Production

## Prerequisites
- Access to production server
- Latest code from main branch
- Environment variables configured

## Steps

1. **Pull latest code:**
   ```bash
   git checkout main
   git pull origin main
   ```

2. **Run tests:**
   ```bash
   npm test
   ```

3. **Build for production:**
   ```bash
   npm run build
   ```

4. **Deploy:**
   ```bash
   npm run deploy:prod
   ```

5. **Verify deployment:**
   - Check https://app.example.com
   - Run smoke tests
   - Monitor logs

## Rollback
If issues occur:
```bash
npm run deploy:rollback
```

Share: https://hibi.is/deploy-guide
```

### Share Templates

**Scenario:** Create reusable templates for common tasks.

```markdown
# Bug Report Template

**Title:** [Brief description of the bug]

**Environment:**
- OS: [e.g., Windows 11, macOS 14]
- Browser: [e.g., Chrome 120]
- Version: [e.g., 2.1.0]

**Steps to Reproduce:**
1. Go to '...'
2. Click on '...'
3. Scroll down to '...'
4. See error

**Expected Behavior:**
[What should happen]

**Actual Behavior:**
[What actually happens]

**Screenshots:**
[If applicable]

**Additional Context:**
[Any other relevant information]

Share: https://hibi.is/bug-template
```

## Personal Use Cases

### Daily Journal

```markdown
# Daily Journal - 2024-01-15

**Mood:** 😊

**Highlights:**
- Finished the new feature
- Great feedback from client
- Learned about WebSockets

**Challenges:**
- Debugging took longer than expected
- Need to improve test coverage

**Tomorrow:**
- Code review
- Start next sprint
- Team meeting at 2 PM

**Grateful for:**
- Supportive team
- Challenging problems
- Growth opportunities

Private link: https://hibi.is/journal-jan15
```

### Reading List

```markdown
# Reading List

## Currently Reading
- "Clean Code" by Robert Martin
- "The Pragmatic Programmer"

## Want to Read
- "System Design Interview"
- "Designing Data-Intensive Applications"
- "The Phoenix Project"

## Completed
- ✅ "Refactoring" by Martin Fowler
- ✅ "JavaScript: The Good Parts"
- ✅ "Don't Make Me Think"

## Articles Saved
- [How to Write Good Tests](https://example.com/article1)
- [Microservices Best Practices](https://example.com/article2)
- [TypeScript Tips](https://example.com/article3)

Share: https://hibi.is/reading-list
```

### Recipe Collection

```markdown
# Favorite Recipes

## Quick Weeknight Dinners

### Pasta Aglio e Olio (15 min)
**Ingredients:**
- 400g spaghetti
- 6 cloves garlic, sliced
- 1/2 cup olive oil
- Red pepper flakes
- Parsley, chopped
- Parmesan cheese

**Instructions:**
1. Cook pasta in salted water
2. Sauté garlic in olive oil
3. Add red pepper flakes
4. Toss with pasta
5. Top with parsley and parmesan

### Stir-Fry Vegetables (20 min)
**Ingredients:**
- Mixed vegetables
- Soy sauce
- Garlic and ginger
- Sesame oil
- Rice

**Instructions:**
1. Prep vegetables
2. Heat wok with oil
3. Stir-fry in order: aromatics, hard veggies, soft veggies
4. Add sauce
5. Serve over rice

Share: https://hibi.is/recipes
```

## Educational Use

### Study Notes

```markdown
# JavaScript ES6 Features - Study Notes

## Arrow Functions
```javascript
// Traditional
function add(a, b) {
  return a + b;
}

// Arrow function
const add = (a, b) => a + b;
```

## Destructuring
```javascript
// Array destructuring
const [first, second] = [1, 2, 3];

// Object destructuring
const { name, age } = person;
```

## Template Literals
```javascript
const greeting = `Hello, ${name}!`;
```

## Spread Operator
```javascript
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5];
```

Share for study group: https://hibi.is/es6-notes
```

### Practice Problems

```markdown
# Coding Practice Problems

## Problem 1: Two Sum
**Difficulty:** Easy

Given an array of integers and a target sum, return indices of two numbers that add up to target.

**Example:**
Input: nums = [2, 7, 11, 15], target = 9
Output: [0, 1] (because nums[0] + nums[1] = 9)

**My Solution:**
```javascript
function twoSum(nums, target) {
  const map = new Map();
  for (let i = 0; i < nums.length; i++) {
    const complement = target - nums[i];
    if (map.has(complement)) {
      return [map.get(complement), i];
    }
    map.set(nums[i], i);
  }
  return [];
}
```

**Time Complexity:** O(n)
**Space Complexity:** O(n)

Share: https://hibi.is/practice-twosum
```

## Team Collaboration

### Shared Checklist

```markdown
# Sprint Planning Checklist

## Preparation
- [x] Review backlog
- [x] Estimate stories
- [ ] Prepare sprint goals
- [ ] Set up sprint board

## During Sprint Planning
- [ ] Present sprint goal
- [ ] Review user stories
- [ ] Assign tasks
- [ ] Set sprint commitment

## Post-Planning
- [ ] Update JIRA
- [ ] Send summary email
- [ ] Schedule daily standups
- [ ] Create sprint retrospective

Team access: https://hibi.is/sprint-checklist
```

### Team Resources

```markdown
# Team Resources & Links

## Project Links
- [Staging](https://staging.example.com)
- [Production](https://app.example.com)
- [CI/CD](https://ci.example.com)

## Documentation
- [API Docs](https://docs.example.com)
- [Design System](https://design.example.com)
- [Wiki](https://wiki.example.com)

## Tools
- [Slack](https://team.slack.com)
- [JIRA](https://team.atlassian.net)
- [GitHub](https://github.com/team/repo)

## Contacts
- Support: support@example.com
- DevOps: devops@example.com
- Product: product@example.com

Share with team: https://hibi.is/team-resources
```

## Tips for Basic Usage

{{< callout type="info" >}}
**Pro Tip:** Use descriptive URLs when possible. If you create an account, you can customize the URL to make it more memorable.
{{< /callout >}}

### Best Practices

1. **Keep it Simple:** Don't overcomplicate - hibi.is works best for quick sharing
2. **Use Appropriate Visibility:** Choose public/unlisted/private based on content sensitivity
3. **Add Context:** Include dates, purpose, or instructions to make content more useful
4. **Set Expiration:** For temporary content, set an expiration date
5. **Organize with Tags:** Use tags to categorize and find content later

### Common Patterns

```plaintext
Pattern 1: Quick Share
1. Copy content
2. Paste in hibi.is
3. Click create
4. Share link

Pattern 2: Formatted Content
1. Write in Markdown
2. Preview formatting
3. Publish
4. Share

Pattern 3: Code Sharing
1. Select language
2. Paste code
3. Enable syntax highlighting
4. Share with team
```

## Next Steps

{{< cards >}}
  {{< card link="/use-cases/advanced-features" title="Advanced Features" icon="sparkles" >}}
  {{< card link="/use-cases/integration" title="Integration" icon="puzzle" >}}
  {{< card link="/docs/getting-started" title="Getting Started" icon="play" >}}
{{< /cards >}}
