# AGENTS.md — using property.bot as a product / plugin consumer

Public plugin surface for [property.bot](https://property.bot) (spoken: **PropertyBot**). Not the private application source.

AI agents use public docs and, when authorized, first-person MCP tools. Humans use conversation.

## Humans first (WhatsApp)

| Channel | Last-4 | Role |
| --- | --- | --- |
| WhatsApp | …0663 | Primary human path |
| Voice / SMS (Telnyx) | …1919 | Secondary voice / SMS |

Do not invent full phone numbers, paste other people’s numbers, or invent contact paths. For contact and erasure, follow [auth.md](https://property.bot/auth.md) and [contact](https://property.bot/contact). This package keeps last-4 only.

## MCP surfaces

1. **Product MCP (OAuth)** — `https://mcp.property.bot/mcp`
   WorkOS OAuth. Tools act first-person for the signed-in caller’s linked phone. Do not ungate lookup, matching, or messaging for anonymous agents.

2. **Docs MCP (no auth)** — `https://property.bot/mcp/docs`
   Documentation only (`list_docs` / `get_doc` style). No person or PII tools.

This package’s `mcp.json` points at the product MCP URL. Hosts store OAuth tokens in the client credential store. Never ask users to paste Bearer tokens or set `PROPERTYBOT_MCP_TOKEN` / `MCP_BEARER_TOKEN` in chat.

## Privacy

- Phones are PII. Prefer last-4 (…0663 WhatsApp, …1919 voice/SMS) or canonical links.
- Match cards are redacted (city, budget band, side, first name). Never invent last names, street addresses, or phones.
- No public people-search API. Do not invent list-all, lookup-by-arbitrary-phone, or scrapers.
- Docs MCP stays documentation-only.
- `send_text` delivers Telnyx SMS to the signed-in caller’s linked phone only (`messages:send` for registered agents). Not an introduction tool.
- Live schemas are authoritative. The public product card lists `connection_status`, `lookup_person`, `remember_person`, `find_matches`, `send_text`, `start_phone_verification`, `confirm_phone_verification`, `list_agent_connections`, and `disconnect_agent`. Hosts may expose additional tools after OAuth — read live schemas. After OAuth, call `connection_status` first (`phone_linked`, `verification_methods` including `whatsapp_inbound`). `disconnect_agent` does not revoke this package’s OAuth connector tokens.

## Canonical links

| Resource | URL |
| --- | --- |
| Home | https://property.bot |
| Auth contract | https://property.bot/auth.md |
| Connect an agent | https://property.bot/connect.md |
| Product MCP card | https://property.bot/.well-known/mcp/product-server-card.json |
| OpenAPI (public health/info) | https://property.bot/openapi.json |
| Agent brief | https://property.bot/llms.txt |
| Privacy | https://property.bot/privacy |
| Contact | https://property.bot/contact |
| Developers | https://property.bot/developers |
| Docs | https://property.bot/docs |

## This package

- Agent Plugins manifest: root [`plugin.json`](./plugin.json) ([spec](https://agent-plugins.org/specification))
- MCP config: [`mcp.json`](./mcp.json) → product MCP at `https://mcp.property.bot/mcp`
- Skill: [`skills/property-bot/`](./skills/property-bot/)
- Cursor/Grok layout: [`.cursor-plugin/plugin.json`](./.cursor-plugin/plugin.json)

Read live MCP tool schemas before calling tools. Prefer facts from the links above over guesses.
