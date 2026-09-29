# Setting up your machine (Windows)

One-time setup for the attorney's machine: Claude Code with the Tax Whistleblower Counsel
plugin, and the morning digest (spec §2.11) that tells Ryan which skills to write for you. Your
documents and conversations never leave your machine; only the digest, skill files and
`CLAUDE.md` are synced, and a check refuses anything that looks like an SSN or EIN.

## 1. Install the tools

1. **Node.js** (LTS) from nodejs.org. Accept the defaults.
2. **Git for Windows** from git-scm.com. Accept the defaults (this includes Git Bash, which
   Claude Code uses to run its hooks).
3. **You do not need a GitHub account.** Ryan sets up a deploy key instead — a key that opens
   one repository and identifies no one, so nothing here is tied to a login of yours and there
   is no second password or 2FA device to keep. Skip to §3; he does that part.
4. **Claude Code**: follow the install steps at code.claude.com/docs, then `claude` once to sign
   in with your Claude account.

## 2. Your working folder

Pick one folder for your whistleblower work, for example `C:\Law`. Always start Claude Code
from inside it (`cd C:\Law` then `claude`). **Never run `git init` in this folder.** It holds
privileged material and it stays on your machine.

Any standing instructions you want Claude to follow go in `C:\Law\CLAUDE.md`. That file is
synced to Ryan, so keep client names out of it; put matter facts in the conversation, not in
`CLAUDE.md`.

## 3. The drafts repo becomes your skills folder

**What this does for you:** each weekday morning your laptop reads the Claude sessions since the
day before — on this machine; the conversations never leave it — and sends Ryan a short digest:
what you asked for more than once, where Claude got in your way, and what the server didn't
have. You never have to ask for a skill; Ryan writes them from the digest and they reach you as
plugin updates.

### At the keyboard (Ryan, once, about 20 minutes)

Everything below runs in **PowerShell** (Start → type "PowerShell"). Do not sign in to GitHub on
this machine.

1. **Check the tools.** Each should print a version or a path:
   ```powershell
   node --version
   git --version
   where.exe claude
   ```
   No Node → install the LTS from nodejs.org, then **open a new PowerShell window**. If
   `where.exe claude` finds nothing, the scheduled job won't either; fix that before going on.

2. **Make the key.** When it asks for a passphrase, press **Enter twice** (no passphrase — the
   job runs unattended, and a prompt nobody answers fails silently; that is also why the key
   reaches this one repository and nothing else):
   ```powershell
   ssh-keygen -t ed25519 -C "twc-drafts tpliske" -f $env:USERPROFILE\.ssh\twc_drafts
   ```

3. **Get the public key to Ryan's Mac.** It is not a secret. Copy it, then text or email it to
   yourself:
   ```powershell
   Get-Content $env:USERPROFILE\.ssh\twc_drafts.pub | Set-Clipboard
   ```

4. **Register it — on Ryan's Mac**, with write access (a read-only key clones fine and fails
   only at push, the most confusing failure available). Save the pasted line as
   `twc_drafts.pub`, then:
   ```bash
   gh repo deploy-key add twc_drafts.pub --allow-write -R RyanPliske/tax-whistleblower-counsel-skill-drafts -t "tpliske laptop"
   ```
   (Or on github.com: the repo → Settings → Deploy keys → Add, tick **Allow write access**.)

5. **Tell ssh which key to use**, back on the laptop. Written from PowerShell on purpose:
   Notepad would save it as `config.txt`, which ssh ignores.
   ```powershell
   Add-Content -Encoding ascii $env:USERPROFILE\.ssh\config "`nHost github-twc`n  HostName github.com`n  User git`n  IdentityFile ~/.ssh/twc_drafts`n  IdentitiesOnly yes`n"
   ssh -T git@github-twc
   ```
   Type `yes` to trust GitHub's fingerprint. The answer must name the **repository**:
   `Hi RyanPliske/tax-whistleblower-counsel-skill-drafts! You've successfully authenticated…`.
   `Permission denied` means step 4 didn't take.

6. **Clone it as the skills folder**, with his name on the commits, and install:
   ```powershell
   cd $env:USERPROFILE\.claude
   if (Test-Path skills) { Rename-Item skills skills-before-twc }
   git clone git@github-twc:RyanPliske/tax-whistleblower-counsel-skill-drafts.git skills
   cd skills
   git config user.name "Thomas C. Pliske"
   git config user.email "tpliske@twlfusa.com"
   node _sync\install.mjs
   ```
   It prints five lines; expect `morning job: "TWC morning digest" registered` and
   `transcripts kept: 365 days`. If `skills-before-twc` now exists, copy any skills he wants
   into `skills\`.

7. **Preview before anything leaves.** Read this together — it is exactly what would be sent:
   ```powershell
   $env:TWC_DRY_RUN=1; node _sync\morning.mjs; Remove-Item Env:TWC_DRY_RUN
   ```
   It takes a minute or two. It covers his last week of sessions; `nothing to read` means there
   were none (fine — do one short session in `C:\Law` and repeat). If it names a client or a
   figure, stop and tell Ryan before step 8; the fix is in `_sync\digest-prompt.md`.

8. **The real run**, through the scheduled task so the task itself is proven:
   ```powershell
   Start-ScheduledTask -TaskName "TWC morning digest"
   ```
   Wait a few minutes, then:
   ```powershell
   Get-Content _sync\last-run.log -Tail 3
   ```
   `pushed (…)` and a new file under `digests/` on GitHub means done. `failed: …` → the
   `twc-sync-doctor` skill in this folder walks the fix; ask Claude *"Skills aren't syncing.
   Diagnose it."*

Auto-memory is **not** synced by default. If you want Ryan to see it too, run
`node skills\_sync\install.mjs --sync-memory` once. To stop, delete
`skills\_sync\.sync-memory`.

## 4. The plugin

(Already done on the attorney's laptop 2026-09-23; skip unless setting up a new machine.)

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
2. The digest is checked by §3 steps 7–8. After that, the only thing to watch is the next
   weekday morning: `skills\_sync\last-run.log` gains a `pushed` or `nothing to read` line
   without anyone touching it. That proves the schedule, not just the script.

## What is and is not synced

| Synced | Not synced |
|---|---|
| the morning digest and its skill drafts | your conversations and transcripts (read here, never sent) |
| `skills\<name>\SKILL.md` files your Claude writes | anything in `C:\Law` other than `CLAUDE.md` |
| `C:\Law\CLAUDE.md` | |
| auto-memory, only after `--sync-memory` | your Claude account or Microsoft sign-in |

The pre-commit check refuses a commit that contains an SSN or EIN pattern, or a file outside
those paths, and names the line. Fix it and run the task again.

## claude.ai instead of Claude Code

The server also works as a custom connector in claude.ai (Settings → Connectors → Add custom
connector → `https://twc-api-2dy6pokgjq-uc.a.run.app/mcp`), and the five skills can be uploaded
there as custom skills. Sub-agents and the capture loop are Claude Code only.
