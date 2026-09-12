# Original Planning Doc: Discord AI Social Media Automation using n8n + Groq

> Kept for reference. This was the original plan; see the main [README](../README.md) for what's actually implemented (Discord only, Ollama instead of Groq).

## Overview

Build a fully automated social media posting system using:

- Google Calendar (scheduling)
- Google Drive (content storage)
- Groq API (AI caption generation)
- Discord Webhooks (posting platform)
- n8n (automation engine)

## Full automation flow

```
Google Calendar Event
        ↓
n8n Trigger
        ↓
Google Drive (Get image + topic)
        ↓
Groq API (Generate caption)
        ↓
Discord Webhook (Post message)
```

## Step 1 — Create Discord server

Open Discord → "+" (Add Server) → Create My Own Server → name it (e.g. "AI Content Hub") → Create.

## Step 2 — Create a channel

Inside the server, "+" next to Text Channels → name it (e.g. `ai-posts`) → Create Channel.

## Step 3 — Create a Discord webhook

Channel → ⚙️ Edit Channel → Integrations → Webhooks → New Webhook → name it (e.g. "n8n Bot") → copy the webhook URL.

This URL is the authentication — no login needed in n8n.

## Step 4 — Google Drive setup

```
social-posts/
    post1/
        image.jpg
        topic.txt
```

`topic.txt` example: "AI automation is changing business workflows" — used as input for AI caption generation.

## Step 5 — Google Calendar setup

Create an event (e.g. "Post AI Content") on a schedule (e.g. daily 8 PM). This event triggers the n8n workflow.

## Step 6 — Groq API setup

Generate an API key from the Groq dashboard and save it for use in an n8n HTTP Request node.

## Step 7–9 — n8n workflow

n8n listens for calendar events, fetches content from Drive, generates a caption via Groq, then posts to Discord.

Nodes:
- **Trigger:** Google Calendar Trigger
- **Storage:** Google Drive (list, download image, download text)
- **AI:** HTTP Request → Groq API
- **Posting:** HTTP Request → Discord Webhook

## Step 10 — Discord posting format

Simple message:
```json
{ "content": "Your AI-generated caption here" }
```

Optional embed:
```json
{ "embeds": [ { "title": "AI Post", "description": "Caption text here" } ] }
```

## Authentication summary

| Service | Auth method |
| --- | --- |
| Google Calendar | OAuth login in n8n |
| Google Drive | OAuth login in n8n |
| Groq API | API key (bearer token) |
| Discord | Webhook URL (no login needed) |

## Optional upgrades (later)

- Multi-channel posting (Discord + Telegram)
- Approval system (manual approve before posting)
- Auto hashtag optimization
- AI image generation
- Content database (Airtable / Sheets)
- Multi-post scheduling system
