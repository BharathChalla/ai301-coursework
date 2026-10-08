---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer exactly one question about exactly one PR package: **is this
ready to submit?** A PR package is a candidate pull request (its title,
description, commit list, diff, and test evidence) read against the plan
it claims to implement (scope, files, test plan, and any recorded
deviation notes) and the issue that plan belongs to. Grade one package
per run. Never answer a different question (is the fix clever, is the
maintainer likely to merge it), and never answer from gut feel: answer
only by executing the components in this directory.

## Inputs and modes

Run in exactly one of two modes.

- **Live mode**: the author's own submission, checked before it goes
  out. Read these inputs: `plan.md` (including its Deviations section);
  the diff on the branch, produced by running `git diff main...HEAD`
  (three dots; substitute the repo's default branch if it is not
  `main`) from the working copy; the draft PR title and description in
  `pr_draft.md` (first line the title, the rest the description); the
  test evidence in `test_evidence.md`; and the issue's URL. Gather
  issue-side evidence live from the real repo (the issue thread, the
  PR template at `.github/PULL_REQUEST_TEMPLATE.md`, the contributing
  guide, and any stated AI policy). A house-chain student reads the
  house plan and the house repro pack instead of `plan.md` and their own
  repro; the same checks grade the same things. Treat the package as
  what the drafts and the diff contain: a file in the working directory
  that is not in the diff, the description, or the evidence is not part
  of the package.
- **Eval mode**: a package bundle (one markdown file holding the issue
  context, a repo-facts block, a plan-context block, and the candidate
  PR) is the whole world. Every fact comes from the bundle text.
  Fetch nothing, read nothing else, and run every check with the full
  verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else. It names the one
repository a PR may target and the house rules of that environment;
apply a house rule when grading the check it touches. Refuse to grade a
PR for any other repository. If the `Repo:` line of `scope.md` still
holds a bracketed placeholder such as `<ORG>/<PATH-REVIEW-REPO>`, stop
without grading, say that the `Repo:` line is unfilled, and tell the
author to fill it with their section's Path Review repo. Never guess a
scope. In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, after grading, read `voice-guide.md` and hold the
outgoing PR text, the title and the description, against it. Report
every rule the draft breaks in the summary, quoting the rule and the
offending line. A broken voice rule never changes the verdict on its
own: only a rubric check that reads the voice guide can do that, and
none does. In eval mode, ignore `voice-guide.md` entirely.

## Component reads

Three components do the grading. Read all of them before grading
anything.

- `rubric.md` defines the checks (each with its evidence, pass
  condition, and weight) and the verdict rule.
- `references/evidence-guide.md` maps where each evidence family lives
  in a PR package and what good looks like there.
- `procedure.md` is the operating procedure: execute it as written, in
  its order, without improvising around gaps. Where the procedure is
  silent on a step, say so in the summary as a procedure gap and apply
  the most literal reading of the rubric; never silently invent a step.

If `rubric.md` has no checks filled in, or `procedure.md` has no steps
filled in, refuse to grade: say that the tool cannot grade without a
rubric and a procedure, and stop.

## Verdict and output

The verdict space is binary: `accept` means submit (the PR is ready to
open) and `reject` means hold (it needs work first). There is no third
verdict, no "accept with reservations", and no score; put reservations
in a check's evidence line. Before the JSON block, write a short
per-check summary (one line per check, plus any voice-guide notes in
live mode). End the reply with the fenced JSON block below, valid and
last, with nothing after it. Use the PR URL as `item` in live mode and
the bundle id in eval mode.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- **Evidence first.** Never grade a check without naming the fact or
  quote that decided it. "Looks fine" is not evidence.
- **Grade the thing, not the polish.** Read the diff against the plan
  and the evidence against the test plan; never grade the description's
  tone, length, or formatting. A terse complete PR can be ready, and a
  beautiful confident one can be hiding drift.
- **The rubric decides, not you.** If a check passes by its stated
  condition but feels wrong, it still passes; note the tension in the
  summary and leave the fix to the rubric.
- **The procedure decides how, not you.** Follow `procedure.md` as
  written and report its gaps.
- **Unclear defaults to fail.** Treat `unclear` as the rubric's verdict
  rule directs; where the rule is silent, an unverifiable claim is a
  failing one, because a PR you cannot verify from the package is a PR
  that is not ready to submit.
- **An honest shortfall is not a failure.** A PR that discloses a
  limitation, a deferred edge, or a recorded deviation can be ready;
  hold a PR only for what it does, claims, or omits without saying so.
