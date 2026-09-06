# Property Bot for Grok Bot

This **public** repository is the agent/plugin surface for [property.bot](https://property.bot):
Cursor/Grok plugin layout, [Agent Plugins](https://agent-plugins.org/specification)
discovery (`plugin.json`, `mcp.json`, `skills/`), and [`AGENTS.md`](./AGENTS.md) for
AI agents that consume the product. The Property Bot **application source is private**;
this repo is not that codebase.

Initial plugin package for personal housing and roommate searches. It contains
an OAuth MCP connection and the `/property-bot` skill. No API key, local server,
package install, or service credential is required by the package.

## Status and format

This is a development package, not a published marketplace listing. It uses the
Cursor plugin manifest because Grok Bot's documented connector system uses the
Cursor marketplace and account infrastructure. Root `plugin.json` follows the
Agent Plugins specification so portable clients can discover the same skills and
MCP entry. Grok Bot installation and a full signed-in conversation still need
validation in the target app. This is not a Grok Build CLI plugin or an xAI
Responses API integration.

This repository contains only the plugin package, including its hidden
`.cursor-plugin` directory. The only configured server is `https://mcp.property.bot/mcp`
(OAuth-gated product MCP). OAuth tokens belong in the host's credential store.
No custom headers or user-supplied phone binding belong in `mcp.json`.

## Develop locally

Cursor documents local plugin loading as a development harness. Run these
commands from the plugin root (the public repository root, or `plugins/grok-bot`
in the private application checkout):

```bash
mkdir -p ~/.cursor/plugins/local
ln -s "$PWD" ~/.cursor/plugins/local/property-bot
```

If that destination already exists, inspect it rather than replacing it. Reload
Cursor and inspect Customize for the Property Bot skill and MCP server. Local
imports must be allowed by the account's policy. This verifies Cursor loading;
it does not prove that Grok Bot loads local Cursor directories.

For Grok Bot, distribute through the supported marketplace/team plugin flow,
then open Settings → Plugins, add Property Bot, and complete browser sign-in.
Attach the connector with `@` and invoke the skill with `/property-bot` when it
is available to the Bot. There is no public Property Bot listing yet.

The plugin files are distributed under the [MIT license](LICENSE). This license
does not cover the hosted Property Bot service or its private implementation.
Marketplace review and signed-in Grok Bot acceptance testing are still pending.

## Try it

- “Use Property Bot to find matches for my current housing search.”
- “Save my search for a room in Austin, up to $1,200 a month, starting October 1, 2026.”
- “Change my maximum budget to $1,500.”
- “I found a place. Close my search.”

The six user tools are `lookup_person`, `remember_person`, `find_matches`,
`send_text`, `start_phone_verification`, and `confirm_phone_verification`.
The server's live schemas are authoritative. A phone link is required for
profile and match operations. New linking sends a real verification code and
requires secure code entry supported by the host; validate that flow before
offering this to new users. An already linked account can skip linking.

`send_text` saves a local record; it does not deliver SMS. Matches are redacted
suggestions. The connector cannot send introductions, book rooms, or erase a
profile. Closing a search is available; erasure uses the human contact path.

## Acceptance checks

Use a dedicated test account and authorized test data for signed-in checks.

| Scenario | Expected result |
| --- | --- |
| Load package | One Property Bot MCP server and one property-bot skill discovered |
| Signed out | Browser OAuth requested; no profile read/write succeeds anonymously |
| Signed in, unlinked | Profile tools return `phone_verification_required`; deliberate secure linking succeeds before retry |
| Existing profile | Read saved preferences without writing; return only real redacted matches |
| New search | Save stated facts with a side and valid date; verify returned need before claiming success |
| Restated maximum budget | Send `clear_budget: true`; stale minimum does not survive |
| False lifestyle preference | Preserve `false` without replacing it with an unknown/default value |
| Another person's phone requested | Do not bind or look up that person |
| No matches | Say there are none; do not invent people or broaden saved criteria silently |
| Text/introduction request | Explain unavailable delivery; no claim that `send_text` sends SMS |
| Close versus erase | Close only on request; explain erasure needs the human channel |
| Expired token / failed write | Reauthenticate or report failure; no success claim or blind write retry |

## Evidence and references

On 2026-09-04, the public MCP metadata returned HTTP 200 and advertised a WorkOS
authorization server. That server's discovery document provided authorization,
token, registration, and S256 PKCE support. An unauthenticated MCP request
returned HTTP 401 with a protected-resource metadata challenge. These checks
do not prove OAuth completion, phone linking, plugin loading, or match execution.

- [Grok Bot plugins and connections](https://docs.x.ai/grok-bot/computer-and-apps)
- [Grok Bot team connector infrastructure](https://docs.x.ai/grok-bot/teams-and-enterprises)
- [Cursor plugin manifest and marketplace reference](https://prod.cursor.com/docs/reference/plugins)
- [Cursor local plugin development](https://prod.cursor.com/docs/plugins)
- [Property Bot authentication](https://property.bot/auth.md)
- [Report a plugin problem](https://github.com/fourcolors/property-bot-grok-plugin/issues)
- [Privacy and data handling](https://property.bot/privacy)
