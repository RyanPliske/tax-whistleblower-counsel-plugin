---
name: form-211-draft
description: Draft a Form 211 narrative section by section from the attorney's facts, with the submission checklist and the penalty-of-perjury declaration cited to the regulation. Use when the user says "draft the 211", "Form 211 narrative", or "what goes in the claim".
---

# form-211-draft

> **Citation rule.** Every statute, regulation, IRM, or case citation in your output must come
> from a `search_authorities` or `get_authority` result in this conversation, quoted from its
> `citation` object with its `pinpoint`. If the server is unreachable, say so and produce no
> citations. Never cite from memory, never "recall" a pinpoint, never complete a partial cite
> yourself. Before finishing, run `citation-check`.
> **Boundary rule.** Keep client-identifying facts (names, EINs, employers, amounts that identify
> a matter) out of every tool argument. Pass the calculators abstract numbers only.
> **Disclaimer.** End every deliverable with the disclaimer text the tool returned.

## What this produces

A section-by-section draft of the Form 211 submission, written from the attorney's facts in
chat, followed by a submission checklist. The legal requirements for each section come from
`form_211_outline`; the facts come from the attorney and go into the draft, never into a tool.

## Steps

1. **Connect.** Call `ping`. Note `corpusVersion`. If it fails, say so and stop.

2. **Get the outline.** Call `form_211_outline` with the `track` if known (from `claim-intake`
   or the attorney) and `format: "outline"`. Read `sections` (each with `field`, `required`,
   `guidance`, `authorities`), `declaration`, `submission`, and `authorities`.

3. **Draft each section in order**, asking the attorney for the facts a section needs before
   writing it. Ask in plain terms and in small batches. The narrative section is the heart of
   the claim; organise it under the items the outline lists (what happened, who, how, when and
   where, the amounts, how the whistleblower knows, the documents that support it, the
   whistleblower's relationship to the taxpayer). Write in the whistleblower's first person
   unless the attorney says otherwise. Where a fact is missing, leave `[ATTORNEY TO CONFIRM: …]`
   rather than inventing it.

4. **Cite the requirement, not the fact.** Under each section heading, one line: the
   requirement it satisfies, as `cite, pinpoint` from the section's `authorities`, with a short
   verbatim quotation from `text` where the wording matters (what "specific and credible"
   requires, what must be signed). Never cite a source for a fact about the taxpayer.

5. **Declaration and signature.** Reproduce the `declaration` text exactly as the tool returned
   it and cite its authority. State, cited, that the whistleblower signs it personally and that
   a representative's signature does not satisfy it.

6. **Submission checklist.** From `submission`: the digital route and the mailing route with
   the address exactly as the tool gives it, what attaches (the documents the narrative
   relies on, a Form 2848 if counsel will be recognised, any supplement), and the items marked
   required that the draft has not yet covered. Links the tool labels as outside the corpus
   stay labelled that way.

7. **Run `citation-check`** on the whole draft. Deliver only when it passes.

## Rules

- Nothing about the taxpayer, the whistleblower's identity, or the amounts goes into a tool
  argument. The only tool arguments here are `track` and `format`.
- Do not "improve" the declaration wording. If the attorney wants different wording, say the
  regulation's text is what the tool returned and let him decide.
- The draft is a document for the attorney's review. Mark it DRAFT at the top and end it with
  the disclaimer the tool returned.
