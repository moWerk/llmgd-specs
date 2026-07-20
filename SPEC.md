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

1. Inputs: **all** interaction transcript(s) that produced the work, plus the
   published artifact. Prior sessions of the same project persist as separate
   transcript files; the grader MUST seek and ingest them, not grade only the
   current session — grading a resumed fragment while sibling transcripts exist
   understates coverage and MUST be reported as coverage-limited.
2. Run the standardized rubric (GRADING_PROMPT.md): binary evidence questions,
   each answered with a transcript citation or answered NO.
3. Unprovable claims grade DOWN. Missing transcript coverage grades DOWN and is
   reported.
4. The verdict includes: origin distribution + headline, assurance tier with
   its flags and evidence citations, coverage estimate, integrity caveats,
   grader identity, retrieval mode.
5. The FIRST verdict SHOULD be the published one; re-running for a better score
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

**Self-grading** (author-side, same model that produced the work) carries a
structural conflict of interest in both directions and MUST be flagged; a
third-party rerun is recommended before relying on it. Transcript custody: a
verdict is only as trustworthy as transcript custody; authors SHOULD archive
session logs immutably at publication.

## 10. Worked example (the case that motivated v0.2)

A long software session, heavily machine-authored, where the human (a) issued
domain rulings and command sets [origin O2 in parts, O1/O0 in others], (b)
refuted a fabricated power budget with a day-long hardware observation and
corrected his own instrument reading [**U**, costly], and (c) had a 226-test
suite with planted-bug validation run throughout [**T**, observed]. No
complete-diff review ceremony occurred [no **R**]; external review was pending
[no **X**].

- v0.1 verdict: `O0`, flags `U,T`, **preset: none** — the work fell out of the
  scheme for want of R. The floor, despite deep, evidenced human oversight.
- v0.2 verdict: **`LLMGD-3 · origin O1`** — A3 (understood and tested), with an
  origin distribution showing the human-directed mass. Honest, and respectable.

## 11. Versioning and changelog

Semantic versioning; labels MUST cite the version they were graded under.

**v0.2 (current).** (a) Assurance inverted: U and T are load-bearing, R is a
minor breadth signal, no longer a gate. (b) Grade split into two coordinates,
Origin (authorship) · Assurance (oversight); the LLMGD-N number now tracks the
assurance tier, with origin as a required companion tag. (c) Origin reported as
a distribution for composite works, not collapsed to the weakest part. (d)
Graders MUST ingest all project transcripts, not only the current session.

**v0.1 labels remain valid as v0.1.** They cite their version and their
grammar is unchanged; do not retro-grade them. New grading uses v0.2.

Changes happen by public PR to this repository.
