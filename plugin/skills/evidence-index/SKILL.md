---
name: evidence-index
description: Build an exhibit index from the attorney's descriptions of the evidence and map each exhibit to the §7623 element it supports, with cited authorities and gaps. Use when the user says 'index these exhibits' or 'organize the evidence'.
---

# evidence-index

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

1. Build the exhibit table from descriptions (never uploads).  2. search_authorities for 'specific and credible information' and 'substantially contributed'.  3. Map each exhibit to an element.  4. citation-check.
