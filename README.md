# property.bot plugin for Cursor and Grok Bot

This **public** repository is the agent/plugin surface for [property.bot](https://property.bot):
Cursor/Grok plugin layout, [Agent Plugins](https://agent-plugins.org/specification)
discovery (`plugin.json`, `mcp.json`, `skills/`), and [`AGENTS.md`](./AGENTS.md) for
AI agents that consume the product. The property.bot **application source is private**;
this repo is not that codebase.

Spoken name: **PropertyBot**. Written brand: **property.bot**.

Initial plugin package for personal housing and roommate searches. It contains
an OAuth MCP connection and the `/property-bot` skill. No API key, local server,
package install, or service credential is required by the package.

## Status and format

This is a development package, not a published Cursor/Grok marketplace listing.
It uses the Cursor plugin manifest because Grok Bot's documented connector system
uses the Cursor marketplace and account infrastructure. Root `plugin.json`
follows the Agent Plugins specification so portable clients can discover the
same skills and MCP entry. Grok Bot installation and a full signed-in
conversation still need validation in the target app. This is not a Grok Build
CLI plugin or an xAI Responses API integration.

This repository contains only the plugin package, including its hidden
`.cursor-plugin` directory. The only configured server is `https://mcp.property.bot/mcp`
(OAuth-gated product MCP). OAuth tokens belong in the host's credential store.
No custom headers or user-supplied phone binding belong in `mcp.json`.

## Develop locally

Cursor documents local plugin loading as a development harness. Run these
commands from the plugin root:

```bash
mkdir -p ~/.cursor/plugins/local
ln -s "$PWD" ~/.cursor/plugins/local/property-bot
```

If that destination already exists, inspect it rather than replacing it. Reload
Cursor and inspect Customize for the property.bot skill and MCP server. Local
imports must be allowed by the account's policy. This verifies Cursor loading;
it does not prove that Grok Bot loads local Cursor directories.

For Grok Bot, distribute through the supported marketplace/team plugin flow,
then open Settings → Plugins, add property.bot, and complete browser sign-in.
Attach the connector with `@` and invoke the skill with `/property-bot` when it
is available to the Bot. There is no public Cursor/Grok listing yet.

The plugin files are distributed under the [MIT license](LICENSE). This license
does not cover the hosted property.bot service or its private implementation.
C1/H1–H3 package copy is aligned with live MCP. Marketplace submit stays
gated on merge of this PR plus the site PR so live matches this tip, and on
Sterling **H4** signed-in Grok Bot OAuth smoke evidence. A Cloud VM cannot
complete H4.

## Try it

- “Use property.bot to find matches for my current housing search.”
- “Save my search for a room in Austin, up to $1,200 a month, starting October 1, 2026.”
- “Change my maximum budget to $1,500.”
- “I found a place. Close my search.”

Live product MCP schemas are authoritative. Do not treat any closed six-tool
list as complete. The product card currently advertises `connection_status`,
`lookup_person`, `remember_person`, `find_matches`, `send_text`,
`start_phone_verification`, `confirm_phone_verification`,
`list_agent_connections`, and `disconnect_agent`. After OAuth, call
`connection_status` first and read `phone_linked` plus `verification_methods`
(including `whatsapp_inbound` when advertised). A phone link is required for
profile and match operations. New linking is a real verification; the user
must send any WhatsApp inbound message themselves. An already linked account
can skip linking.

`send_text` delivers Telnyx SMS to this signed-in caller's linked phone only.
Registered agents need `messages:send`. It cannot contact a match. Matches are
redacted suggestions. The connector cannot send introductions, book rooms, or
erase a profile. Closing a search is available; erasure uses the human contact
path.

## Acceptance checks

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
| Another person's phone requested | Do not bind or look up that person |
| No matches | Say there are none; do not invent people or broaden saved criteria silently |
| Text themselves | `send_text` delivers Telnyx SMS to the linked user only after they ask; registered agents need `messages:send`; never include match PII |
| Text/introduction to a match | Explain unavailable; no claim that a match was contacted |
| Close versus erase | Close only on request; explain erasure needs the human channel |
| Disconnect this connector | Host plugin settings; `disconnect_agent` does not revoke OAuth connector tokens |
| Expired token / failed write | Reauthenticate or report failure; no success claim or blind write retry |

## Evidence and references

On 2026-09-04, the public MCP metadata returned HTTP 200 and advertised a WorkOS
authorization server. That server's discovery document provided authorization,
token, registration, and S256 PKCE support. An unauthenticated MCP request
returned HTTP 401 with a protected-resource metadata challenge. These checks
do not prove OAuth completion, phone linking, plugin loading, or match execution.

- [Grok Bot plugins and connections](https://docs.x.ai/grok-bot/computer-and-apps)
- [Grok Bot team connector infrastructure](https://docs.x.ai/grok-bot/teams-and-enterprises)
- [Cursor plugin manifest and marketplace reference](https://cursor.com/docs/reference/plugins)
- [Cursor local plugin development](https://cursor.com/docs/plugins)
- [property.bot authentication](https://property.bot/auth.md) — hosts use
  browser OAuth; never paste Bearer tokens or set `PROPERTYBOT_MCP_TOKEN` /
  `MCP_BEARER_TOKEN` in chat (CLI-only legacy on that page). Full DIDs there
  are public product lines; this package keeps last-4 only.
- [Connect an agent](https://property.bot/connect.md)
- [Report a plugin problem](https://github.com/fourcolors/property-bot-grok-plugin/issues)
- [Privacy and data handling](https://property.bot/privacy)
