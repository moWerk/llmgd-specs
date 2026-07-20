<p align="center"><img src="assets/llmgd-stacked.svg" width="140" alt="LLMGD badge"></p>

# LLMGD — Large Language Model Governance Disclosure

A Creative-Commons-style label that says, honestly and verifiably, how much
LLM involvement a piece of work contains.

<p><img src="assets/llmgd-level-0.svg" width="44" alt="LLMGD-0">
<img src="assets/llmgd-level-1.svg" width="44" alt="LLMGD-1">
<img src="assets/llmgd-level-2.svg" width="44" alt="LLMGD-2">
<img src="assets/llmgd-level-3.svg" width="44" alt="LLMGD-3">
<img src="assets/llmgd-level-4.svg" width="44" alt="LLMGD-4">
<img src="assets/llmgd-level-5.svg" width="44" alt="LLMGD-5"></p>

The mark is the standard: the L-shaped axis is the Origin and Assurance
grid, and the dot plots where the work sits on it. Level badges walk the
dot up the diagonal.

## Why

Somewhere between "fully hand-written" and "the machine did everything" lives
almost all modern work, and we have no vocabulary for the in-between. A
co-author trailer is a blunt instrument: it confuses blame with involvement.
A checkbox that says "AI-assisted" says nothing at all.

LLMGD gives the in-between a name. Two graded axes, a handful of evidence
flags, and one line in your commit or PR that anyone can read and any LLM can
verify against the session transcript that produced the work.

The idea sparked while shipping a watch app PR where every design decision
and every sentence was mine, but the typing was not. Not disclosing that felt
wrong. Disclosing it as co-authorship felt wrong too. So: a standard.

## The short version (v0.2)

Two axes, because a reader asks two questions.

- **Origin (O0–O4)** — *who authored it.* O0 machine-decided from bare
  prompts, O2 human-designed and machine-expressed, O4 untouched by any model.
  Composite works report a **distribution**, not just the weakest part.
- **Assurance (A0–A5)** — *how well a human governed it.* Built from evidence
  flags where the **costly** ones lead: **U**nderstood (demonstrated by
  correcting/steering) and **T**ested (empirically) are load-bearing;
  **R**ead is a minor breadth signal, not a gate; **X** is external review.
  A3 = understood *and* tested — the responsible heavy-LLM-use state, and a
  respectable grade.
- **The number** — `LLMGD-N` equals the assurance tier, and travels with the
  origin tag: `LLMGD-3 (origin O1)`. The number is trust; origin is
  provenance.
- **Machine-gradeable**: a standardized prompt ([GRADING_PROMPT.md](GRADING_PROMPT.md))
  runs over the interaction transcript(s). Claims without evidence grade down.
  Self-flattery loses to the rubric.

Full specification, the two-axis model, and why v0.2 inverted the v0.1
Read-gate: [SPEC.md](SPEC.md).

## Using it

Add the two lines to your commit, PR or document footer:

```
Disclosure: LLMGD-3 · origin O1 (machine-authored, human-understood and device-tested)
LLMGD: v0.2; assurance=A3; flags=U,T; origin={O0:.4,O1:.3,O2:.3}; origin_headline=O1; scope=code+messages; graded-by=<model>; retrieval=author-side
```

Then, ideally, publish the graded verdict or keep the transcript retrievable
for third-party grading. That is the whole standard.

### Embedding the badges

For a nicer label, embed the badge assets instead of plain text. The
reusable two-line pattern: line one carries only the inline lockup
linking back to this spec, plus the human phrase, so the one real link
cannot be confused with badges. Line two leads with the level badge and
the awarded flag chips, then the machine-readable string. The wordmark uses a picture element so both
GitHub themes render it correctly (the `-white` asset variants carry
white text for dark backgrounds):

```html
<p>
<a href="https://github.com/moWerk/llmgd-specs"><picture><source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/moWerk/llmgd-specs/main/assets/llmgd-inline-white.svg"><img src="https://raw.githubusercontent.com/moWerk/llmgd-specs/main/assets/llmgd-inline.svg" height="26" alt="LLMGD"></picture></a>&ensp;origin O1 · machine-authored, human-understood and device-tested<br>
<img src="https://raw.githubusercontent.com/moWerk/llmgd-specs/main/assets/llmgd-level-3.svg" height="20" alt="LLMGD-3"> <img src="https://raw.githubusercontent.com/moWerk/llmgd-specs/main/assets/llmgd-flag-U.svg" height="20" alt="U"> <img src="https://raw.githubusercontent.com/moWerk/llmgd-specs/main/assets/llmgd-flag-T.svg" height="20" alt="T">&ensp;<sub><code>LLMGD: v0.2; assurance=A3; flags=U,T; origin_headline=O1; scope=code+messages+docs; graded-by=&lt;model&gt;; retrieval=author-side</code></sub>
</p>
```

Swap the level badge and flag chips for your verdict's values; the
machine-readable line always carries the exact coordinates.

Rendered, this repository's own label:

<p>
<a href="https://github.com/moWerk/llmgd-specs"><picture><source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/moWerk/llmgd-specs/main/assets/llmgd-inline-white.svg"><img src="https://raw.githubusercontent.com/moWerk/llmgd-specs/main/assets/llmgd-inline.svg" height="26" alt="LLMGD"></picture></a>&ensp;origin O1 · human concept and rulings, LLM-designed detail and text; understood, read<br>
<img src="https://raw.githubusercontent.com/moWerk/llmgd-specs/main/assets/llmgd-level-2.svg" height="20" alt="LLMGD-2"> <img src="https://raw.githubusercontent.com/moWerk/llmgd-specs/main/assets/llmgd-flag-U.svg" height="20" alt="U"> <img src="https://raw.githubusercontent.com/moWerk/llmgd-specs/main/assets/llmgd-flag-R.svg" height="20" alt="R">&ensp;<sub><code>LLMGD: v0.2; assurance=A2; flags=U,R; origin_headline=O1; scope=docs+assets; graded-by=claude-fable-5; retrieval=author-side</code></sub>
</p>

## This repository's own label

Dogfood starts at home (A2 = understood, read; not empirically tested — it's a
spec):

```
Disclosure: LLMGD-2 · origin O1 (human concept and rulings, LLM-designed detail and text; understood, read)
LLMGD: v0.2; assurance=A2; flags=U,R; origin_headline=O1; scope=docs; graded-by=claude-fable-5; retrieval=author-side
```

## Assets

The badge family in [assets/](assets/) (mark, level badges 0-5, flag chips
R/U/T/X, inline and stacked lockups, black/white variants) is CC0. The
assets carry their own self-graded label in
[assets/DECLARATION.md](assets/DECLARATION.md), the first field verdict
ever produced by the grading prompt. It was run before the standard was
even published, it grades against the author's interest where the evidence
says so, and it flags its own conflict of interest. Read it to see what an
honest verdict looks like.

## Licensing

Specification and documentation: CC BY-SA 4.0.
The grading prompt and all assets: CC0, so they can be embedded anywhere
without friction.

## Status

v0.2 — the first field-grading (a heavily machine-authored but understood-and-
tested project) exposed v0.1's Read-gate as backwards; v0.2 makes the costly
signals lead and splits authorship from oversight. v0.1 labels already in the
wild remain valid as v0.1. Published following the lighthouse principle: use
it, shine, and see who navigates by it. If I am the only person doing this,
so be it.
