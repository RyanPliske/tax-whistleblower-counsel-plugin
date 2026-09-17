---
name: citation-check
description: Verify every citation in a draft against the Tax Whistleblower Counsel server. Resolves each cite with get_authority, compares every quotation to the verbatim text, and blocks the deliverable on any not_found, ambiguous, or mismatched cite. Use at the end of every other skill, or when the user says "check these cites" or "verify the citations".
---

# citation-check

> **Citation rule.** Every statute, regulation, IRM, or case citation in your output must come
> from a `search_authorities` or `get_authority` result in this conversation, quoted from its
> `citation` object with its `pinpoint`. If the server is unreachable, say so and produce no
> citations. Never cite from memory, never "recall" a pinpoint, never complete a partial cite
> yourself. For a case, keep the corpus `id` beside the cite so `citation-check` can resolve
> it by id. Before finishing, run `citation-check`.
> **Boundary rule.** Keep client-identifying facts (names, EINs, employers, amounts that identify
> a matter) out of every tool argument. Pass the calculators abstract numbers only.
> **Disclaimer.** End every deliverable with the disclaimer text the tool returned.

## What this produces

A verification table for every citation in the draft under review, and a verdict: PASS, or
BLOCKED with the list of fixes. A blocked draft is not delivered.

## Steps

1. **Connect.** Call `ping`. If it fails, the draft cannot be checked; say so and report the
   draft as unverified. Do not pass it.

2. **Extract every citation** from the draft: statute sections (§7623, §7502, §7503 and their
   subdivisions), Treasury regulations (§301.7623-1 to -4 and subdivisions), IRM paragraphs,
   case names, reporter cites (T.C., T.C. Memo., F.4th), and corpus ids if the draft carries
   them. Number them in order of first appearance. Include cites inside quotations and
   footnotes.

3. **Resolve each one** with `get_authority`: by `id` when the draft has it, otherwise by
   `cite` exactly as written in the draft. Record `resolution`:
   - `exact`: proceed to step 4;
   - `ambiguous`: list the `candidates` for the attorney; do not choose one;
   - `not_found`: the cite is not in the corpus; do not search for a "close" one and
     substitute it.

4. **Check the pinpoint and every quotation.** For an exact hit, compare the pinpoint the
   draft uses with `citation.pinpoint`; a broader or different pinpoint is a mismatch. Then
   take every passage the draft attributes to that authority and confirm it appears verbatim
   in `citation.text`, allowing only whitespace and straight-versus-curly quote differences.
   Any other difference, including an ellipsis that removes words that change the meaning, is
   a mismatch.

5. **Check characterisations.** Where the draft says an authority "holds", "requires", or
   "provides" something without quoting it, read `text` and mark the line `supported` or
   `unverified paraphrase`. An unverified paraphrase is a warning, not a block, but list it.

6. **Report** a table: `#` · cite as written · resolution · pinpoint check · quotation check ·
   action. Then the verdict:
   - **PASS**: every cite `exact`, every pinpoint and quotation matched.
   - **BLOCKED**: any `not_found`, `ambiguous`, pinpoint mismatch, or quotation mismatch.
     List each fix: replace the quotation with the verbatim text, correct the pinpoint to the
     one returned, pick among candidates, or remove the cite.

7. Confirm the draft ends with the disclaimer the tool returned and states the
   `corpusVersion` it was checked against. Add them if missing.

## Rules

- Never repair a cite by guessing. Every replacement text comes from a `get_authority` result
  in this conversation.
- If the attorney says a `not_found` authority is real, report that the corpus does not hold
  it. It may stay in the draft only if he says so, labelled "outside the corpus; attorney
  verified", and the verdict notes it.
- Pending legislation (`status: "pending, not law"`) may be cited only as pending, never as law.
- A PASS says the citations match the corpus as of `corpusVersion`. It does not say the law
  is current; the disclaimer covers that.
