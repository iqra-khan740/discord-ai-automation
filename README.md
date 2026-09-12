# Discord AI Social Media Automation (n8n)

An n8n workflow that automatically generates and posts AI social media content to Discord, triggered by a Google Calendar event.

> **Scope note:** the original planning doc for this project covered both Discord and Telegram, and used Groq for caption generation. What's actually built and working right now is **Discord only**, using a local **Ollama** model (`gemma4:31b`) instead of Groq. This README describes the workflow as implemented. Telegram posting and the Groq integration are listed under [Roadmap](#roadmap) as not-yet-built.

## How it works

```
Google Calendar Event (new event created)
        ↓
n8n Trigger (polls every minute)
        ↓
Google Drive — find + download post image
Google Drive — find + download post title/topic text
        ↓
AI Agent (Ollama: gemma4:31b) — analyzes image, writes caption + hashtags
        ↓
Structured Output Parser — enforces JSON schema (caption, hashtags, etc.)
        ↓
Merge — combines AI output with the downloaded text
        ↓
HTTP Request → Discord Webhook — posts the final message
```

## Repo contents

```
discord-ai-automation/
├── README.md
├── workflows/
│   └── discord-ai-posting.json   # n8n workflow export
└── docs/
    └── planning-doc.md           # original planning notes (Discord + Telegram, Groq)
```

## Setup

1. **Discord**
   - Create a server and a text channel (e.g. `ai-content-hub`).
   - Channel Settings → Integrations → Webhooks → New Webhook → copy the URL.
2. **Google Drive**
   - Store the post image and a topic/title text file per post.
   - Update the `queryString` filters in the "Search files and folders" nodes to match your file naming.
3. **Google Calendar**
   - Create a calendar (e.g. "Social Media Posts") and add events — each new event triggers a post.
4. **Ollama**
   - Run Ollama locally/remotely and pull the model referenced in the workflow (or swap in another chat model node).
5. **Import into n8n**
   - Import `workflows/discord-ai-posting.json`.
   - Reconnect the Google Calendar / Google Drive OAuth credentials (these are not exported with the workflow).
   - Paste your real Discord webhook URL into the final HTTP Request node — **do not commit that URL to git**. Use an n8n credential or environment variable instead.

## ⚠️ Security note

The original JSON export contained a **live Discord webhook URL** hard-coded in the HTTP Request node. Webhook URLs act as bearer credentials — anyone with the URL can post to your channel. It has been replaced with a placeholder in this repo. Keep your real webhook URL out of git (use n8n's credential store, or a `.env` file that's git-ignored).

## Roadmap

- [ ] Telegram posting (parallel to Discord)
- [ ] Swap/optionally add Groq API for caption generation
- [ ] Manual approval step before posting
- [ ] Multi-post scheduling
- [ ] Content database (Airtable/Sheets) instead of Drive folders
