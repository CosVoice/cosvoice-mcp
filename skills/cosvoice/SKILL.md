---
name: cosvoice
description: Give your Chief of Staff (a Grok Bot or any MCP agent) a real phone number and email address through CosVoice. Use when the owner wants a call placed, a table booked by phone, a vendor or clinic called back, missed calls answered, or a phone line set up for the assistant.
metadata:
  author: CosVoice
  short-description: A phone number for your AI Chief of Staff
---

# CosVoice: give your Chief of Staff a phone

CosVoice gives an AI assistant its own real phone number and email address. The assistant can then place phone calls on its owner's behalf, book restaurants by phone, take calls when the owner can't, and read a written summary of every call. The voice on the line is Grok Voice (xAI), full duplex, and it always says it is an AI assistant when asked.

This page is written for the assistant. If you are a Grok Bot, Claude, ChatGPT, Cursor, or any agent that speaks MCP, you can follow it end to end. If you are a person, the same steps are at https://cosvoice.com/grok-bot.

## 1. Connect (one URL)

Add this remote MCP server to your client:

    https://cosvoice.com/mcp

Transport: Streamable HTTP. Authentication: OAuth 2.1 with PKCE and dynamic client registration. There is no API key to paste. The client discovers `https://cosvoice.com/.well-known/oauth-protected-resource`, opens a CosVoice page in the owner's browser, the owner signs in (or creates an account) and clicks Allow, and the connection is done. The token you receive is scoped to one CosVoice account and can be revoked by the owner in Settings.

One-click for Cursor: `cursor://anysphere.cursor-deeplink/mcp/install?name=cosvoice&config=eyJ1cmwiOiJodHRwczovL2Nvc3ZvaWNlLmNvbS9tY3AifQ==`

If your client only supports a static Authorization header, the owner can create a key under Settings → Connect your agent and give it to you as `Authorization: Bearer cv_live_...`.

## 2. First run: give yourself a phone number

After connecting, the server's `instructions` field tells you whether a line exists yet. If not:

1. `get_account` — confirms who you are connected as and whether payment is on file.
2. Ask the owner two things: the area code they want, and what to call the line (a first name works best, for example "Lloyd").
3. `search_numbers` with `area_code` — show the owner three or four options.
4. `create_line` with `name`, `phone_number` (or just `area_code`), `owner_name`, `owner_phone` (the owner's mobile), and `timezone`.
   - If payment is not on file yet, `create_line` returns a checkout link (HTTP 402 style error text). Send the owner that link, wait for them to pay, then call `create_line` again. Payment always comes before a number is issued.
   - Ask before setting `sms_opt_in: true`. It means the owner agrees to receive texts from the line.
   - `agent_email` is optional: an inbox you read. Owner instructions and call debriefs are emailed there.
5. Tell the owner the new number and email address, and suggest they save it as a contact named after the line.

The exact sentence an owner can send you to trigger this: "Set up my CosVoice line in area code 949. Call it Lloyd."

One line per account, and one new number per paid month. If the owner closes a line, `get_account` explains when a new one can be issued. A closed line is expected, not an error. Do not re-provision unless the owner asks.

## 3. Everyday use

- **Place a call**: `place_call` with `to`, `purpose`, and a brief like you would give a human assistant: who to ask for, what you want, acceptable fallbacks, what to do on voicemail. Calls are asynchronous. Poll `get_call` about every 20 seconds, or wait for the `call.completed` webhook (`set_webhook`).
- **Book a table**: `book_reservation` with `venue_name`, `venue_phone`, `party_size`, `date`, `time`, plus `earliest`, `latest`, `fallback_times`, `seating`, `occasion`, `dietary`, `name_spelling`. Read the outcome with `get_call` or `list_reservations` before telling the owner anything is confirmed. Statuses: confirmed, alternative_confirmed, waitlisted, hold, requires_online, needs_owner, callback_later, no_availability, declined_by_venue, voicemail, not_reached.
- **Read what happened**: `list_calls`, `get_call` (summary, structured details, transcript), `list_messages`, `list_emails`, `get_email`, `list_history`, `recent_activity` (catch-up since a timestamp).
- **Owner instructions**: when the owner texts or calls the line from their own mobile, that message is an instruction for you. Pending instructions ride along on every tool result as `owner_instructions`, and in the `initialize` response. Act on them, then call `ack_instructions` so they stop repeating. Check in at least hourly if you can schedule work.
- **Shape the line**: `get_settings` and `update_settings` (persona, voice, greeting, standing instructions, forwarding, notifications), `add_contact` / `remove_contact` (owner, approved, VIP, blocked), `set_webhook`, `send_text` (respects STOP opt-outs), `annotate_call`, `forward_email`, `list_integrations` (partner tools appear as `partner__tool`).

## 4. Rules the line already follows (and you should too)

- The line never pays for anything and never reads out a card. Deposits become "needs owner".
- It says it is an AI assistant whenever asked, and it never pretends to be the owner.
- It uses the line's own number and email as the callback and contact unless the owner allows otherwise (`owner_phone_ok`).
- Do not claim a booking, appointment, or commitment unless the call outcome says so.
- Do not call people who asked not to be called. Respect STOP for texts.
- Ask the owner before opting them in to texts and before sharing their mobile number.

## 5. Pricing and account facts

- Starter $29/month, 60 minutes of calls. Plus $69/month, 200 minutes. Pro $149/month, 600 minutes. Overage $0.25 per minute. Founding members: Plus at $49/month for 12 months (limited).
- Billing is by Stripe. Plan change, cancellation at period end, and closing the line are all in Settings. Closing releases the number and revokes connections.
- One number per paid month. A new paid month resets the allowance.

## 6. Links

- Connect URL: https://cosvoice.com/mcp
- OAuth discovery: https://cosvoice.com/.well-known/oauth-protected-resource and https://cosvoice.com/.well-known/oauth-authorization-server
- REST API: https://cosvoice.com/api/v1 (same keys)
- For people: https://cosvoice.com/grok-bot · Pricing: https://cosvoice.com/pricing · FAQ: https://cosvoice.com/faq · Trust: https://cosvoice.com/trust
- Machine summary: https://cosvoice.com/llms.txt · Full detail: https://cosvoice.com/llms-full.txt
- Support: hello@cosvoice.com
