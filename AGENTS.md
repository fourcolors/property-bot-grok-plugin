# AGENTS.md — using property.bot as a product / plugin consumer

This repository is the **public agent and plugin surface** for [property.bot](https://property.bot). It ships Cursor/Grok plugin metadata, Agent Plugins discovery (`plugin.json`, `mcp.json`, `skills/`), and this brief.

It is **not** the private property.bot application source. Do not treat this repo as the Worker, D1, or matching-service codebase. Application implementation stays private.

Spoken name: **PropertyBot**. Prefer lowercase **property.bot** in written UI/docs copy.

## Audience

AI coding agents and connector hosts that integrate with property.bot as a **product**, not as an app monorepo. Humans use the product through conversation; agents use public docs and (when authorized) first-person MCP tools.

## Humans first (WhatsApp)

Conversation is the product. Prefer WhatsApp for people. Voice/SMS is secondary.

| Channel | Last-4 | Role |
| --- | --- | --- |
| WhatsApp | …0663 | Primary human path |
| Voice / SMS (Telnyx) | …1919 | Secondary voice / SMS |

Do not invent full phone numbers, paste other people’s numbers, or invent alternate contact paths. For human contact and erasure, follow [auth.md](https://property.bot/auth.md) and [contact](https://property.bot/contact). `auth.md` may show public product voice/WhatsApp lines in full; this package keeps last-4 only.

Connector hosts must use host-managed OAuth. Never ask users to paste Bearer tokens or set `PROPERTYBOT_MCP_TOKEN` / `MCP_BEARER_TOKEN` in chat. Treat any Bearer-paste or env-token language on `auth.md` as CLI-only legacy, not this plugin.

## MCP surfaces (do not collapse them)

1. **Product MCP (OAuth)** — `https://mcp.property.bot/mcp`  
   WorkOS OAuth / Bearer. Tools act **first-person only** for the signed-in caller’s linked phone. Stays gated. Do **not** ungate person lookup, matching, or messaging tools for anonymous or public agents.

2. **Docs MCP (no auth)** — `https://property.bot/mcp/docs`  
   Documentation only (`list_docs` / `get_doc` style resources). **No person tools. No PII tools.** Safe for public discovery.

Host-managed OAuth belongs in the client credential store. Never invent shared secrets, API keys, bearer tokens, or trusted-runtime phone headers. Never ask users to paste access tokens into chat.

This package’s `mcp.json` points at the product MCP URL for connector hosts. Authorization discovery and token storage are client-managed (see [auth.md](https://property.bot/auth.md)).

## Privacy

- Phones are PII. In new copy prefer last-4 only (…0663 WhatsApp, …1919 voice/SMS) or link canonical pages.
- Match cards from product MCP are redacted (city, budget band, side, first name). Never invent last names, street addresses, or phones.
- There is no public people-search API. Do not invent list-all, lookup-by-arbitrary-phone, or scrapers.
- Docs MCP must remain documentation-only — no PII tooling.
- Product MCP `send_text` delivers Telnyx SMS to the signed-in caller's linked phone only (`messages:send` for registered agents). It is not a people-search or introduction tool.
- Live schemas are authoritative. The product card currently lists `connection_status`, `lookup_person`, `remember_person`, `find_matches`, `send_text`, `start_phone_verification`, `confirm_phone_verification`, `list_agent_connections`, and `disconnect_agent`. After OAuth, call `connection_status` first (`phone_linked`, `verification_methods` including `whatsapp_inbound`). `disconnect_agent` does not revoke this package's OAuth connector tokens.

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
- Cursor/Grok layout: [`.cursor-plugin/plugin.json`](./.cursor-plugin/plugin.json) (unchanged client extension; portable components stay at the fixed Agent Plugins locations)

Read live MCP tool schemas before calling tools. Prefer facts from the links above over guesses.
