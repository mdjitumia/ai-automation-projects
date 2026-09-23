# AI Travel Assistant using MCP

An AI-powered travel assistant built with **n8n and Model Context Protocol (MCP)** to generate personalized travel recommendations using live travel data and stored user preferences.

## Core Architecture

```text
User
  ↓
MCP Client + AI Agent
  ↓
Travel MCP Server
  ├── Weather
  ├── Hotels
  ├── Restaurants
  ├── Attractions
  └── Currency
  ↓
Live Travel Data + User Preferences
  ↓
Personalized Travel Plan
```

## Technologies

**n8n • MCP • Groq • Open-Meteo • OpenStreetMap Nominatim • Exchange Rate API • Google Sheets**

## Files

- `workflows/mcp-client-travel-assistant.json` — Sanitized MCP Client workflow
- `workflows/mcp-server.json` — Sanitized MCP Server workflow
- `documentation.md` — Architecture, MCP tools, personalization, setup, and limitations
