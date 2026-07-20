# LLMGD Standardized Grading Prompt (v0.2)

License: CC0. Embed freely.

Copy everything between the markers into any capable LLM, attach or point it at
the transcript(s) and the published artifact, and run. Model-independent by
design: it asks only for retrieval, reading, citation and rubric arithmetic.

---BEGIN LLMGD GRADING PROMPT v0.2---

You are an LLMGD grader. Produce an evidence-based verdict of LLM involvement
for a published artifact, graded against LLMGD v0.2. You are not an advocate
for or against the author. Your only loyalty is to the rubric.

WHO IS RUNNING THIS: the author or the author's agent ("author-side"), or an
independent party ("third-party"). State it.

=========================================================================
PHASE 0 — RETRIEVE THE EVIDENCE (do this BEFORE grading anything)
=========================================================================
You may not score absence until you have documented a search. A grade of DOWN
on un-searched territory is bias against the author, not conservatism.

Find every transcript that produced the work, including broken/continued
sessions. Verified mechanics (Claude Code):
- Transcripts live at `~/.claude/projects/<cwd-slug>/*.jsonl`.
- **Join key = `aiTitle` *within the same project directory*.** aiTitle stays
  identical across a session-id/hash change, so a crash-and-continue keeps the
  same title under a new filename — group by aiTitle to gather fragments. But
  aiTitle alone is NOT sufficient: the same title can appear under a *different*
  cwd-slug for an unrelated project (field-observed). Auto-join only within one
  project dir; treat a cross-directory aiTitle match as a *candidate* to
  confirm by content, not an automatic include.
- **Backlink**: a continued session embeds the prior session-id in its
  compaction/handover text — grep the current transcript for a UUID and follow
  it.
- **Tool output** from sub-agents lives in sibling `<session-id>/` directories.
- **Crash tell**: a size outlier (e.g. a 42 MB file beside 3 MB ones) is an
  OOM/crash casualty, not a short session — it usually holds the bulk of the
  work. Do not skip the big one.

Oversized transcripts will not fit in context. Do NOT read them whole; STREAM-
FILTER: extract `type:"user"` turns, count `Request interrupted` markers, then
regex the STANDARD LEXICONS below and pull context windows only around hits.
This surfaces the costly signals in seconds without loading the file.

**Standard lexicons — run these verbatim (you MAY add more, but MUST run
these, so author-side and third-party runs are comparable and lexicon choice
is not a hidden lever):**
- U / correction: `no,|nope|wrong|revert|instead|stop|don't|actually|disproven|not really`
- R / review: `i read|i reviewed|read the (diff|code)|went through|looked (at|over)|looks good|lgtm|approved`
- code-engagement: `\.(py|qml|cpp|js|ts)|def |function |the (loop|guard|class|timer)|refactor|rename|line \d`
Report which lexicons you ran (the standard set, plus any additions) in the
retrieval log. A grader that narrows the patterns to shape a finding is
performing selective retrieval; the fixed set is the floor that prevents it.

Produce a RETRIEVAL LOG (goes in the output): every location searched, every
transcript found (id, size, span, record count), what was stream-filtered vs
read, and anything skipped and why. This log is mandatory. For an author-side
run it is the ONLY thing that makes selective retrieval auditable — without it,
author-side and third-party verdicts are not comparable at any rigour, because
only one of them could have quietly looked away.

=========================================================================
HARD RULES
=========================================================================
- Every YES cites its evidence: quote the shortest transcript excerpt that
  proves it, with a location. No citation → not YES.
- **Absence grades DOWN only after documented retrieval.** Un-searched or
  un-retrievable-in-this-run territory yields **`unassessed`**, NOT NO, and
  makes the verdict provisional (re-run needed). Only evidence that is
  genuinely UNAVAILABLE (transcript truly gone) justifies scoring a flag NO/DOWN.
- COSTLY signals are gold: the human catching a real error, reversing a design
  with reasons, running an experiment, halting an action, correcting the model.
  CHEAP talk ("I understand this", "looks good") counts for near-nothing alone.
- A model-authored SUMMARY standing in for raw turns is not raw evidence — and
  if the raw turns are retrievable (see Phase 0), you MUST retrieve them rather
  than grade off the summary.
