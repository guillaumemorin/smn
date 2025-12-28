---
title: Frequently Asked Questions
type: docs
---

# Frequently Asked Questions

Common questions and answers about hibi.is.

## General

### What is hibi.is?

hibi.is is a web service designed to help you create, store, and share content quickly and easily. Whether you're sharing code snippets, taking notes, shortening URLs, or publishing documentation, hibi.is provides a simple and powerful platform for all your content needs.

### Is hibi.is free?

Yes! hibi.is offers a free tier that includes:
- Create up to 100 items
- Basic analytics
- Public and unlisted sharing
- API access (rate limited)

We also offer Pro and Enterprise plans with additional features and higher limits.

### Do I need an account?

For basic usage, no account is required. However, creating an account gives you:
- Access to your content from anywhere
- Ability to edit and delete content
- Analytics and insights
- API access
- Custom domains
- And more!

### How long is content stored?

By default, content is stored indefinitely unless you:
- Set an expiration date
- Delete it manually
- Exceed your storage quota

Free tier content may be subject to cleanup policies after extended periods of inactivity.

## Privacy & Security

### Is my content private?

You control the privacy of your content with three visibility options:

- **Public:** Visible to everyone, indexed by search engines
- **Unlisted:** Only accessible via direct link, not indexed
- **Private:** Only accessible to you when logged in

### Can I password-protect my content?

Yes! Pro and Enterprise plans support password protection. Set a password when creating content, and viewers will need to enter it to access.

### Is my data encrypted?

Yes. All data is:
- Transmitted over HTTPS (TLS 1.3)
- Encrypted at rest
- Backed up regularly
- Protected against unauthorized access

### Do you track my content views?

Basic analytics (view counts) are available on all plans. Advanced analytics (referrers, geography, timeline) are available on Pro and Enterprise plans. You can opt out of analytics entirely in your account settings.

### Can I delete my account and data?

Yes, absolutely. You can delete your account and all associated data at any time from your account settings. This action is permanent and cannot be undone.

## Features & Usage

### What content types are supported?

hibi.is supports:
- **Text:** Plain text content
- **Markdown:** Formatted text with Markdown syntax
- **Code:** Syntax-highlighted code in 100+ languages
- **Links:** URL shortening and redirection
- **Files:** Upload and share files (Pro+)
- **Images:** Direct image hosting (Pro+)

### What's the maximum content size?

Size limits vary by plan:
- **Free:** 5 MB per item
- **Pro:** 50 MB per item
- **Enterprise:** 500 MB per item

### Can I edit content after creating it?

Yes! You can edit any content you own at any time. Content history is preserved on Pro and Enterprise plans, allowing you to view and restore previous versions.

### Can I customize the URL?

Custom URLs (e.g., `hibi.is/my-custom-url`) are available on Pro and Enterprise plans. Free tier users receive auto-generated short URLs.

### Can I use my own domain?

Yes! Pro and Enterprise plans support custom domains. You can serve your content from your own domain (e.g., `docs.yourcompany.com`) with automatic SSL.

### How do I share content?

Multiple sharing options:
1. **Direct Link:** Copy and share the URL
2. **QR Code:** Generate QR code for mobile sharing
3. **Embed:** Embed content on your website
4. **Social Media:** Share directly to Twitter, Facebook, LinkedIn
5. **Email:** Send via email from the share menu

## API & Integration

### Is there an API?

Yes! All plans include API access with varying rate limits:
- **Free:** 60 requests/hour
- **Pro:** 1,000 requests/hour
- **Enterprise:** 10,000 requests/hour

See our [API Documentation](/api) for details.

### Can I automate content creation?

Absolutely! Use our API to:
- Create content programmatically
- Integrate with CI/CD pipelines
- Build custom applications
- Automate workflows
- Connect with other services

### Are there SDKs available?

Yes, official SDKs are available for:
- JavaScript/TypeScript (npm)
- Python (PyPI)
- Go (Go modules)
- Ruby (RubyGems)
- PHP (Composer)

Community SDKs exist for other languages.

### What are webhooks and how do I use them?

Webhooks allow you to receive real-time notifications when events occur (content created, updated, deleted, etc.). Available on Pro and Enterprise plans.

See [Advanced Usage](/docs/advanced) for webhook setup instructions.

### Can I integrate with Slack/Discord?

Yes! You can:
1. Use webhooks to send notifications to Slack/Discord
2. Use slash commands (Enterprise)
3. Install official integrations from the marketplace

## Troubleshooting

### My content isn't loading

