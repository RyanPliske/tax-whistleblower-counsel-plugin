---
name: claim-intake
description: Screen a prospective IRS whistleblower claim under IRC §7623: gather the screen_claim inputs, run it, expand the flags with cited authorities, and produce an intake memo. Use when the user says 'new whistleblower matter', 'screen this claim', or 'does this qualify'.
---

# claim-intake

**Status:** stub — written at M4 (see whistleblower-mcp/docs/SPEC.md §5).

> **Citation rule.** Every statute, regulation, IRM, or case citation in your output must come
> from a `search_authorities` or `get_authority` result in this conversation, quoted from its
> `citation` object with its `pinpoint`. If the server is unreachable, say so and produce no
> citations. Never cite from memory, never "recall" a pinpoint, never complete a partial cite
> yourself. Before finishing, run `citation-check`.
> **Boundary rule.** Keep client-identifying facts (names, EINs, employers, amounts that identify
> a matter) out of every tool argument. Pass the calculators abstract numbers only.
> **Disclaimer.** End every deliverable with the disclaimer text the tool returned.

## Steps

1. Ask 3–4 questions for the screen_claim inputs you don't have.  2. screen_claim.  3. search_authorities per flag the attorney wants expanded.  4. citation-check.
