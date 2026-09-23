# Stock Market Analysis Agent — Documentation

## 1. Project Overview

This project is an n8n-based AI stock-analysis assistant designed to provide concise technical-analysis summaries from stock chart images.

The workflow contains a Telegram-facing AI Agent layer and a chart-analysis sub-workflow. The main Agent receives user messages, identifies an exchange-qualified ticker, calls the `get_chart` workflow tool, and returns the resulting analysis to Telegram.

The chart sub-workflow receives a ticker, retrieves a three-month chart image, passes the image to Gemini Vision, and prepares a short analysis. The supplied workflow explicitly configures the chart interval as `3M`. fileciteturn8file0L14-L18 fileciteturn8file0L24-L39

---

## 2. Overall Architecture

```text
                           USER
                             |
                             v
                    ┌─────────────────┐
                    │ Telegram        │
                    │ Trigger         │
                    └────────┬────────┘
                             |
                             v
                    ┌─────────────────┐
                    │ AI Agent        │
                    │                 │
                    │ Gemini Chat     │
                    │ Memory          │
                    │ get_chart Tool  │
                    └────────┬────────┘
                             |
                      Execute Tool
                             |
                             v
              ┌─────────────────────────────┐
              │ Chart Analysis Sub-workflow │
              │                             │
              │ Input: EXCHANGE:TICKER      │
              │        ↓                    │
              │ Chart Image API             │
              │        ↓                    │
              │ Gemini Vision               │
              │        ↓                    │
              │ Short Analysis              │
              └──────────────┬──────────────┘
                             |
                             v
                    ┌─────────────────┐
                    │ Telegram Reply  │
                    └─────────────────┘
```

The uploaded workflow contains the Telegram trigger, main AI Agent, Gemini chat model, conversation memory, `get_chart` tool, chart retrieval node, Gemini Vision analysis, response preparation, and Telegram reply. fileciteturn8file0L119-L146 fileciteturn8file0L153-L181 fileciteturn8file0L190-L241

---

## 3. Main AI Agent Workflow

### 3.1 Telegram Trigger

The workflow starts when a Telegram message is received and passes the message text to the AI Agent. fileciteturn8file0L119-L143

### 3.2 AI Agent

The main Agent is configured as a technical stock-analysis assistant.

Its workflow instructions include:

- Convert company names to exchange-qualified ticker symbols.
- Call the `get_chart` tool for analysis requests.
- Call the chart tool separately when comparing multiple stocks.
- Keep responses short and chat-friendly.
- Avoid specific buy/sell investment advice.
- Include an educational-purpose disclaimer when required.
- Use conversation memory for follow-up questions.

These behaviors are defined in the Agent's system message. fileciteturn8file0L136-L146

### 3.3 Gemini Chat Model

The AI Agent uses a Google Gemini chat model configured with a temperature of `0.3`. fileciteturn8file0L153-L162

### 3.4 Conversation Memory

A windowed memory node stores the recent conversation context using a custom session key based on the Telegram chat ID. fileciteturn8file0L176-L183

---

## 4. `get_chart` Tool

The main Agent is connected to a workflow tool named:

```text
get_chart
```

The tool invokes the chart-analysis sub-workflow and passes an exchange-qualified ticker such as:

```text
NASDAQ:AAPL
NASDAQ:MSFT
NASDAQ:TSLA
```

The uploaded configuration defines `get_chart` as a workflow tool attached to the main AI Agent. fileciteturn8file0L190-L226

---

## 5. Chart Analysis Sub-Workflow

### 5.1 Input

The sub-workflow is triggered by:

```text
When Called by Agent
```

and accepts a workflow input named:

```text
ticker
```

fileciteturn8file0L5-L17

### 5.2 Chart Retrieval

The workflow requests a chart image from a chart-image API and dynamically inserts the ticker into the request.

The supplied workflow uses a **3-month (`3M`) chart interval**. fileciteturn8file0L24-L39

### 5.3 Gemini Vision

The returned chart image is passed to Gemini Vision.

The configured prompt asks for a short 4–6 sentence summary covering:

1. Overall trend direction visible in the chart.
2. Approximate current price position relative to the displayed range.
3. One concise bullish, bearish, or neutral takeaway with a reason.

The prompt also instructs the model to keep the result concise and include an educational-purpose disclaimer. fileciteturn8file0L67-L78

### 5.4 Response Preparation

The Vision result is mapped into an `analysis` field together with the original ticker. fileciteturn8file0L90-L110

---

## 6. End-to-End Execution

```text
User
"Analyze Microsoft"
        ↓
Telegram Trigger
        ↓
AI Agent
Microsoft → NASDAQ:MSFT
        ↓
get_chart Tool
        ↓
Chart Analysis Workflow
        ↓
3M Chart Image
        ↓
Gemini Vision
        ↓
Concise Technical Summary
        ↓
Telegram Reply
```

The uploaded workflow connections show the main Agent invoking the chart tool and the sub-workflow chaining chart retrieval → Gemini Vision → prepared response. fileciteturn8file0L253-L301

---

## 7. Multi-Stock Comparison

The Agent is instructed to call `get_chart` separately for each stock in a comparison request and then provide one combined takeaway. fileciteturn8file0L136-L142

Conceptually:

```text
Stock A → get_chart → Analysis A
Stock B → get_chart → Analysis B
                         ↓
                  Combined Takeaway
```

---

## 8. AI Scope & Guardrails

The workflow explicitly instructs the Agent to avoid specific buy/sell investment advice and to frame the output as educational information. fileciteturn8file0L136-L142

The chart-analysis prompt also includes an educational-purpose disclaimer. fileciteturn8file0L76-L78

---

## 9. Technology Stack

| Technology | Role |
|---|---|
| n8n | Workflow orchestration |
| Telegram | User interface and response channel |
| Google Gemini | Conversational model |
| Gemini Vision | Chart-image analysis |
| Chart Image API | Chart retrieval |
| n8n Memory | Recent conversation context |

---

## 10. Repository Structure

```text
stock-market-analysis-agent/
├── README.md
├── documentation.md
└── workflows/
    └── stock-analysis-agent.json
```

---

---

## 11. Security

The uploaded workflow contained a hard-coded bearer token in the chart-image API request header. fileciteturn8file0L39-L46

The GitHub portfolio version replaces this with:

```text
Bearer YOUR_CHART_IMAGE_API_TOKEN
```

If the original token was real and has ever been exposed, revoke/rotate it before publishing the original workflow.

Do not commit:

- API keys
- Bearer tokens
- Telegram bot tokens
- Private webhook identifiers
- Private workflow identifiers
- Account-specific configuration

The included `workflow.json` is a sanitized portfolio/import template.

---

---

## 12. Project Summary

```text
Telegram
   ↓
AI Agent
   ↓
Ticker Identification
   ↓
get_chart Tool
   ↓
Chart Image API
   ↓
Gemini Vision
   ↓
Concise Technical Summary
   ↓
Telegram
```

The project demonstrates an agentic workflow pattern where a conversational AI delegates a specialized visual-analysis task to a separate workflow tool and returns the result through the user's chat interface. fileciteturn8file0L190-L226
