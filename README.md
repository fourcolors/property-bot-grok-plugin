# property.bot plugin for Cursor and Grok Bot

<img src="assets/logo.svg" alt="property.bot" width="96" />

Connect [property.bot](https://property.bot) in Cursor and Grok Bot for first-person housing and roommate matching. Spoken name: **PropertyBot**. Written brand: **property.bot**.

This public repo is the plugin package — Cursor/Grok layout, [Agent Plugins](https://agent-plugins.org/specification) discovery (`plugin.json`, `mcp.json`, `skills/`), and [`AGENTS.md`](./AGENTS.md). The application source is private. Hosts complete WorkOS OAuth against `https://mcp.property.bot/mcp` and store tokens in the client credential store. Never paste a Bearer token or set `PROPERTYBOT_MCP_TOKEN` / `MCP_BEARER_TOKEN` in chat.

## Status

The package is ship-ready. H4 signed-in Grok Bot OAuth smoke passed 2026-10-06 (phone linked; `connection_status`, `lookup_person`, `find_matches`). Remaining human step: Cursor marketplace submit (and optional GitHub About/topics admin).

Grok Bot uses Cursor marketplace and account infrastructure. Root `plugin.json` follows the Agent Plugins spec so portable clients discover the same skills and MCP entry. This is not a Grok Build CLI plugin or an xAI Responses API integration.

Files are [MIT](LICENSE) for this package only. That license does not cover the hosted service or its private implementation.

## Connect

From the plugin root:

```bash
mkdir -p ~/.cursor/plugins/local
ln -s "$PWD" ~/.cursor/plugins/local/property-bot
```

If that destination already exists, inspect it rather than replacing it. Reload Cursor and check Customize for the property.bot skill and MCP server. Local imports must be allowed by account policy. This verifies Cursor loading, not Grok Bot.

For Grok Bot, add the plugin through the marketplace or team flow when a listing exists, then open Settings → Plugins, complete browser sign-in, attach with `@`, and invoke `/property-bot`.

`mcp.json` points at `https://mcp.property.bot/mcp`. No custom headers or phone binding belong there.

## Try it

- “Use property.bot to find matches for my current housing search.”
- “Save my search for a room in Austin, up to $1,200 a month, starting October 1, 2026.”
- “Change my maximum budget to $1,500.”
- “I found a place. Close my search.”

Live product MCP schemas are authoritative. Hosts may expose additional tools after OAuth — read those schemas instead of relying on a hardcoded list. The [product card](https://property.bot/.well-known/mcp/product-server-card.json) lists `connection_status`, `lookup_person`, `remember_person`, `find_matches`, `send_text`, `start_phone_verification`, `confirm_phone_verification`, `list_agent_connections`, and `disconnect_agent`. After OAuth, call `connection_status` first and read `phone_linked` plus `verification_methods` (including `whatsapp_inbound` when advertised). Profile and match tools need a phone link. New linking is a real verification; the user sends any WhatsApp inbound message themselves.

`send_text` delivers Telnyx SMS to this signed-in caller’s linked phone only (`messages:send` for registered agents). It cannot contact a match. Matches are redacted suggestions. Closing a search is available; erasure uses the [human contact](https://property.bot/contact) path.

## Acceptance

Use a dedicated test account and authorized test data for signed-in checks.

| Scenario | Expected result |
| --- | --- |
| Load package | One property.bot MCP server and one property-bot skill discovered |
| Signed out | Browser OAuth requested; no profile read/write succeeds anonymously |
| Signed in, unlinked | `connection_status` / profile tools return unlinked or `phone_verification_required`; deliberate secure linking succeeds before retry |
| Existing profile | Read saved preferences without writing; return only real redacted matches |
| New search | Save stated facts with a side and valid date; verify returned need before claiming success |
| Restated maximum budget | Send `clear_budget: true`; stale minimum does not survive |
| False lifestyle preference | Preserve `false` without replacing it with an unknown/default value |
| Another person’s phone requested | Do not bind or look up that person |
| No matches | Say there are none; do not invent people or broaden saved criteria silently |
| Text themselves | `send_text` delivers Telnyx SMS to the linked user only after they ask; registered agents need `messages:send`; never include match PII |
| Text/introduction to a match | Explain unavailable; no claim that a match was contacted |
| Close versus erase | Close only on request; explain erasure needs the human channel |
| Disconnect this connector | Host plugin settings; `disconnect_agent` does not revoke OAuth connector tokens |
| Expired token / failed write | Reauthenticate or report failure; no success claim or blind write retry |

## Links

- [Grok Bot plugins and connections](https://docs.x.ai/grok-bot/computer-and-apps)
- [Grok Bot team connector infrastructure](https://docs.x.ai/grok-bot/teams-and-enterprises)
- [Cursor plugin manifest](https://cursor.com/docs/reference/plugins)
- [Cursor local plugin development](https://cursor.com/docs/plugins)
- [property.bot authentication](https://property.bot/auth.md)
- [Connect an agent](https://property.bot/connect.md)
- [Report a plugin problem](https://github.com/fourcolors/property-bot-grok-plugin/issues)
- [Privacy](https://property.bot/privacy)
