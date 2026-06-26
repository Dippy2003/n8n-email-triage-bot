# AI-Powered Customer Support Ticket Router

An n8n workflow that watches a Gmail inbox, classifies incoming support
emails with an LLM (Groq / Llama 3.3), and automatically routes each ticket
to the right place — Slack, Notion, or a templated auto-reply — while
logging every ticket to Google Sheets.

No OpenAI dependency: classification runs on Groq's free, fast inference API.

## How it works

1. **Gmail Trigger** polls a support inbox for new mail.
2. A loop guard (**Is Real Customer Email?**) drops anything sent from the
   bot's own address or already carrying an internal ticket reference, so
   the bot can never reply to its own replies.
3. **Groq Classify** sends the email to Llama 3.3 with a structured prompt
   and gets back a category (`Urgent` / `Billing` / `General`) and an
   urgency level.
4. **Route by Category** fans the ticket out:
   - `Urgent` → Slack alert with formatted blocks
   - `Billing` → a ticket page created in a Notion database
   - `General` → an FAQ-pointer auto-reply
5. Every ticket also gets a universal confirmation email and a logged row
   in Google Sheets, regardless of category.

## Stack

- **n8n** (Community Edition, self-hosted via Docker)
- **Groq API** (Llama 3.3 70B) for email classification
- **Gmail API** for the trigger, confirmation, and FAQ auto-reply
- **Slack API** for urgent alerts
- **Notion API** (direct REST calls) for billing tickets
- **Google Sheets API** for the ticket log

## Setup

1. Copy `.env.example` to `.env` and fill in real values:
   ```bash
   cp .env.example .env
   ```
2. Start n8n:
   ```bash
   docker compose up -d
   ```
3. Open `http://localhost:5678`, import `workflow.json`, and connect your
   Gmail, Slack, and Google Sheets credentials in the UI (Notion auth is
   handled via the `NOTION_TOKEN` env var directly, not an n8n credential).
4. Create a Notion database with columns: `Name` (title), `Sender`,
   `Category`, `Urgency`, `Status`, `Ticket Ref`, `Received`, `Notes` —
   then share it with your Notion integration via that database's
   **`⋯` → Connections** menu.
5. Activate the workflow.
