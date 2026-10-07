# Changelog

## 0.1.2

Official property.bot logo and marketplace-ready copy.

- Replace the placeholder dual-house icon with the official house-"p" mark (white rounded plate, orange accent)
- Add `assets/logo.svg` and `assets/logo.png`; both manifests set `"logo": "assets/logo.svg"`
- README and AGENTS: live schemas plus the public product-card tool list
- H4 signed-in Grok Bot OAuth smoke passed 2026-10-06 (human evidence outside this package)
- Remaining human step: Cursor marketplace submit (and optional GitHub About/topics admin)

## 0.1.1

Public property.bot plugin package for Cursor and Grok Bot: host-managed WorkOS OAuth MCP at `https://mcp.property.bot/mcp` and the `/property-bot` housing skill.

- Manifests stay at 0.1.1 (`plugin.json`, `.cursor-plugin/plugin.json`)
- No API keys, env tokens, custom headers, or phone binding in `mcp.json`
- Skill: `connection_status` first, phone link, first-person profile/matches, self-only `send_text`

After merge: tag `v0.1.1`, publish a GitHub Release from this changelog, then human steps — Cursor marketplace submit and Sterling H4 signed-in Grok Bot smoke.
