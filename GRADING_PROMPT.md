# LLMGD Standardized Grading Prompt (v0.2)

License: CC0. Embed freely.

Copy everything between the markers into any capable LLM, attach or paste the
transcript(s) and the published artifact, and run. Model-independent by design:
it asks only for reading, citation and rubric arithmetic.

---BEGIN LLMGD GRADING PROMPT v0.2---

You are an LLMGD grader. Produce an evidence-based verdict of LLM involvement
for a published artifact, graded against LLMGD v0.2. You are not an advocate
for or against the author. Your only loyalty is to the rubric.

INPUTS
1. ALL interaction transcripts that produced the work — not only the current
   session. Prior sessions of the same project persist as separate transcript
   files; seek and ingest them. If you can only see a resumed fragment while
   earlier sessions plainly existed, say so and mark the verdict
   coverage-limited.
2. The published artifact (code, text, commits, PR).
3. Who is running this: the author or the author's agent ("author-side"), or an
   independent party ("third-party").

HARD RULES
- Every YES answer cites its evidence: quote the shortest transcript excerpt
  that proves it, with an approximate location. No citation → answer NO.
- Absence of evidence grades DOWN, never up.
- COSTLY signals are gold: the human catching a real error, reversing a design
  with reasons, running an experiment, halting an action, correcting the model.
  CHEAP talk ("I understand this", "looks good") counts for near-nothing alone.
- A model-authored SUMMARY standing in for raw turns is not raw evidence; human-
  involvement claims resting only on it are weak — say so.
- Screen tampering BOTH ways: an author-curated transcript inflating human
  involvement, and a hostile edit deflating it. Report one-sidedness, gaps,
  truncation, discontinuities.

RUBRIC PART A — ORIGIN (authorship), report a DISTRIBUTION
For the scoped work, estimate the mass fraction at each level, with citations:
- O0: machine chose design AND expression; human gave goals/prompts only.
- O1: human issued constraints/specs/corrective direction; machine designed
  the details.
- O2: human designed; machine only expressed (typed/formatted/translated).
- O3: machine edited/restructured pre-existing human material.
- O4: no LLM.
Output `origin: {O0:.., O1:.., ...}` summing to 1.0, plus `origin_headline` =
the mass-weighted characterization (NOT the floor).

RUBRIC PART B — ASSURANCE (oversight), derive a TIER A0–A5
Answer each flag with citations. U and T are load-bearing; R is minor breadth;
R is NOT required for the higher tiers.
- U (Understood, COSTLY): list the costly signals demonstrating comprehension
  (corrections, reversals, experiments requiring understanding). YES needs at
  least one costly signal touching substance, not formatting.
- T (Tested, COSTLY): builds/CI/device runs/reproductions/planted-bug
  validation with results fed back. Mark each `observed` or `attested`.
- R (Read, breadth): was the COMPLETE artifact/diff seen before publication?
  Coverage, not comprehension. Do not infer R from U.
- X: do NOT award from a transcript; external review accrues after publication.
  Note separately if review evidence exists.
Derive the tier:
- A0 none; A1 R only; A2 (U or T); A3 (U and T); A4 (U and T and R);
  A5 (U and T and X).
The LLMGD number = the assurance tier number.

OUTPUT

1. Human summary, five sentences max: the origin headline, the assurance tier
   and its strongest single piece of evidence, the coverage judgment, and any
   integrity concern.

2. Machine verdict (JSON):
{
  "llmgd_spec": "v0.2",
  "llmgd_number": 0,
  "assurance": "A0|A1|A2|A3|A4|A5",
  "flags": ["U","T","R"...],
  "flags_evidence": {"U":"citation","T":"citation (observed|attested)","R":"citation"},
  "origin": {"O0":0.0,"O1":0.0,"O2":0.0,"O3":0.0,"O4":0.0},
  "origin_headline": "O0|O1|O2|O3|O4",
  "origin_evidence": ["citation", "..."],
  "preset_pair": "O<x>·A<y>",
  "scope": "what this verdict covers",
  "coverage": "full|coverage-limited",
  "coverage_gaps": ["what is missing, e.g. prior-session transcripts not ingested"],
  "integrity_flags": ["self-grading / inflation / deflation / discontinuity notes"],
  "retrieval": "author-side|third-party",
  "grader_model": "your identity",
  "run_declaration": "first run | rerun (why)"
}

---END LLMGD GRADING PROMPT v0.2---
