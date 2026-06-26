# AI-Powered Customer Support Ticket Router

An n8n workflow that watches a Gmail inbox, classifies incoming support
emails with an LLM (Groq / Llama 3.3), and automatically routes each ticket
to the right place — Slack, Notion, or a templated auto-reply — while
logging every ticket to Google Sheets.

No OpenAI dependency: classification runs on Groq's free, fast inference API.
