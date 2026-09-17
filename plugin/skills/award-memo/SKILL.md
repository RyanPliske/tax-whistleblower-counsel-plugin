---
name: award-memo
description: Estimate the §7623 award band across collection scenarios with the award factors applied and every rule cited. Use when the user says "what's the award range", "estimate the award", or "run the award numbers".
---

# award-memo

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

A memo with the award band (low, mid, high) per collection scenario under the framework the
regulation applies, the factors that move it and the pinpoint for each, the reductions,
withholding and sequestration notes, the assumptions, and the authorities. The regulation says
the analysis cannot be reduced to a mathematical equation; the band is the framework's steps,
not a prediction, and the memo says so.

## Steps

1. **Connect.** Call `ping`. Note `corpusVersion`. If it fails, say so and stop.

2. **Track first.** If `claim-intake` has not run in this conversation, run it (or at least
   `screen_claim`) so the track and any bar or reduction are established. Use the track the
   tool returned.

3. **Gather the award inputs**, in the attorney's words, abstract numbers only:
   - collected proceeds as stated, and up to a few alternative scenarios with labels (for
     example "IRS asserted", "likely settlement", "low case");
   - which positive factors apply, chosen from the eight in Reg. §301.7623-4(b)(1), by slug:
     `prompt`, `issue_previously_unknown`, `hard_to_detect`, `thorough_presentation`,
     `exceptional_cooperation`, `identified_assets`, `identified_connections`,
     `changed_taxpayer_behavior`; and which negative factors from (b)(2): `delay`,
     `contributed_to_noncompliance`, `profited`, `harmed_pursuit`, `violated_instructions`,
     `violated_confidentiality`, `violated_6103n_contract`, `false_or_misleading`;
   - whether the action rests on public-source allegations and, if so, whether the
     whistleblower was the original source;
   - whether the whistleblower planned or initiated the acts and, if so, the role (primary,
     significant, moderate) and any conviction;
   - the attorney fee percentage, if the memo should show net figures.

4. **Run `estimate_award`.** Read `framework`, each scenario's `band`, `amounts`, `pctBasis`,
   `reductions`, `net`, `withholding`, then `sequestration`, `assumptions`, `authorities`.

5. **Pin each factor.** For every factor the attorney selected, run `get_authority` on its
   regulation subparagraph (the tool's `authorities` give the framework units; the factor
   lists are Reg. §301.7623-4(b)(1) and (b)(2), one romanette per factor) and quote the
   factor's text. Under each, one or two sentences in the attorney's words on the facts that
   support it. Facts stay in the memo, not in tool arguments.

6. **Write the memo**:
   - **Framework** applied and why, from the tool.
   - **Scenarios table**: scenario, collected proceeds, band %, low/mid/high amounts, after
     fee if given, withholding on the mid figure.
   - **Factors**: positives then negatives, each with its pinpoint, quotation, and facts.
   - **Reductions** (planner and initiator), if any, with the range and what was applied.
   - **Caveats**: `pctBasis` verbatim; the sequestration note (the rate is not in the corpus
     and the attorney should check the current fiscal year's rate); the withholding note.
   - **Assumptions**: the tool's list, verbatim.
   - **Authorities**, `cite · pinpoint · url`, once each.
   - The disclaimer from the tool response.

7. **Run `citation-check`.** Deliver only when it passes.

## Rules

- Never present the mid figure as "the likely award". The band is the framework, not a
  forecast; say that in the memo's first paragraph.
- If a factor the attorney wants is not one of the sixteen slugs, say the regulation does not
  list it and do not pass it.
- A `denied` framework or a bar from `screen_claim` ends the memo with that finding and its
  authorities; do not compute around it.
