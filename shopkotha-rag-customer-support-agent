# RAG-Based Email Customer Support Agent — Documentation

## 1. Overview

This project is an AI-powered customer support workflow built in n8n. It uses **Retrieval-Augmented Generation (RAG)** to ground responses in a company knowledge base and adds **Human-in-the-Loop (HITL)** approval before a customer-facing reply is sent.

The submitted n8n workflow contains two logical branches inside one workflow:

1. **Knowledge Base Preparation** — manually run when the company knowledge base is created or updated.
2. **Customer Email Processing** — runs automatically when a new unread support email is detected.

The supplied project report describes this architecture and the end-to-end approval flow. fileciteturn6file1L5-L22

---

## 2. System Architecture

```text
┌─────────────────────────────────────────────────────────────┐
│                 KNOWLEDGE BASE PREPARATION                  │
│                 (Manual / Update Process)                   │
└─────────────────────────────────────────────────────────────┘

Manual Trigger
      ↓
Google Drive Company Knowledge
      ↓
Document Loader
      ↓
Recursive Text Splitter
      ↓
Gemini Embeddings
      ↓
Supabase Vector Knowledge Base


┌─────────────────────────────────────────────────────────────┐
│                 CUSTOMER EMAIL PROCESSING                   │
│                    (Automatic Flow)                         │
└─────────────────────────────────────────────────────────────┘

Gmail Trigger
      ↓
Extract Email Data
      ↓
Customer Support AI Agent
      ↓
Supabase KB Retrieve Tool
      ↓
Gemini Chat Model
      ↓
Format Draft Reply
      ↓
Telegram HITL Approval
      ↓
        ┌───────────────┐
        │   Approved?   │
        └──────┬────────┘
          YES  │   │  NO
               │   │
               ↓   ↓
        Gmail Reply  Telegram
                    Manual Notice
```

The report's architecture diagram explicitly separates knowledge-base indexing from customer-email processing while using Supabase as the shared retrieval layer. fileciteturn6file1L90-L97

---

## 3. Knowledge Base Preparation

The knowledge base preparation branch is manually triggered when the company knowledge document is created or updated.

### Flow

```text
Load KB Manually
      ↓
Download Company Knowledge
      ↓
Data Loader
      ↓
Text Splitter
      ↓
Gemini Embeddings
      ↓
Supabase KB - Insert
```

The supplied workflow uses a document loader, recursive text splitting, Gemini embeddings, and a Supabase vector store. fileciteturn6file0L5-L24 fileciteturn6file0L44-L58

The report describes the same process as downloading the company document from Google Drive, splitting it into chunks, converting the chunks into embedding vectors, and storing them in the Supabase `documents` table. fileciteturn6file1L8-L10 fileciteturn6file1L47-L56

---

## 4. Customer Email Processing

The automatic branch begins with the Gmail trigger, which monitors the support inbox for unread customer messages.

### Flow

```text
Gmail Trigger
      ↓
Extract Email Data
      ↓
Customer Support AI Agent
```

The workflow extracts:

- `customerEmail`
- `subject`
- `body`
- `messageId`
- `threadId`

The message subject and body are passed to the AI agent, while the message ID is preserved so an approved reply can be sent in the original email thread. fileciteturn6file0L70-L87 fileciteturn6file0L100-L138

---

## 5. RAG Retrieval

The Customer Support AI Agent is connected to the **Supabase KB Retrieve Tool**.

The tool is configured to search the ShopKotha knowledge base for relevant information such as:

- Policies
- FAQs
- Delivery
- Returns
- Refunds
- Payments
- Warranty
- Account information

The agent is instructed to use the knowledge-base tool before answering. fileciteturn6file0L167-L182 fileciteturn6file0L216-L226

### Retrieval Flow

```text
Customer Question
      ↓
Query Embedding
      ↓
Supabase Semantic Retrieval
      ↓
Relevant Knowledge Chunks
      ↓
Customer Support AI Agent
```

The report explains that the retrieved company-specific context is supplied to the agent so responses are grounded in company policies and FAQs rather than relying only on the model's general knowledge. fileciteturn6file1L47-L56

---

## 6. AI Response Generation

Gemini is used as the language model for generating the customer-support draft.

The agent receives the customer's subject and body and uses the retrieved knowledge-base context before generating the response. fileciteturn6file0L196-L224

The system prompt requires the agent to:

