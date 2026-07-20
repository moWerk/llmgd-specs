# LLMGD Specification v0.2

Large Language Model Governance Disclosure. A standardized, machine-gradeable
declaration of LLM involvement in published work.

The key words MUST, MUST NOT, SHOULD, SHOULD NOT and MAY are to be
interpreted as described in RFC 2119.

> **v0.2 is the current version.** It supersedes v0.1 but does not invalidate
> it: a label that cites `v0.1` remains a valid v0.1 label. See §11 for what
> changed and why. In short — v0.1 gated every grade on *Read*, the cheapest
> assurance signal, which floored the responsible pattern of heavy, well-
> understood, well-tested LLM use. v0.2 makes the costly signals (Understood,
> Tested) load-bearing and splits authorship from oversight into two
> coordinates.

## 1. Design principles

1. **Consumer-driven.** The label answers the questions its readers have.
   Reviewers and future maintainers ask two *different* questions: "how much
   of this did a machine author?" (provenance) and "how well did a human
   govern it?" (trust). v0.2 answers both, separately.
2. **Decisions, not keystrokes.** Authorship is graded on who made the
   decisions the bytes express, not who typed them.
3. **Costly signals lead.** A human *demonstrating* understanding (catching an
   error, reversing a design) or *testing* against reality is strong evidence.
   A human *saying* "I read it" is cheap. The grade must weight the costly
   signals above the cheap ones — including in how the tiers are built.
4. **Evidence or it did not happen.** Every level is defined by observable
   events in the interaction transcript(s). Unprovable claims grade DOWN.
5. **Composable, with a summary.** Precise coordinates underneath; one
   memorable number on top.

## 2. Axis one — ORIGIN (authorship): O0–O4

Origin grades the causal source of the work's decisions: design, structure,
behaviour, wording.

| Level | Definition |
|---|---|
| **O0** | Machine decided and expressed. Human supplied goals or bare prompts only. |
| **O1** | Human set constraints, specifications or corrective direction. Machine designed the details and expressed them. |
| **O2** | Human designed. Machine purely expressed (typing, formatting, translation into artifact form). |
| **O3** | Human designed and expressed. Machine edited, restructured, polished or rescoped existing human material. |
| **O4** | No LLM involvement. |

**Composite works MUST report a distribution, not just the weakest part.**
A real unit (a PR, a release) spans levels: some commits O2, some O0. Report
the mass fraction at each level plus a headline. The headline SHOULD be the
mass-weighted characterization, not the floor — collapsing a work that is half
O2 to a bare "O0" discards the signal that half of it was human-designed.

```
origin: {O0: 0.30, O1: 0.20, O2: 0.50}; origin_headline: O1
```

Atomic works may report a single level: `origin: O2`.

**O0-vs-O1 boundary test.** This boundary now carries the judgment weight, so
apply a fixed test: does the human utterance *name the design* (→ O1, or O2 if
it also specifies the expression) or *feed a decision the machine then makes*
(→ O0)? "Shift it to the service", a dictated command set, "commit scoped by
theme" name the design. "It crashes", "the VAI doesn't cut power", "implement
what you think is safe" feed a fact, goal or correction the machine designs
around. When O0 and O1 mass land near-even, the grader MUST report the
sensitivity and which way a stricter reading tips the headline, not present a
coin-flip as settled.

## 3. Axis two — ASSURANCE (oversight): A0–A5

Assurance grades how strongly a human governed the work before publication.
This is the trust axis and the primary driver of the LLMGD number (§5).

**The underlying evidence types (flags):**

| Flag | Name | Weight | Evidence required |
|---|---|---|---|
| **U** | Understood | **costly, load-bearing** | Demonstrated comprehension: the human corrected errors, reversed designs, caught inconsistencies, ran experiments that required understanding. NOT "I understand this" (cheap talk). |
| **T** | Tested | **costly, load-bearing** | Empirically exercised: builds, device runs, CI, reproductions, planted-bug validation, with results fed back. Mark `observed` (results in transcript) or `attested` (human-reported). |
| **R** | Read | breadth, minor | The complete artifact (or full diff) was seen before publication. Coverage, not depth: proves breadth ("saw all of it"), not comprehension. |
| **X** | eXternally reviewed | external | An independent human reviewed it. Normally accrues AFTER publication; a shipped label MUST NOT self-claim X. |

**Key change from v0.1:** U and T are load-bearing; R is a minor breadth
signal and is NOT a prerequisite for the higher tiers. Demonstrated
understanding plus testing is stronger than any review ceremony, and must be
gradeable on its own. R and U are orthogonal: R is breadth ("saw all of it"),
U is depth ("understood what mattered"). A work can have either, both, or
neither.

**The assurance tiers (A0–A5), derived from the flags:**

