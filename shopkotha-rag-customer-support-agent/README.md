# RAG-Based Email Customer Support Agent

AI-powered customer support workflow built with **n8n, RAG, Google Gemini, Supabase, Gmail, Google Drive, and Telegram HITL**.

The system grounds customer replies in a company knowledge base and requires **human approval before a customer-facing email is sent**.

## Core Flow

```text
Knowledge Base
    ↓
Google Drive
    ↓
Document Loader → Text Splitter → Gemini Embeddings
    ↓
Supabase Vector KB
    ↓
Gmail Trigger
    ↓
Customer Support AI Agent
    ↓
RAG Retrieval + Gemini Response
    ↓
Telegram Human Approval
    ↓
Approved → Gmail Reply
Rejected → Telegram Manual-Response Notice
```

## Technologies

**n8n • RAG • Google Gemini • Supabase • Gmail • Google Drive • Telegram • LangChain**

## Files

- `workflows/shopkotha-rag-email-support.json` — Sanitized n8n workflow export
- `documentation.md` — System architecture, workflow logic, HITL process, setup, limitations, and implementation notes
