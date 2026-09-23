# Social Media Automation

A collection of practical social media automation systems built with **n8n** and **Make**, covering AI-powered Messenger customer support and automated Facebook Page publishing.

## Modules

### 1. Facebook Messenger AI Customer Support

An n8n workflow that receives Meta Messenger events through a webhook, verifies Meta webhook subscriptions, processes incoming messages with an AI Agent powered by Groq, maintains conversation context, and sends replies through the Facebook Graph API.

### 2. Facebook Page Post Automation

A Make scenario that reads post records from Google Sheets, filters eligible content by status, and publishes the selected post to a Facebook Page.

## Technologies

**n8n • Make • Meta Messenger • Facebook Graph API • Groq • Llama 3.3 70B • Google Sheets • AI Agents • Webhooks**

## Structure

```text
social-media-automation/
├── README.md
├── documentation.md
├── facebook-page-post-automation/
│   └── make-blueprint.json
└── facebook-messenger-automation/
    └── n8n-workflow.json
```

## Platforms

| Module | Platform | Purpose |
|---|---|---|
| Facebook Messenger AI Customer Support | n8n | AI-powered conversational automation |
| Facebook Page Post Automation | Make | Automated Facebook Page publishing |
