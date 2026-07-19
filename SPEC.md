# LLMGD Specification v0.1

Large Language Model Governance Disclosure. A standardized, machine-gradeable
declaration of LLM involvement in published work.

The key words MUST, MUST NOT, SHOULD, SHOULD NOT and MAY are to be
interpreted as described in RFC 2119.

## 1. Design principles

1. **Consumer-driven.** The label answers the questions its readers actually
   have. Reviewers need the verification state. Future maintainers need
   intent provenance ("did a human ever mean this line?"). Auditors need
   generation share. Authors need honest self-accounting.
2. **Decisions, not keystrokes.** Authorship is graded on who made the
   decisions the bytes express. Keystroke counting is meaningless in an era
   of autocomplete; a human's words typed by a machine remain the human's
   words.
3. **Evidence or it did not happen.** Every level and flag is defined by
   observable events in the interaction transcript. Claims that cannot be
   evidenced grade DOWN. This is the standard's teeth.
4. **Composable, with presets.** Like Creative Commons: precise building
   blocks underneath, a small set of memorable presets on top.

## 2. The Origin axis (O0–O4)

Origin grades the causal source of the work's decisions: design choices,
structure, behavior, wording.

| Level | Definition |
|---|---|
| **O0** | Machine decided and expressed. Human supplied goals or bare prompts only. |
| **O1** | Human set constraints, specifications or corrective direction. Machine designed the details and expressed them. |
| **O2** | Human designed. Machine purely expressed (typing, formatting, translation of the human's decisions into artifact form). |
| **O3** | Human designed and expressed. Machine edited, restructured, polished or rescoped existing human text/code. |
| **O4** | No LLM involvement. The anchor point. |

Where parts of one artifact differ, see Section 6 (Scope).

Detection guidance: O-level evidence is the transcript's decision record.
Specifications, corrections, design reversals and style rulings issued by the
human raise the human's origin share. Machine-proposed designs accepted
without modification lower it.

## 3. The Assurance flags (R, U, T, X)

Assurance records what verification the content received before publication.
Flags are composable because their evidence types are incomparable: neither
"tested but not understood" nor "understood but untested" dominates the
other. A ladder here would lie.

| Flag | Name | Definition | Transcript evidence required |
|---|---|---|---|
| **R** | Read | Every line/word of the published artifact passed a human read-back before publication. | The artifact (or its complete diff) presented, and an explicit human acknowledgment after presentation. |
| **U** | Understood | Comprehension demonstrated, not claimed. | Costly signals: the human corrected errors, reversed designs, caught inconsistencies, imposed style rulings, or answered/asked questions that require internal understanding. |
| **T** | Tested | Empirically exercised against reality. | Build results, device or field test reports, reproductions, CI runs, with results fed back into the session. Mark sub-type: `observed` (machine-visible results) or `attested` (human-reported results). |
| **X** | eXternally reviewed | An independent human reviewed the work. | Normally accrues AFTER publication. A shipped label MUST NOT self-claim X; review infrastructure (e.g. a merged PR review) adds it. |

## 4. Evidence taxonomy

Graders MUST weight evidence by class:

- **Costly signals (strong).** Actions that are hard to fake: catching a
  real error, reversing a design with reasons, stopping the machine
  mid-action, style corrections applied consistently. Primary basis for U.
- **Cheap talk (weak).** Statements like "I fully understand this" carry
  near-zero weight on their own.
- **Observed vs attested.** Machine-visible events (a test log in the
  transcript) are `observed`. Human reports of off-transcript events ("it
  runs green on my watch") are `attested`. Both count; the verdict MUST
  state which kind supported each flag.

## 5. Presets

| Preset | Grid position | Reading |
|---|---|---|
| **LLMGD-0** | O0, no flags | Fully generated, published as emitted. Highest scrutiny advised. |
| **LLMGD-1** | O0–O1 + R | Generated, but a human read all of it before shipping. |
| **LLMGD-2** | O1 + R,U | Human-steered design, machine detail, read and demonstrably understood. |
| **LLMGD-3** | any O + X | Independently human-reviewed, whatever the origin. |
| **LLMGD-4** | O2 + R,U,T | Machine as pure executional tool. Human designed it, understood it, verified it. |
| **LLMGD-5** | O3 + R | Human-written work that a machine edited or audited. |

Presets are a courtesy summary. The machine-readable line (Section 7)
carries the exact coordinates and always wins over the preset on conflict.

## 6. Scope

The declaration unit SHOULD be the publishing unit (commit, PR, document).
A composite unit is graded on its **most-LLM part** (lowest O, weakest
assurance), with an OPTIONAL per-part breakdown:

```
LLMGD: v0.1; origin=O2; assurance=R,U,T; scope=code+messages
LLMGD-part: code=O1; messages=O2; translations=O3
```

A PR label is the weakest label among its commits.

## 7. Label format

Human line, then machine line:

```
Disclosure: LLMGD-<preset> (<origin phrase>; <assurance phrase>)
LLMGD: v<major>.<minor>; origin=O<0-4>; assurance=<flags|none>; scope=<parts>; graded-by=<grader>; retrieval=<mode>
```

Grammar (informal ABNF):

```
label      = "LLMGD: " version "; " origin "; " assurance "; " scope "; " grader "; " retrieval
version    = "v" 1*DIGIT "." 1*DIGIT
origin     = "origin=O" ("0"/"1"/"2"/"3"/"4")
assurance  = "assurance=" ( "none" / flag *("," flag) )
flag       = "R" / "U" / "T" / "T-attested" / "X"
scope      = "scope=" part *("+" part)
grader     = "graded-by=" token
retrieval  = "retrieval=" ("author-side" / "third-party" / "self-claimed")
```

`retrieval` declares who ran the grading: `author-side` (the author or the
author's own agent), `third-party` (independent grader with transcript
access), `self-claimed` (no machine grading performed; weakest form).

## 8. Grading protocol

1. Input: the interaction transcript(s) that produced the work, plus the
   published artifact.
2. The grader runs the standardized rubric (GRADING_PROMPT.md): binary
   evidence questions, each answered with a citation from the transcript or
   answered NO.
3. Unprovable claims grade down. Missing transcript coverage grades down.
4. The verdict includes: axis coordinates, flags with evidence citations,
   coverage estimate, provenance caveats, grader identity and retrieval mode.
5. The FIRST verdict SHOULD be the published one. Re-running until a
   favorable verdict appears ("verdict shopping") defeats the standard;
   graders MUST include a run declaration if known.

## 9. Integrity considerations

Two attack directions exist and the grader MUST screen for both:

- **Author inflation (gaslighting up).** Self-serving transcripts, cheap-talk
  claims, curated excerpts. Mitigations: costly-signal weighting, coverage
  reporting, grade-down defaults.
- **Hostile deflation (shit-talking down).** A third party grading someone
  with a doctored or selectively truncated transcript. Mitigations: the
  grader MUST report coverage and continuity (missing turns, style
  discontinuities, truncation artifacts) and MUST mark verdicts from
  incomplete transcripts as `coverage-limited`, naming what was missing.

Transcript custody: transcripts are alterable by their holder. Verdicts are
only as trustworthy as transcript custody. Authors SHOULD archive session
logs immutably (content-addressed or hash-chained) at publication time.
A verdict from an archived, hash-referenced transcript outranks one from a
pasted excerpt.

Multi-session and compacted work: grade only what is in evidence. Context
lost to summarization defaults down, never up.

## 10. Versioning

The spec uses semantic versioning; labels MUST cite the version they were
graded under. Definition changes bump minor; axis/flag semantic changes bump
major. Changes happen by public PR to this repository.
