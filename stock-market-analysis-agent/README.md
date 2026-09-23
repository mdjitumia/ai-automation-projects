# Stock Market Analysis Agent

AI-powered stock chart analysis workflow built with **n8n, Google Gemini, Telegram, and a chart-image API**.

The system receives stock-analysis requests through Telegram, uses an AI Agent to identify the ticker and call a chart-analysis tool, analyzes the returned chart image with Gemini Vision, and sends a concise technical-analysis summary back to Telegram.

## Core Flow

```text
Telegram Message
       ↓
AI Agent
 ├── Gemini Chat Model
 ├── Conversation Memory
 └── get_chart Tool
       ↓
Chart Analysis Workflow
       ↓
Chart Image
       ↓
Gemini Vision
       ↓
Technical Summary
       ↓
Telegram Reply
```

## Key Capabilities

- Stock analysis from company names or ticker requests
- Technical chart-image analysis
- Multi-stock comparison support
- Short, chat-friendly responses
- Recent conversation memory
- Educational / not-financial-advice framing

## Technologies

**n8n • Google Gemini • Gemini Vision • Telegram • Chart Image API**

## Repository Contents

- `workflows/stock-analysis-agent.json` — Sanitized n8n workflow export
- `documentation.md` — Detailed architecture, workflow logic, AI behavior, setup, security, and limitations
