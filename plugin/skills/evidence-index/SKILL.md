---
name: evidence-index
description: Build an exhibit index from the attorney's descriptions of the evidence and map each exhibit to the §7623 element it supports, with cited authorities and the gaps. Use when the user says "index these exhibits", "organize the evidence", or "what does this evidence prove".
---

# evidence-index

> **Citation rule.** Every statute, regulation, IRM, or case citation in your output must come
> from a `search_authorities` or `get_authority` result in this conversation, quoted from its
> `citation` object with its `pinpoint`. If the server is unreachable, say so and produce no
> citations. Never cite from memory, never "recall" a pinpoint, never complete a partial cite
> yourself. Before finishing, run `citation-check`.
> **Boundary rule.** Keep client-identifying facts (names, EINs, employers, amounts that identify
> a matter) out of every tool argument. Pass the calculators abstract numbers only.
> **Disclaimer.** End every deliverable with the disclaimer text the tool returned.

## What this produces

An exhibit index, an element map showing which exhibits carry which §7623 element, a list of
elements no exhibit supports, and the authorities that define each element. The exhibits are
described by the attorney in chat; nothing from them is sent to a tool.

## Steps

1. **Connect.** Call `ping`. Note `corpusVersion`. If it fails, say so and stop.

2. **Build the exhibit table** from the attorney's descriptions. One row per exhibit: number,
   short description, source or custodian, date or period, and what it tends to show. Ask for
   what is missing in batches of three or four. If the attorney has documents open in the
   conversation, read them to describe them; do not paste their contents into any tool.

3. **Fetch the elements.** Run `search_authorities` once per element, `limit` 5, and take the
   pinpoint from the top hit with `get_authority` when the snippet is not enough:
   - specific and credible information, and what a claim must contain;
   - the IRS proceeding based on the information, and "substantially contributed";
   - collected proceeds;
   - the §7623(b)(5) thresholds;
   - the positive and negative award factors;
   - the whistleblower's role (planned or initiated; original source of public allegations)
     when the facts raise it.
   Use `sources` to steer: `["statute","regulation"]` for the definitions, `["tax_court",
   "dc_circuit"]` for how they have been applied.

4. **Map exhibits to elements.** For each element: the exhibits that support it, one line
   each on how, and the element's authority as `cite, pinpoint` with a verbatim quotation of
   at most two sentences from `text`. An exhibit may serve several elements.

5. **List the gaps.** Every element with no supporting exhibit, and every exhibit that
   supports no element, with a sentence on what would fill the gap. This list is the point of
   the exercise; do not soften it.

6. **Run `citation-check`** on the index. Deliver only when it passes.

## Output shape

1. Exhibit index (table).
2. Element map (one block per element: authority, exhibits, how).
3. Gaps.
4. Authorities, `cite · pinpoint · url`, once each.
5. The disclaimer from the tool response.

## Rules

- Describe evidence; do not evaluate credibility or weight beyond what the attorney says.
- If the attorney asks which element an exhibit "really" proves, answer from the element's
  cited text, not from a general sense of whistleblower practice.
