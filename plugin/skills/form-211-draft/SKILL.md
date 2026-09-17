---
name: form-211-draft
description: Draft a Form 211 narrative section by section from the attorney's facts, with the submission checklist and the penalty-of-perjury declaration cited. Use when the user says 'draft the 211' or 'Form 211 narrative'.
---

# form-211-draft

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

1. form_211_outline.  2. Draft each section from the attorney's facts, in chat, never in tool calls.  3. get_authority for the declaration and submission instructions.  4. citation-check.
