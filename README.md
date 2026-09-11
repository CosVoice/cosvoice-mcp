# CosVoice for Grok Bot, Claude, ChatGPT, and Cursor

**Give your Chief of Staff a phone.** CosVoice gives an AI assistant its own real phone number (in your area code) and its own email address. The assistant can place calls for you, book restaurants by phone, answer the calls you miss, and read a written summary of every call. The voice on the line is Grok Voice, full duplex, and it always says it is an AI assistant when asked.

Website: https://cosvoice.com · Setup page written for the assistant: https://cosvoice.com/skill

## Connect in one paste

```
https://cosvoice.com/mcp
```

Add that as a remote MCP server (Streamable HTTP). CosVoice uses OAuth 2.1 with PKCE and dynamic client registration, so a CosVoice page opens, you sign in, click **Allow**, and the assistant is connected. There is no API key to copy.

| Client | How |
| --- | --- |
| Grok Bot / Grok | Settings → Plugins → add a custom MCP server by URL, or install this plugin |
| Claude (web, desktop, Code) | Add a custom connector / `claude mcp add --transport http cosvoice https://cosvoice.com/mcp` |
| ChatGPT | Settings → Connectors → add MCP server by URL (developer mode) |
| Cursor | [Add to Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=cosvoice&config=eyJ1cmwiOiJodHRwczovL2Nvc3ZvaWNlLmNvbS9tY3AifQ==) or paste the URL under Settings → MCP |

Clients that only support a static header can use a key from Settings → Connect your agent as `Authorization: Bearer cv_live_…`.

## Then let the assistant set itself up

Send it: **"Set up my CosVoice line in area code 949. Call it Lloyd."**

It calls `search_numbers`, shows you options, and `create_line`. Payment comes first: if no plan is on file, `create_line` returns a checkout link for you to complete, then the number is issued.

## What it can do

| Tool | What it does |
| --- | --- |
| `get_account`, `search_numbers`, `create_line` | First-run setup: check the account, find numbers in an area code, create the line |
| `place_call` | Call a real number and hold a live conversation on your behalf (async; poll `get_call`) |
| `book_reservation` | Book a table by phone with a time window, fallbacks, seating, occasion, dietary notes |
| `get_call`, `list_calls`, `list_reservations`, `annotate_call` | Summaries, structured details, transcripts, outcomes |
| `send_text`, `list_messages` | Texts from and to the line (STOP respected) |
| `list_emails`, `get_email`, `forward_email` | The line's own inbox |
| `list_contacts`, `add_contact`, `remove_contact` | Owner, approved, VIP, blocked callers |
| `get_settings`, `update_settings`, `set_webhook` | Persona, voice, greeting, standing instructions, `call.completed` webhooks |
| `ack_instructions` | Texts you send the line from your own phone arrive as instructions on every tool result; acknowledge when handled |
| `list_history`, `recent_activity`, `list_integrations` | Unified history, catch-up since a timestamp, partner tools |

## What this repo contains

- `skills/cosvoice/SKILL.md` — the skill (same text as https://cosvoice.com/skill)
- `.mcp.json` — the hosted server entry (`type: http`, OAuth)
- `.grok-plugin/plugin.json` and `.claude-plugin/plugin.json` — plugin manifests
- `server.json` — the official MCP Registry entry (`com.cosvoice/cosvoice`)

No code runs on your machine. The plugin only points your client at the hosted server.

## Rules the line follows

Never pays or reads out a card. Always says it is an AI assistant when asked. Uses its own number and email as the callback unless you say otherwise. Never claims a booking unless the call outcome says so. Respects STOP for texts.

## Pricing

Starter $29/mo (60 min), Plus $69/mo (200 min), Pro $149/mo (600 min), overage $0.25/min. One number per paid month. Details: https://cosvoice.com/pricing

## Links

Trust and safety: https://cosvoice.com/trust · FAQ: https://cosvoice.com/faq · Privacy: https://cosvoice.com/privacy · Terms: https://cosvoice.com/terms · Support: hello@cosvoice.com

MIT licensed. CosVoice is a product of MimesisIQ.
