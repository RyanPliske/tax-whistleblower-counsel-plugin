---
name: award-memo
description: Estimate the §7623 award range across collection scenarios with the factors applied and every rule cited. Use when the user says 'what's the award range' or 'estimate the award'.
---

# award-memo

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

1. screen_claim if not already done.  2. estimate_award with the attorney's scenarios.  3. get_authority on each factor applied.  4. citation-check.