| Tier | Meaning | Flag condition |
|---|---|---|
| **A0** | Nothing evidenced. Published as emitted. | none |
| **A1** | Seen, not shown understood or tested. Breadth only. | R alone |
| **A2** | One costly signal. Understood **or** tested. | U or T |
| **A3** | Both costly signals. Understood **and** tested. | U and T |
| **A4** | Understood, tested, and fully reviewed for breadth. | U and T and R |
| **A5** | Understood, tested, and independently reviewed. | U and T and X |

A3 — heavily machine-authored but genuinely understood and tested — is the
responsible heavy-LLM-use state, and it is a *respectable* grade. v0.1 could
not represent it; v0.2 makes it central.

**R for machine-authored code.** When origin is low, a human rarely governs by
reading the whole diff — reading 40k machine-written lines is not how a
maintainer of such work exercises control; U and T are. So `R: NO` on
high-machine-origin work is the *expected, honest* state, not a deficiency, and
A3 is its natural ceiling short of external review. A4 asks for a breadth-review
that this way of working simply does not use; that is a description, not a
demerit.

## 4. Evidence taxonomy

Graders MUST weight evidence by class:

- **Costly signals (strong).** Hard to fake: catching a real error, reversing
  a design with reasons, running an experiment, halting the machine mid-action,
  a correction applied consistently. The basis for U, and for trusting T.
- **Cheap talk (weak).** "I fully understand this" / "looks good" carry near-
  zero weight alone.