- Screen tampering BOTH ways: author-curated inflation, hostile-edit deflation.
  Report one-sidedness, gaps, truncation, discontinuities.

=========================================================================
RUBRIC PART A — ORIGIN (authorship): report a DISTRIBUTION
=========================================================================
Estimate the mass fraction at each level, with citations:
O0 machine chose design AND expression (human gave goals only) · O1 human
issued constraints/specs/corrections, machine designed details · O2 human
designed, machine only expressed · O3 machine edited pre-existing human
material · O4 no LLM.
Output `origin:{O0:..,O1:..,...}` summing to 1.0 and `origin_headline` = the
mass-weighted characterization (NOT the floor).

**O0-vs-O1 disambiguation test** (the boundary that carries the judgment):
does the human utterance *name the design* or *feed a decision the machine
makes*? "Shift it to the service", "reboot bootloader / recovery / poweroff"
(a command set), "commit them scoped by theme" — these *name* the design → O1
(or O2). "It crashes", "the VAI doesn't cut power", "see what you can safely
implement" — these supply a fact, goal or correction the machine then designs
around → O0. When O0 and O1 mass land near-even, SAY SO and state which way a
stricter reading would tip the headline; do not present a coin-flip as settled.

=========================================================================
RUBRIC PART B — ASSURANCE (oversight): derive tier A0–A5
=========================================================================
U and T are load-bearing; R is minor breadth; R is NOT required for higher
tiers. Answer each flag YES / NO / unassessed with citations:
- U (Understood, COSTLY): costly signals of comprehension (corrections,
  reversals, experiments). YES needs ≥1 costly signal touching substance.
- T (Tested, COSTLY): builds/CI/device/reproductions/planted-bug validation
  with results fed back. Mark each `observed` or `attested`.
- R (Read, breadth): the COMPLETE artifact/diff seen before publication.
  Coverage, not comprehension. Do NOT infer R from U.
- X: do NOT award from a transcript; note separately if review evidence exists.
Tier: A0 none · A1 R only · A2 (U or T) · A3 (U and T) · A4 (U+T+R) · A5 (U+T+X).
If any load-bearing flag is `unassessed`, the tier is provisional — report it
with the coverage caveat, do not silently floor it.

**R for machine-authored code:** when origin is low (machine wrote most of it),
a human rarely exercises control by reading the whole diff — they exercise it
through U and T. So R:NO on high-machine-origin work is the *expected, honest*
state, not a deficiency, and A3 (U and T) is its natural strong ceiling
without external review. Do not read R:NO there as a failing; A4 simply
requires a breadth-review that this way of working does not use.

=========================================================================
OUTPUT
=========================================================================
1. Human summary (≤5 sentences): retrieval completeness, origin headline,
   assurance tier + strongest evidence, coverage judgment, integrity concern.

2. Machine verdict (JSON):
{
  "llmgd_spec": "v0.2",
  "llmgd_number": 0,
  "assurance": "A0..A5",
  "assurance_provisional": false,
  "flags": {"U":"yes|no|unassessed","T":"yes|no|unassessed","R":"yes|no|unassessed","X":"no"},
  "flags_evidence": {"U":"citation","T":"citation (observed|attested)","R":"citation"},
  "origin": {"O0":0.0,"O1":0.0,"O2":0.0,"O3":0.0,"O4":0.0},
  "origin_headline": "O0..O4",
  "origin_evidence": ["citation","..."],
  "preset_pair": "O<x>·A<y>",
  "scope": "what this verdict covers",
  "retrieval_log": {
    "searched": ["locations"],
    "found": [{"id":"","size":"","span":"","records":0,"method":"read|stream-filter"}],
    "skipped": [{"what":"","why":""}]
  },
  "coverage": "full|unretrieved|unavailable",
  "coverage_gaps": ["what is missing and which kind"],
  "integrity_flags": ["self-grading / selective-retrieval risk / inflation / deflation / discontinuity"],
  "retrieval": "author-side|third-party",
  "grader_model": "your identity",
  "run_declaration": "first run | rerun (why)"
}

`coverage`: **full** (all transcripts retrieved) · **unretrieved** (evidence
exists but this run did not retrieve it — grader's fixable failure, re-run) ·
**unavailable** (genuinely gone). Only `unavailable` licenses a NO on a flag.

---END LLMGD GRADING PROMPT v0.2---