- Use the knowledge-base tool before answering.
- Reply politely and concisely.
- Respond in the same language used by the customer.
- Escalate instead of guessing when relevant information is unavailable.
- Escalate refund requests above the configured threshold.
- Escalate when the customer appears upset.

---

## 7. Human-in-the-Loop Approval

This is a key safety layer of the system.

After the AI creates a draft:

```text
AI Draft
   ↓
Telegram
   ↓
Support Representative
   ↓
Approve / Reject
```

The workflow sends the customer information and draft response to a designated support representative through Telegram. fileciteturn6file0L274-L285

The `Approved?` decision node evaluates the human decision. fileciteturn6file0L300-L326

### Approved Path

```text
Approved
   ↓
Gmail Send Reply
   ↓
Original Customer Email Thread
```

The approved draft is sent as a reply to the original Gmail thread. fileciteturn6file0L334-L343

### Rejected Path

```text
Rejected
   ↓
Telegram Notify Rejected
   ↓
Manual Response Required
```

The report confirms that rejected drafts result in a Telegram notification instructing the support representative to handle the customer manually. fileciteturn6file1L100-L110

---

## 8. Why RAG + HITL?

### RAG

RAG allows the agent to retrieve company-specific information before generating a response. This helps keep replies grounded in approved policies and FAQs. fileciteturn6file1L47-L56

### HITL

Human approval keeps the final customer-facing action under human control. The AI performs retrieval and drafting, while a support representative decides whether the message should actually be sent. fileciteturn6file1L57-L64

Together:

```text
Knowledge Grounding
        +
AI Drafting
        +
Human Approval
        =
Controlled AI Customer Support
```

---

## 9. Technology Stack

| Component | Role |
|---|---|
| n8n | Workflow orchestration, triggers, branching, AI-agent integration, HITL |
| Gmail | Receive customer emails and send replies |
| Google Drive | Store the knowledge-base source document |
| Google Gemini | Embeddings and response generation |
| Supabase | Vector database and semantic retrieval |
| LangChain-based n8n Nodes | Document loading, splitting, embeddings, retrieval, agent structure |
| Telegram | Human approval interface |

These roles are documented in the supplied project report. fileciteturn6file1L25-L36

---

## 10. Repository Structure

```text
rag-email-customer-support/
├── README.md
├── documentation.md
└── workflows/
    └── shopkotha-rag-email-support.json
```

---

## 11. Setup

1. Import `workflows/shopkotha-rag-email-support.json` into n8n.
2. Configure your own Gmail credentials.
3. Configure your own Google Drive source document.
4. Configure your own Supabase project and vector table.
5. Configure Gemini authentication and model access.
6. Configure your own Telegram credential and chat ID.
7. Replace placeholders in the public workflow export.
8. Run the knowledge-base branch when the knowledge source is created or updated.
9. Test the email-support branch with controlled customer messages.
10. Verify both Telegram approval paths before production use.

---

## 12. Security

The workflow JSON included in this repository is a **sanitized portfolio export**.

Do not commit:

- API keys
- OAuth credentials
- Telegram bot tokens
- Telegram chat IDs
- Private Google Drive identifiers
- Customer email addresses or personal information
- Private webhook secrets
- Database credentials

Customer support workflows may process sensitive information, so credentials, permissions, and stored data should be protected. fileciteturn6file1L80-L89

---

## 13. Limitations

The supplied report identifies several limitations:

- AI models can still hallucinate; RAG and escalation reduce risk but do not eliminate it.
- An outdated knowledge base can lead to outdated answers.
- Complex or emotional customer queries may still require human judgment.
- Retrieval quality depends on chunking, embeddings, and knowledge-base completeness.
- Gmail, Telegram, Gemini, n8n, and database usage can introduce recurring costs. fileciteturn6file1L76-L89

---

## 14. Practical Use

The report identifies e-commerce customer support as the primary use case, with the same pattern also applicable to SaaS support, educational platforms, banking/financial FAQ workflows, and healthcare administration for non-clinical inquiries with appropriate escalation. fileciteturn6file1L65-L75

---

## 15. Implementation Summary

The system follows this pattern:

```text
Retrieve → Ground → Draft → Review → Deliver
```

The result is a practical customer-support automation where AI handles repetitive retrieval and drafting work while a human retains control over the final customer-facing response. fileciteturn6file1L107-L112
