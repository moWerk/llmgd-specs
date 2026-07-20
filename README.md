<p align="center"><picture><source media="(prefers-color-scheme: dark)" srcset="assets/llmgd-signal-lockup-3-dark.svg"><img src="assets/llmgd-signal-lockup-3.svg" height="44" alt="LLMGD-3 badge"></picture></p>

# LLMGD — Large Language Model Governance Disclosure

A Creative-Commons-style label that says, honestly and verifiably, how much
LLM involvement a piece of work contains.

**Origin** — the authorship split, from all-machine to all-human:

<p>
<img src="assets/llmgd-signal-o0.svg" height="40" alt="O0 all machine">
<img src="assets/llmgd-signal-o1.svg" height="40" alt="O1 mostly machine">
<img src="assets/llmgd-signal-o2.svg" height="40" alt="O2 half and half">
<img src="assets/llmgd-signal-o3.svg" height="40" alt="O3 mostly human">
<img src="assets/llmgd-signal-o4.svg" height="40" alt="O4 all human">
</p>

The mark reads itself: a smooth **analog wave** is the human side, a stepped
**digital wave** is the machine, and the split sits at the authorship
proportion. The **LLMGD-N** number is the second axis — the assurance tier,
how well a human governed the work.

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
reusable two-line pattern: line one carries the **assurance-tier lockup**
(linking to this spec) and the **origin mark** for your split, plus the
human phrase; line two is the machine-readable string. The lockup uses a
picture element so both GitHub themes render it (the `-dark` variants carry
light text for dark backgrounds; the origin marks are colour-only and
theme-safe):

```html
<p>
<a href="https://github.com/moWerk/llmgd-specs"><picture><source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/moWerk/llmgd-specs/main/assets/llmgd-signal-lockup-3-dark.svg"><img src="https://raw.githubusercontent.com/moWerk/llmgd-specs/main/assets/llmgd-signal-lockup-3.svg" height="30" alt="LLMGD-3"></picture></a>&ensp;<img src="https://raw.githubusercontent.com/moWerk/llmgd-specs/main/assets/llmgd-signal-o1.svg" height="26" alt="origin O1">&ensp;origin O1 · machine-authored, human-understood and device-tested<br>
<sub><code>LLMGD: v0.2; assurance=A3; flags=U,T; origin_headline=O1; scope=code+messages+docs; graded-by=&lt;model&gt;; retrieval=author-side</code></sub>
</p>
```

Swap the lockup number (`-0`…`-5`, your assurance tier) and the origin
mark (`-o0`…`-o4`, your authorship split) for your verdict's values; the
machine-readable line always carries the exact coordinates.

Rendered, this repository's own label:

<p>
<a href="https://github.com/moWerk/llmgd-specs"><picture><source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/moWerk/llmgd-specs/main/assets/llmgd-signal-lockup-2-dark.svg"><img src="https://raw.githubusercontent.com/moWerk/llmgd-specs/main/assets/llmgd-signal-lockup-2.svg" height="30" alt="LLMGD-2"></picture></a>&ensp;<img src="https://raw.githubusercontent.com/moWerk/llmgd-specs/main/assets/llmgd-signal-o1.svg" height="26" alt="origin O1">&ensp;origin O1 · human concept and rulings, LLM-designed detail and text; understood, read<br>
<sub><code>LLMGD: v0.2; assurance=A2; flags=U,R; origin_headline=O1; scope=docs+assets; graded-by=claude-fable-5; retrieval=author-side</code></sub>
</p>

## This repository's own label

Dogfood starts at home (A2 = understood, read; not empirically tested — it's a
spec):

```
Disclosure: LLMGD-2 · origin O1 (human concept and rulings, LLM-designed detail and text; understood, read)
LLMGD: v0.2; assurance=A2; flags=U,R; origin_headline=O1; scope=docs; graded-by=claude-fable-5; retrieval=author-side
```

## Assets

The **signal** badge family in [assets/](assets/) is CC0: origin marks
`signal-o0…o4` (the analog↔digital authorship split), assurance-tier
lockups `signal-lockup-0…5`, and the wordmark, each with dark and pure
black/white variants. The analog wave is the human hand, the square wave
the machine; the split shows the proportion.

For a worked verdict — the case that shaped v0.2 — see [SPEC.md §10](SPEC.md).
[assets/DECLARATION.md](assets/DECLARATION.md) is kept as history: the
first field verdict the grading prompt ever produced (a v0.1 self-grade of
the original badge round), notable because it grades against the author's
interest and flags its own conflict of interest.

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
