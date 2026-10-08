# Structuring LLM-made contributions for review

Part of LLMGD v0.3: a companion to the [specification](SPEC.md) (§12). It is
guidance, not a grading input.

LLMGD says how much
machine work is in a contribution and how well its author governed it. This
document says how to shape the contribution so that a reviewer can check that
label quickly, and so that the work earns trust instead of asking for it.

It is written for the LLM setup as much as for the author using it: paste it into the
instructions of a coding agent, or follow it by hand. It grew out of porting
eight AsteroidOS watch apps to SailfishOS with an LLM in October 2026, in the
open, and it records what reliably worked there.

## The stance

- **LLM-made work is a prototype until a competent reviewer has checked it** and
  thereby taken ownership of the code. Say so, in the README and in releases
  (for example by publishing them as pre-releases).
- **Claim what you actually did.** Someone who designed the UX and tested every
  build is the author of the design and the tester of the result, not the
  author of code they have not read.
- **The scarce resource is the reviewer's attention.** Everything
  below exists to spend less of it.

## 1. Before writing: the ecosystem's conventions first

The most common failure of a newcomer with an LLM is not bad code but missing
ecosystem knowledge: the LLM follows the prompt literally, and the author
does not know what the experienced people would do differently.

- Before packaging, permission, architecture or API choices, find out what
  established projects in that ecosystem do. Look at the platform's own apps
  on a real device, at the build tooling's own files, at well-known community
  apps. If the author's instruction conflicts with the convention, say so
  before building.
- Treat "X is not allowed" or "X is impossible" as a lead to verify at the
  source, not as a conclusion.
- Before investing in large work, agree on the direction with the people who
  will review and maintain it.
- Write code in the idiom of the language and the surrounding code (for
  example, bindings in QML rather than imperative updates). Give one concept
  one name everywhere, and before adding code, ask what existing code the
  change could consolidate; the best consolidation is deletion.

## 2. Commits

- **One concern per commit.** A reviewer must be able to accept
  or reject it as a unit. Big work goes in small reviewable steps, never as a
  branch dump.
- **The message explains itself:** what changed, why, what was checked and how,
  and **what was not checked**; after a structural change, also what did
  *not* change. Name the mistakes made on the way and how they
  were found; they tell the reviewer where to look.
- **End with an LLMGD line** instead of a co-author trailer. A co-author tag
  confuses involvement with blame; the grade says what kind of involvement.
- Keep code comments to the non-obvious *why*. Comments that narrate the next
  line cost the reviewer time and hide the comments that matter.

## 3. Epistemic labels

Every factual claim the setup makes, in commits, READMEs and replies to its
author, carries its basis:

- **confirmed**: observed in this session (a log line, a measurement, a file
  read on the device);
- **inferred**: reasoned from confirmed facts, not observed;
- **recalled**: from training or memory, unverified here.

Never present a guess as a finding. After two failed fixes for the same
problem, stop and ask for a diagnostic instead of trying a third.

## 4. The README of every change

Each feature or port section in the README ends with two things:

- **"What was checked, and what was not"**: devices, versions, the tests run,
  and plainly the parts nobody has seen or tried.
- **The disclosure block**, the same LLMGD line as the commit.

## 5. Releases

- A **packages table**: file, and for which devices and versions it is meant.
- A **"Tested" line** that names the devices and who tested.
- **Known quirks** stated where users will read them, with the trade-off
  that caused them (for example "the camera runs in the background for
  instant switching, which pauses other audio").
- When a release is superseded, put a "Superseded by" link on top of the old
  one, so links in forum posts keep working.

## 6. A reviewer's entry point

Each repository gets a short `review-and-architecture-hints.md`:

1. **Where the code comes from**, with the exact `git diff` command that shows
   only the new work (for a port: against the fork point).
2. **A file map**, with line markers for large files.
3. **Read these first**: where the real logic lives.
4. **Skim**: boilerplate, stand-ins, assets, packaging.
5. **Worth questioning**: known weak spots, judgement calls and assumptions,
   including the ones nobody has fixed. A reviewer should find them listed,
   not stumble on them.
6. **How it was tested.**

Forty to sixty lines are enough. Check every claim in it against the code
before publishing it.

## 7. Testing while the author is away

When the setup works alone (overnight, say):

- **Build test hooks** into the app that exercise the real code paths and log
  the result (feed a file instead of a microphone, run the add/remove path of
  a store, log the camera's state), so behaviour can be checked without
  touching the screen.
- **No side effects the author would notice**: no sound, no lit screens at
  night, no settings left changed. Save and restore any state a test changes
  (brightness, test modes).
- Put whatever could not be checked this way on the "not checked" list for
  the author's morning.

## 8. Long sessions

- Keep **rolling records**: a state file (what is true now), a dated session
  log (what happened), a plan file for unattended runs, and a morning report
  that leads with what needs the author's eyes.
- Record durable lessons where the next session will read them, and correct
  them when they turn out wrong.

## 9. Talking to people

- **Replies to people are the author's own.** The LLM drafts posts in the
  author's voice, marked as drafts; the author edits and posts.
- When someone gives a hint (a forum reply, a review), act on it visibly: say
  in the commits and release notes which changes it triggered.

## 10. Applying this to existing work

Work that was made before any of this can be brought into shape afterwards,
by the same kind of LLM session that made it. The rule that keeps this
honest: **the history is restructured, the result is not changed.**

1. **Leave the original branch untouched.** Create a new branch from the
   point where the work started (the fork point, or the last commit before
   the LLM work).
2. **Rebuild the history in single-concern commits** on the new branch, each
   with a message after section 2: what changed, why, what was checked, what
   was not, and an LLMGD line graded from the evidence in the transcripts.
   Mistakes found in the old history stay mentioned; they are information for
   the reviewer.
3. **Prove equivalence:** `git diff <original-branch> <clean-branch>` must be
   empty before anything else is added. If it is not, the refactor changed
   the work and is not done.
4. **Then add the documentation** in separate commits: the README's "What was
   checked, and what was not" (section 4) and `review-and-architecture-hints.md`
   (section 6). Check every claim in them against the code.
5. **Grade** with [GRADING_PROMPT.md](GRADING_PROMPT.md) over the transcripts
   that produced the work, and publish the first verdict.
6. **Hand the clean branch to the author** for review and the merge decision.
   Nothing is force-pushed over the original.

A prompt for the session:

    Read PRACTICE.md and SPEC.md from https://github.com/moWerk/llmgd-specs.
    Refactor the work on branch <original> into a reviewable branch <clean>,
    following PRACTICE.md section 10: same final tree as <original> (prove it
    with an empty git diff), single-concern commits with honest messages and
    LLMGD lines graded from the transcripts, then the README section and
    review-and-architecture-hints.md as separate commits. Do not push over
    <original>. Report what you could not verify.

Where these rules come from: see Credits in the [README](README.md).

## Templates

Commit message:

    <Area>: <what changed, short>

    <why: who asked for it, what problem it solves>
    <how: the mechanism, in a few lines>

    Checked: <what, on which device or setup, how>
    Not checked: <what nobody has seen or tried>

    Disclosure: LLMGD-<n> · origin O<n> (<one-line plain summary>)
    LLMGD: v0.3; assurance=A<n>; flags=<...>; origin={...}; origin_headline=O<n>; scope=<...>; graded-by=<model>; retrieval=<...>

README section ending:

    What was checked, and what was not:
    - <confirmed checks, with devices and versions>
    - <what has not been seen or tried>

    ```
    Disclosure: ...
    LLMGD: ...
    ```
