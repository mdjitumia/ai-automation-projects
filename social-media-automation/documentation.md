# Social Media Automation — Documentation

## 1. Overview

This project contains two Facebook/Meta automation modules implemented on different workflow platforms:

1. **Facebook Messenger AI Customer Support** — n8n
2. **Facebook Page Post Automation** — Make

They are grouped under one project because both address social media operations, while their implementation formats and runtime platforms remain separate.

---

# Part 1 — Facebook Messenger AI Customer Support

## 2. Purpose

The Messenger workflow receives Meta Messenger webhook events, validates Meta webhook subscription requests, sends incoming user messages to an AI Agent, and returns the generated response through the Facebook Graph API.

## 3. Architecture

```text
Facebook / Meta Messenger
          ↓
       Webhook
          ↓
   Webhook Verification
      ↙           ↘
 Challenge       Message
 Response          ↓
              AI Agent
             ↙        ↘
       Groq Model    Memory
             ↓
        AI Response
             ↓
      Facebook Graph API
             ↓
       Messenger User
```

## 4. Workflow Components

### Webhook

The workflow begins with an n8n Webhook node configured to handle Meta webhook requests.

### Webhook Verification

The verification branch checks Meta subscription parameters including `hub.mode` and `hub.verify_token`. A successful verification returns the `hub.challenge` value through the Respond to Webhook node.

### AI Agent

For a normal incoming message, the Messenger text is passed to an n8n AI Agent configured to generate a customer-support response.

### Groq Chat Model

The AI Agent uses the Groq chat model `llama-3.3-70b-versatile`.

### Conversation Memory

The workflow uses conversation memory associated with the Messenger sender/session context so that recent interaction history can be retained during the conversation.

### Facebook Graph API

The generated response is sent back to the Messenger user using an HTTP request to the Facebook Graph API messaging endpoint.

## 5. End-to-End Flow

```text
Incoming Messenger Event
        ↓
Meta Webhook
        ↓
Webhook Verification / Message Handling
        ↓
AI Agent
        ↓
Groq LLM + Conversation Memory
        ↓
Generated Response
        ↓
Facebook Graph API
        ↓
Messenger Reply
```


# Part 2 — Facebook Page Post Automation

## 6. Purpose

The Facebook Page Post Automation is a **Make scenario** that reads content from Google Sheets, filters records according to post status, and publishes the selected content to a Facebook Page.

## 7. Architecture

```text
Google Sheets
      ↓
   Filter Rows
(Status-based selection)
      ↓
Facebook Pages
   Create Post
      ↓
Facebook Page
```

## 8. Google Sheets Logic

The supplied blueprint uses the Google Sheets `filterRows` module and includes status conditions for:

```text
Not Posted
IN review
```

The workflow maps spreadsheet content into the Facebook post module.

## 9. Facebook Post Creation

The Make `facebook-pages:CreatePost` module publishes the mapped message to the selected Facebook Page and supports link-based post content.

## 10. Scheduling Note

The supplied file is a **Make blueprint**. The exact execution schedule is not represented as an n8n Schedule Trigger inside the JSON. Configure the scenario's scheduling in Make according to the desired publishing cadence.

---

# 11. Import Guide

## Facebook Messenger — n8n

Import:

```text
facebook-messenger-automation/n8n-workflow.json
```

Then configure:

1. Meta webhook URL and webhook verification settings
2. Meta Page access token
3. Groq credentials
4. Any required n8n memory/database configuration
5. Facebook Graph API settings

## Facebook Page Post — Make

Import:

```text
facebook-page-post-automation/make-blueprint.json
```

Then reconnect:

1. Google Sheets
2. Facebook connection
3. Source spreadsheet and sheet
4. Target Facebook Page
5. Make scenario schedule

---

# 12. Important Platform Difference

These two files are intentionally kept separate because they use different automation platforms.

```text
n8n workflow  →  import into n8n
Make blueprint →  import into Make
```

The Make file should not be imported into n8n as an n8n workflow.


