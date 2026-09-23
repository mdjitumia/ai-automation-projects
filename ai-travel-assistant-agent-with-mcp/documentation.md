# AI Travel Assistant using MCP — Documentation

## 1. Overview

The AI Travel Assistant is an n8n-based travel recommendation system built around the **Model Context Protocol (MCP)**.

The system combines an AI Agent, an MCP Client, an MCP Server exposing travel tools, and stored user preferences. It retrieves live travel information before generating personalized recommendations. fileciteturn7file2L4-L28

## 2. System Architecture

```text
                         USER
                           |
                           v
              ┌────────────────────────┐
              │ MCP Client Workflow    │
              │                        │
              │ Chat Trigger           │
              │        ↓               │
              │ Google Sheets Memory   │
              │        ↓               │
              │ AI Agent               │
              │        ↓               │
              │ MCP Client Tool        │
              └───────────┬────────────┘
                          |
                          | MCP
                          v
              ┌────────────────────────┐
              │ Travel MCP Server      │
              │                        │
              │ Get Weather            │
              │ Get Hotels             │
              │ Get Restaurants        │
              │ Get Attractions        │
              │ Get Currency           │
              └───────────┬────────────┘
                          |
                          v
                  External APIs
```

The supplied report shows the Client and Server as separate n8n workflows and uses the MCP Server as the tool layer for the travel assistant. fileciteturn7file2L30-L37

## 3. MCP Server

The MCP Server exposes five travel-related tools:

| Tool | Purpose |
|---|---|
| Get Weather | Current weather information |
| Get Hotels | Hotel/accommodation search |
| Get Restaurants | Restaurant search |
| Get Attractions | Tourist attraction search |
| Get Currency | Currency exchange-rate information |

The uploaded server workflow contains these five HTTP-based tools connected to the MCP Server Trigger. fileciteturn7file0L15-L20 fileciteturn7file0L47-L53 fileciteturn7file0L94-L100 fileciteturn7file0L141-L147 fileciteturn7file0L188-L194 fileciteturn7file0L198-L209

### Data Sources

- **Open-Meteo** — weather
- **OpenStreetMap Nominatim** — hotels, restaurants, attractions
- **Exchange Rate API** — currency rates

These roles are documented in the supplied project report. fileciteturn7file2L14-L20

## 4. MCP Client + AI Agent

The Client workflow receives a chat message, retrieves the user's stored preferences, and passes the information to the AI Agent.

```text
When chat message received
        ↓
Get row(s) in sheet
        ↓
AI Agent
   ├── Groq Chat Model
   └── MCP Client
```

The uploaded Client workflow contains a Chat Trigger, Google Sheets lookup, AI Agent, Groq Chat Model, and MCP Client. fileciteturn7file1L8-L16 fileciteturn7file1L19-L30 fileciteturn7file1L34-L45 fileciteturn7file1L48-L59

## 5. Personalization / Memory

User preferences are stored in Google Sheets and retrieved before recommendations are generated.

The workflow prompt uses:

- Budget
- Hotel type
- Transportation
- Diet
- Activity

The Agent is instructed to adapt recommendations according to these preferences and explicitly mention the personalization applied. fileciteturn7file1L20-L21

The report describes this as reusable user memory across future travel interactions. fileciteturn7file2L21-L28

## 6. MCP Tool Usage

For a travel query, the AI Agent is instructed to use the available MCP tools before answering:

1. Check current weather
2. Find hotels
3. Find restaurants
4. Find tourist attractions
5. Check currency for international destinations

The retrieved information is then combined with stored preferences to produce a personalized plan. fileciteturn7file1L20-L21

## 7. Example

### Input

```text
Plan a 3-day trip to Cox's Bazar
```

The supplied report demonstrates personalization using budget, resort/hotel preference, bus transportation, halal dietary preference, and adventure activity preference. fileciteturn7file2L33-L44

The documented output includes destination overview, weather, hotels, restaurants, attractions, transportation, budget estimates, packing suggestions, and a personalized recommendation. fileciteturn7file2L45-L84

## 8. Why MCP is Used

MCP provides a dedicated tool layer between the AI Agent and external travel services.

```text
AI Agent
   ↓
MCP Client
   ↓
Travel MCP Server
   ↓
Specialized Tools
   ↓
External APIs
```

The supplied report states that MCP allows the assistant to retrieve live information instead of depending only on static model knowledge. fileciteturn7file2L5-L20

## 9. Technology Stack

| Technology | Role |
|---|---|
| n8n | Workflow orchestration |
| MCP | Client-server tool communication |
| Groq | Language model |
| Google Sheets | User preference storage |
| Open-Meteo | Weather data |
| OpenStreetMap Nominatim | Hotels, restaurants, attractions |
| Exchange Rate API | Currency information |

## 10. Repository Structure

```text
ai-travel-assistant-mcp/
├── README.md
├── documentation.md
└── workflows/
    ├── mcp-server.json
    └── mcp-client-travel-assistant.json
```

## 11. Security

The workflow files are sanitized portfolio exports.

Do not commit:

- API keys
- OAuth credentials
- Private n8n endpoints
- MCP webhook paths
- Private Google Sheets identifiers
- Personal user IDs
- Other account-specific configuration

The public JSON files use placeholders such as:

```text
YOUR_N8N_HOST
YOUR_MCP_SERVER_PATH
YOUR_GOOGLE_SHEET_ID
YOUR_USER_ID
```

## 12. Limitations

The supplied project materials indicate practical dependencies on external APIs and stored preference data.

Potential limitations include:

- External API availability and data freshness
- OpenStreetMap search coverage
- Time-sensitive weather and exchange-rate information
- Outdated stored user preferences
- Recommendation quality depending on tool results and model interpretation

## 13. Project Summary

```text
User Query
   ↓
Personalization
   ↓
AI Agent
   ↓
MCP Client
   ↓
MCP Server
   ↓
Specialized Travel Tools
   ↓
Live Data
   ↓
Personalized Travel Plan
```

This project demonstrates a practical MCP-based architecture where the AI Agent accesses external travel tools through a dedicated MCP Server and combines live information with reusable user preferences. fileciteturn7file2L85-L111
