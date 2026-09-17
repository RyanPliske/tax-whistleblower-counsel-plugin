---
name: citation-check
description: Verify every citation in a draft against the server: resolve each cite with get_authority, compare quoted text, and block the deliverable on any not_found or mismatch. Use at the end of every other skill or when the user says 'check these cites'.
---

# citation-check

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

1. Extract every cite in the draft.  2. get_authority({ cite }) for each.  3. Compare quoted text to the citation's text.  4. Report exact / ambiguous / not_found / quote mismatch. A not_found or mismatch blocks the deliverable.
