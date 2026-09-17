---
name: claim-intake
description: Screen a prospective IRS whistleblower claim under IRC §7623. Gathers the screen_claim inputs, runs it, expands the flags with cited authorities, and produces an intake memo. Use when the user says "new whistleblower matter", "screen this claim", "does this qualify", or "which track is this".
---

# claim-intake

> **Citation rule.** Every statute, regulation, IRM, or case citation in your output must come
> from a `search_authorities` or `get_authority` result in this conversation, quoted from its
> `citation` object with its `pinpoint`. If the server is unreachable, say so and produce no
> citations. Never cite from memory, never "recall" a pinpoint, never complete a partial cite
> yourself. Before finishing, run `citation-check`.
> **Boundary rule.** Keep client-identifying facts (names, EINs, employers, amounts that identify
> a matter) out of every tool argument. Pass the calculators abstract numbers only.
> **Disclaimer.** End every deliverable with the disclaimer text the tool returned.

## What this produces

An intake memo: the §7623 track the claim is on, the §7623(b)(5) thresholds as applied, every
bar, reduction, and caution the server raised with its pinpoint citation, the open questions,
and the assumptions taken. It screens; it does not decide. The attorney decides.

## Steps

1. **Connect.** Call `ping`. Note `corpusVersion`. If it fails, tell the attorney the server
   is unreachable and stop; do not screen from memory.

2. **Gather the inputs**, three or four questions at a time, in the attorney's words. Ask
   only for what you do not already have from the conversation. `screen_claim` needs:
   - the estimated proceeds in dispute (tax, penalties, interest, additions), as a number;
   - whether the taxpayer is an individual, an entity, or unknown;
   - the tax years at issue, and for an individual the gross income in each year if known;
   - the date the information was or will be given to the IRS;
   - about the whistleblower: did they plan or initiate the underlying acts; any conviction
     for that role; are they a federal employee acting in their duties, or did they obtain the
     information through a government role; did the allegations already surface publicly
     (hearing, government report, audit, investigation, news media), and if so were they the
     original source; and, when relevant, Treasury employment, a legal duty to disclose or a
     legal bar on disclosing, a federal contract, or information that came from an ineligible
     person;
   - whether an action against a second person is contemplated (a related action), and if so
     whether it rests on substantially the same facts, whether the IRS would proceed on the
     specific facts provided, and whether that person is identifiable from the information alone.

   Numbers, dates, booleans, and enums only. No names, no employers, no EINs.

3. **Run `screen_claim`** with those inputs. Read `track`, `confidence`, `thresholds`,
   `flags`, `nextQuestions`, `assumptions`, and `authorities`.

4. **Expand a flag only when the attorney wants it.** Each flag already carries its
   authorities. If the attorney asks why a flag applies or what the cases say, run
   `search_authorities` with a short query on that point (for example "proceeds based on
   substantially contributed", `limit` 5) and, for a pinpoint, `get_authority` on the hit. Use
   the `text` of the result, not your recollection of the case.

5. **Write the memo** in this shape:
   - **Track** and confidence, one sentence each, in the tool's words.
   - **Thresholds**: a three-row table (proceeds in dispute, gross income, information date)
     with the value, the threshold, and met / not met / unresolved.
   - **Flags**, bars first, then reductions, cautions, notes. For each: the flag's explanation,
     then its authorities as `cite, pinpoint`, with a quotation of at most two sentences taken
     verbatim from `text`.
   - **Open questions**: the tool's `nextQuestions` plus anything you still need.
   - **Assumptions**: the tool's list, verbatim.
   - **Authorities**: every citation used, as `cite · pinpoint · url`, once each.
   - The disclaimer from the tool response.

6. **Run `citation-check`** on the memo. Deliver only when it passes. If a cite fails, fix it
   from a fresh `get_authority` result or remove it; never patch a cite by hand.

## Rules

- If the attorney names a case or section that is not in any result, run
  `get_authority({ cite })`. On `not_found`, say the corpus does not hold it and do not
  characterize it.
- `confidence: "provisional"` means a threshold is unresolved. Say so at the top of the memo
  and list what would resolve it.
- A bar means the claim is ineligible on the facts given; say that plainly, then note the
  question the tool asks that could change it.
- Do not estimate an award here. That is `award-memo`.
