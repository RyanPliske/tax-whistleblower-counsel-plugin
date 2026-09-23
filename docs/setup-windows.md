# Setting up your machine (Windows)

One-time setup for the attorney's machine: Claude Code with the Tax Whistleblower Counsel
plugin, and the skill capture loop (spec §2.11) that sends the skills your Claude writes to
Ryan for curation. Your documents never leave your machine; only skill files and `CLAUDE.md`
are synced, and a check refuses anything that looks like an SSN or EIN.

## 1. Install the tools

1. **Node.js** (LTS) from nodejs.org. Accept the defaults.
2. **Git for Windows** from git-scm.com. Accept the defaults (this includes Git Bash, which
   Claude Code uses to run its hooks).
3. **GitHub CLI** from cli.github.com, then in a new terminal: `gh auth login` and follow the
   prompts (GitHub.com, HTTPS, log in with a browser). Ryan has added your account to the
   private drafts repo; this is the one reason you need a GitHub account.
4. **Claude Code**: follow the install steps at code.claude.com/docs, then `claude` once to sign
   in with your Claude account.

## 2. Your working folder

Pick one folder for your whistleblower work, for example `C:\Law`. Always start Claude Code
from inside it (`cd C:\Law` then `claude`). **Never run `git init` in this folder.** It holds
privileged material and it stays on your machine.

Any standing instructions you want Claude to follow go in `C:\Law\CLAUDE.md`. That file is
synced to Ryan (it is the second-most useful thing after skills), so keep client names out of
it; put matter facts in the conversation, not in `CLAUDE.md`.

## 3. The drafts repo becomes your skills folder

In a terminal:

```powershell
cd $env:USERPROFILE\.claude
if (Test-Path skills) { Rename-Item skills skills-before-twc }
gh repo clone RyanPliske/tax-whistleblower-counsel-skill-drafts skills
node skills\_sync\install.mjs
```

The last line registers a `SessionEnd` hook in `%USERPROFILE%\.claude\settings.json` and
turns on the pre-commit check. It prints what it did. From now on, every skill your Claude
writes lands in the repo, and each time a session ends the hook commits and pushes within
about a minute. If you had skills in the old folder, copy the ones you want into `skills\`.

Auto-memory is **not** synced by default. If you want Ryan to see it too, run
`node skills\_sync\install.mjs --sync-memory` once. To stop, delete
`skills\_sync\.sync-memory`.

## 4. The plugin

```powershell
claude plugin marketplace add RyanPliske/tax-whistleblower-counsel-plugin
claude plugin install tax-whistleblower-counsel@pliske-legal
```

Then start `claude` in `C:\Law`, type `/mcp`, choose **tax-whistleblower-counsel**. A browser
opens showing a Tax Whistleblower Counsel sign-in page with two buttons — take **Sign in with
Microsoft** and use your firm `@twlfusa.com` account, the same one you use for Outlook. It goes
straight to the firm's Microsoft page; there is no separate password to remember. Then press
**Approve**.

When the list shows the server as connected, type `/ping` or ask "ping the counsel server" to
confirm the seat and the corpus version.

If sign-in is refused, send Ryan the exact message. "No seat" means your address has not been
invited yet; anything else he will want to see verbatim.

If the browser never opens, run this once instead and repeat `/mcp`:

```powershell
claude mcp add --transport http --callback-port 8765 tax-whistleblower-counsel https://twc-api-2dy6pokgjq-uc.a.run.app/mcp
```

Updates: `claude plugin marketplace update pliske-legal` then `claude plugin update tax-whistleblower-counsel`.

## 5. Check it works

1. In `C:\Law`, start `claude` and ask: *"Screen this claim"*. The `claim-intake` skill should
   ask you three or four questions.
2. Ask Claude to *"write a skill called my-intake-notes that reminds you how I like intake
   memos formatted"*, then `/exit`.
3. Within a minute, `skills\_sync\last-run.log` on your machine ends with `pushed`, and the
   skill appears at github.com/RyanPliske/tax-whistleblower-counsel-skill-drafts. If the log
   says `failed`, send Ryan the line.

## What is and is not synced

| Synced | Not synced |
|---|---|
| `skills\<name>\SKILL.md` files your Claude writes | anything in `C:\Law` other than `CLAUDE.md` |
| `C:\Law\CLAUDE.md` | your conversations and transcripts |
| auto-memory, only after `--sync-memory` | your Claude account or Google sign-in |

The pre-commit check refuses a commit that contains an SSN or EIN pattern, or a file outside
those paths, and names the line. Fix it and end the session again.

## claude.ai instead of Claude Code

The server also works as a custom connector in claude.ai (Settings → Connectors → Add custom
connector → `https://twc-api-2dy6pokgjq-uc.a.run.app/mcp`), and the five skills can be uploaded
there as custom skills. Sub-agents and the capture loop are Claude Code only.