Try these steps:
1. Check if the URL is correct
2. Verify the content hasn't expired
3. Clear your browser cache
4. Try incognito/private mode
5. Check [status.hibi.is](https://status.hibi.is) for service status

See our [Troubleshooting Guide](/docs/troubleshooting) for more help.

### I'm getting a 429 error (Rate Limited)

You've exceeded your API rate limit. Solutions:
1. Slow down your requests
2. Implement exponential backoff
3. Upgrade to a higher plan
4. Use batch endpoints when possible

### My API key isn't working

Common issues:
1. Check for extra spaces or characters
2. Verify the key hasn't been revoked
3. Ensure you're using the correct API endpoint
4. Check that your account is in good standing

Generate a new API key from Settings → API if needed.

### Content is showing as expired

Content expires if:
1. You set an expiration date when creating it
2. Your account exceeded storage limits
3. Free tier cleanup policy (90+ days inactive)

Check your content settings and account status.

## Billing & Plans

### What's included in the Free plan?

- Up to 100 items
- 5 MB per item
- Basic analytics
- Public & unlisted sharing
- API access (60 req/hour)
- Community support

### What do Pro and Enterprise plans offer?

**Pro Plan ($9/month):**
- Unlimited items
- 50 MB per item
- Custom URLs
- Password protection
- Advanced analytics
- Webhooks
- API: 1,000 req/hour
- Email support
- No ads

**Enterprise Plan (Custom):**
- Everything in Pro
- Custom storage limits
- Custom domains
- SSO/SAML
- SLA guarantee
- API: 10,000+ req/hour
- Priority support
- Dedicated account manager

### Can I upgrade or downgrade anytime?

Yes! You can change your plan at any time. When upgrading, you're charged a prorated amount. When downgrading, credit is applied to future billing.

### Do you offer discounts?

Yes! We offer:
- **Students:** 50% off Pro plan (with .edu email)
- **Non-profits:** 50% off Pro plan
- **Annual billing:** 20% off (2 months free)
- **Teams:** Volume discounts for 5+ users

Contact sales@hibi.is for custom pricing.

### What payment methods do you accept?

We accept:
- Credit cards (Visa, Mastercard, Amex, Discover)
- PayPal
- Bank transfer (Enterprise plans only)
- Cryptocurrency (Bitcoin, Ethereum)

### Is there a refund policy?

Yes. We offer a 30-day money-back guarantee on all paid plans. No questions asked.

## Technical

### What browsers are supported?

hibi.is works on all modern browsers:
- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+
- Opera 76+

Mobile browsers are also fully supported.

### Is there a mobile app?

Not yet, but it's on our roadmap! The website is fully responsive and works great on mobile browsers. You can add hibi.is to your home screen for an app-like experience.

### Does hibi.is work offline?

Basic viewing of previously accessed content works offline. Creating new content requires an internet connection.

### Can I self-host hibi.is?

Not currently. hibi.is is a hosted service. If you're interested in an on-premises deployment, contact us about our Enterprise plan.

### What's your uptime SLA?

- **Free & Pro:** Best effort (typically 99.5%+)
- **Enterprise:** 99.9% uptime SLA with credits for downtime

Check current status at [status.hibi.is](https://status.hibi.is).

## Data & Export

### Can I export my data?

Yes! You can export all your data at any time in multiple formats:
- JSON (full data with metadata)
- CSV (tabular data)
- Markdown (formatted text)
- ZIP (including uploaded files)

See [Configuration Guide](/docs/configuration) for export instructions.

### Can I import data from another service?

Yes! We support importing from:
- Generic JSON/CSV formats
- Pastebin exports
- Gist exports
- Custom formats (contact support)

### What happens to my data if I cancel?

You have 30 days to export your data after cancellation. After 30 days, your data is permanently deleted. We recommend exporting before canceling.

### Do you back up my data?

Yes. We perform:
- Real-time replication
- Daily automated backups
- Weekly off-site backups
- Point-in-time recovery (Enterprise)

## Community & Support

### How do I get help?

Multiple support channels:
1. **Documentation:** Browse our comprehensive docs
2. **FAQ:** You're reading it!
3. **Community:** [GitHub Discussions](https://github.com/guillaumemorin/hibi/discussions)
4. **Email:** support@hibi.is (Pro+)
5. **Chat:** Live chat for Enterprise

### Can I contribute to hibi.is?

Yes! We welcome:
- Bug reports on [GitHub Issues](https://github.com/guillaumemorin/hibi/issues)
- Feature requests
- Documentation improvements
- Community support
- SDK development

### Is hibi.is open source?

Core API and SDKs are open source. The web application is proprietary. See our [GitHub organization](https://github.com/guillaumemorin/hibi) for available repositories.

### How can I stay updated?

Follow us:
- **Blog:** [blog.hibi.is](https://blog.hibi.is)
- **Twitter:** [@hibiis](https://twitter.com/hibiis)
- **GitHub:** [guillaumemorin/hibi](https://github.com/guillaumemorin/hibi)
- **Newsletter:** Subscribe in account settings

## Still have questions?

Can't find what you're looking for? 

{{< cards >}}
  {{< card link="/docs" title="Documentation" icon="book-open" >}}
  {{< card link="/docs/troubleshooting" title="Troubleshooting" icon="exclamation-circle" >}}
  {{< card link="https://github.com/guillaumemorin/hibi/discussions" title="Community Discussions" icon="chat" >}}
{{< /cards >}}

Or contact us directly:
- **Email:** support@hibi.is
- **Twitter:** [@hibiis](https://twitter.com/hibiis)
- **GitHub:** [Open an issue](https://github.com/guillaumemorin/hibi/issues/new)
