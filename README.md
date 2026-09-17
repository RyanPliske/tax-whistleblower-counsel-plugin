# tax-whistleblower-counsel-plugin

**Charter:** the Claude plugin for Tax Whistleblower Counsel — five skills and one agent that do
IRC §7623 work with citations the server verified — and the private marketplace that
distributes it. The server itself is a separate, private repo; this one holds no secrets, only
skills and a URL. Design: `whistleblower-mcp/docs/SPEC.md` (§2.8 plugin, §5 skills, §2.11 the
skill capture loop).

## Install

```bash
claude plugin marketplace add RyanPliske/tax-whistleblower-counsel-plugin
claude plugin install tax-whistleblower-counsel@pliske-legal
```

Then `/mcp` in Claude Code to sign in. In claude.ai, add the server URL as a custom connector
and load the skills separately (SPEC §2.8).

## What's here

| Path | What it is |
|---|---|
| `.claude-plugin/marketplace.json` | The `pliske-legal` marketplace: one plugin, at `./plugin`. |
| `plugin/.claude-plugin/plugin.json` | Plugin manifest. |
| `plugin/.mcp.json` | The remote server (`type: "http"`), `https://twc-api-2dy6pokgjq-uc.a.run.app/mcp`. Claude Code discovers OAuth from the 401. |
| `plugin/skills/` | `claim-intake`, `form-211-draft`, `evidence-index`, `award-memo`, `citation-check`. Every skill carries the same citation, boundary, and disclaimer preamble, names tools only (never argument shapes), and ends by running `citation-check`. The tools themselves are documented in the server repo's generated `docs/TOOLS.md`. |
| `plugin/agents/authority-researcher.md` | Read-only research sub-agent (Claude Code only). |
| `docs/setup-windows.md` | The attorney's one-time setup: tools, the plugin, and the skill capture loop whose hook lives in the private `tax-whistleblower-counsel-skill-drafts` repo. |

**Not legal advice.** The plugin returns reference material and arithmetic for a licensed
attorney's review; see the disclaimer every tool result carries.