- **Observed vs attested.** Machine-visible events (a test log in the
  transcript) are `observed`. Human reports of off-transcript events ("it runs
  green on my watch") are `attested`. Both count; the verdict MUST state which.

## 5. The grade: a coordinate pair, plus a summary number

The rigorous grade is the **pair `O_x·A_y`** — provenance and oversight
together. Example: `O1·A3` = "mostly machine-authored, understood and tested,
not externally reviewed."

The memorable **LLMGD-N number equals the assurance tier N** (A0→LLMGD-0 …
A5→LLMGD-5). The number is the *governance* level — the "how well did a human
keep this above the slop-line" reading — which is what a consumer deciding
whether to trust the work actually needs. Origin travels *with* the number as
a required companion, never folded into it.

`LLMGD-3 (origin O1)` and `LLMGD-3 (origin O4)` are both A3-assured, but one
was machine-drafted and one hand-written; the origin tag keeps that honest.

Presets from v0.1 (LLMGD-0..5 as bespoke flag combinations) are retired; the
number is now simply the assurance tier.

## 6. Scope

The declaration unit SHOULD be the publishing unit (commit, PR, document).
Report the origin distribution across it (§2) and the assurance tier for it.
Assurance is graded on the unit as a whole; if sub-parts differ materially, an
OPTIONAL per-part breakdown MAY be given. A PR's assurance is the assurance of
its weakest materially-relevant part (you cannot claim the whole is tested if
half of it never was), while its origin is a distribution.

## 7. Label format

```
Disclosure: LLMGD-<N> · origin <headline> (<plain phrase>)
LLMGD: v0.2; assurance=A<0-5>; flags=<U,T,R,X|none>; origin=<dist-or-level>; origin_headline=O<0-4>; scope=<parts>; graded-by=<grader>; retrieval=<mode>
```

Example (the a-d-b worked case, §10):

```
Disclosure: LLMGD-3 · origin O1 (machine-authored, human-understood and device-tested; not yet externally reviewed)
LLMGD: v0.2; assurance=A3; flags=U,T; origin={O0:.4,O1:.3,O2:.3}; origin_headline=O1; scope=code+docs; graded-by=claude-opus-4-8; retrieval=author-side
```

`retrieval` = `author-side` | `third-party` | `self-claimed` (weakest).

## 8. Grading protocol

1. **Retrieve before grading.** Find every transcript that produced the work,
   *including broken/continued sessions*. They persist as separate files under
   `~/.claude/projects/<slug>/`. The join key `aiTitle` survives a crash-and-
   continue session-id change — but only join within one project directory: the
   same title can recur under a different cwd-slug for an unrelated project, so
   a cross-directory aiTitle match is a candidate to confirm by content, not an
   automatic include. A continued session backlinks the prior session-id in its
   handover; a size-outlier file (e.g. 42 MB beside 3 MB ones) is a crash
   casualty that usually holds the bulk of the work. Oversized
   transcripts are stream-filtered, never read whole (GRADING_PROMPT.md gives
   the mechanics). Produce a **retrieval log**: searched / found / skipped-and-
   why.
2. **Absence is gated on retrieval.** A flag scores NO (DOWN) only when its
   evidence is genuinely **unavailable** after a documented search. Un-searched
   or not-retrieved-this-run territory yields **`unassessed`**, not NO, and
   makes the verdict provisional. Scoring missing-because-unretrieved as absent
   is bias against the author, not conservatism — it was the worst failure of
   both v0.1 and the first field grading, and it landed hardest on R, the
   former gate.
3. **Coverage has three states**, not two: `full` (all transcripts retrieved),
   `unretrieved` (evidence exists, this run missed it — fixable, re-run), and
   `unavailable` (truly gone). Only `unavailable` licenses a DOWN.
4. Run the rubric: origin distribution (§2), assurance tier from the costly-
   signal-led flags (§3), each answer cited or marked `unassessed`.
5. The verdict includes: retrieval log, origin distribution + headline,
   assurance tier with flags and evidence, coverage state, integrity caveats,
   grader identity, retrieval mode.
6. The FIRST verdict SHOULD be the published one; re-running for a better score
   ("verdict shopping") defeats the standard. Include a run declaration.

## 9. Integrity considerations

Screen both directions:

- **Author inflation.** Self-serving or curated transcripts, cheap-talk
  claims, a model-authored summary standing in for raw turns. Mitigations:
  costly-signal weighting, coverage reporting, grade-down defaults. A summary
  written by the graded party is not raw evidence.
- **Hostile deflation.** A third party grading with a doctored or truncated
  transcript. Mitigation: report coverage, continuity, truncation and
  discontinuity anomalies; mark incomplete-transcript verdicts `coverage-
  limited`, naming what is missing.

- **Selective retrieval — the author-side hull leak.** A grader with
  filesystem access does not merely fail to retrieve; it *chooses* what to
  retrieve, and selective retrieval is invisible in a verdict that reports only
  what it looked at. The mandatory retrieval log (§8) is what makes it
  auditable. Without it, author-side and third-party runs are not comparable
  even at identical rigour, because only one of them could have quietly looked
  away. The real self-grading danger is not generosity — it is grading a
  *convenient subset* and reporting it with full confidence. The retrieval log
  makes *what was searched* auditable but not *what the grader chose to search
  for*; a narrower lexicon yields a cleaner-looking log and a different result.
  So the search lexicons are FIXED in GRADING_PROMPT.md, not left to grader
  discretion — a grader MUST run the standard set. Discretion in what to look
  for is the last place selective retrieval can hide.

**Self-grading** (author-side, same model that produced the work) carries a
structural conflict of interest in both directions and MUST be flagged; a
third-party rerun is recommended before relying on it. Transcript custody: a
verdict is only as trustworthy as transcript custody; authors SHOULD archive
session logs immutably at publication.

## 10. Worked example — the real field grading that shaped v0.2

A long, heavily machine-authored software project (a fleet manager, releases
0.1–0.8), graded author-side by the producing model.

- **v0.1 verdict:** `O0`, flags `U,T`, **preset: none** — floored. And the
  grader had searched only 3.4 MB of ~51 MB of transcript, scoring the
  unretrieved 93% as absence; its `R: NO` was assumed, not searched.
- **v0.2 re-grade, retrieval-first:** the `aiTitle` join surfaced a 43.9 MB
  OOM-crashed session (18,047 records, 3,723 user turns) that v0.1 never read —
  4,363 user turns in scope against ~340 before. Verdict: **`LLMGD-3 · origin
  O1`**. Assurance **A3** — U (independent crash reproduction *before* asserting
  a claim; a power-budget refuted by a day-long hardware observation; the
  author correcting his own instrument reading) and T (a 226-test suite with
  planted-bug validation, including a self-caught decoration failure). R is now
  a *searched* NO: across 4,363 turns only 7 touch code at all, and the 44
  `git diff` runs were the assistant's own grep-filtered verification, never a
  diff shown to the human — the expected, honest state for machine-authored
  code, and A3 is its natural ceiling. Origin is a near-even distribution
  `{O0:.45, O1:.45, O2:.10}`, headline O1, with the boundary sensitivity
  reported rather than hidden.

The revision was authored *by* this grading: the axis split, the U/T inversion,
and the whole retrieval-first model came out of the run's own diagnosis of why
its first verdict was wrong.

## 11. Versioning and changelog

Semantic versioning; labels MUST cite the version they were graded under.

**v0.2 (current).** (a) Assurance inverted: U and T are load-bearing, R is a
minor breadth signal, no longer a gate. (b) Grade split into two coordinates,
Origin (authorship) · Assurance (oversight); the LLMGD-N number now tracks the
assurance tier, with origin as a required companion tag. (c) Origin reported as
a distribution for composite works, not collapsed to the weakest part. (d)
**Retrieval-first grading** — the deeper fix, surfaced by the first field
grading (an author-side self-grade whose grader scored 93% of the project as
"absence" while the raw turns sat on disk): graders MUST retrieve all project
transcripts including crashed/continued sessions before scoring; absence is
gated on documented retrieval (`unassessed`, not NO, for un-searched
territory); coverage splits into full / unretrieved / unavailable; and a
retrieval log makes selective retrieval auditable. Both the axis redesign and
this retrieval model were contributed by the field-grading process itself —
the standard's own subjects improving the standard.

**v0.1 labels remain valid as v0.1.** They cite their version and their
grammar is unchanged; do not retro-grade them. New grading uses v0.2.

Changes happen by public PR to this repository.
