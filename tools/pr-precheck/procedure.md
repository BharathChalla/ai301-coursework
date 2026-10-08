# Procedure: how this tool grades a PR package

## Read order

1. Read the issue context first (title, body, labels, and the thread
   highlights or live thread). Note the defect the PR must fix, and any
   explicit maintainer direction in the thread (a named cause, a
   requested test, a preferred approach).
2. Read the repo-facts block (live: the PR template, the contributing
   guide, any AI policy, and CI config). Write down the template's
   section names, its checklist items, any artifact it says is required
   (a changelog entry, a `Closes #N` line), and the AI-policy line
   verbatim. Do this before the PR so the standards checks are read
   against the repo's asks, not against what the PR happens to contain.
3. Read the plan-context block (live: `plan.md`) before the diff. Write
   down: the in-scope line, the not-in-scope line, the named files, the
   test plan (every scenario it names), the repro evidence's steps, and
   every deviation note. The plan has to be in your head first because
   the diff is graded against it, hunk by hunk; reading the diff first
   anchors you on what the author did instead of what they promised.
4. Read the diff next, hunk by hunk, and number the hunks. For each
   hunk, write the file and one phrase for what it does. Also read the
   commit list (live: `git log main..HEAD --oneline`) and, live only,
   `git status` for committed working notes.
5. Read the test evidence section (live: `test_evidence.md`), then the
   PR description (live: `pr_draft.md`) last. The description is read
   last because its claims are graded against everything above.
6. Live mode only, before step 1: read `scope.md`, confirm the repo is
   the scoped one (stop if the `Repo:` line is an unfilled
   placeholder), and note the house rules. After grading, read
   `voice-guide.md` for the voice seam.

## Evidence gathering

Record these facts, one block per check, before grading any check:

- **Diff stays inside the plan**: for each numbered hunk, tag it
  `plan` (serves the one planned fix), `support` (a test or changelog
  entry for that fix), `deviation` (covered by a named deviation note),
  or `extra`. Record the file and hunk number of every `extra`.
- **Description promises only what the diff delivers**: list every
  factual claim in the description (fidelity claims, deliverables, "no
  changes beyond", counts). For each, record whether the diff or a
  deviation note makes it true. Also list each plan deliverable and
  whether the diff contains it; record any missing one and whether the
  description or a deviation note discloses it.
- **Test evidence is decisive**: list the scenarios in the plan's test
  plan. For each, record whether the evidence shows a before and an
  after with an observable result, quoting the command and output line.
  Record whether the repo's own checks appear with an outcome, and the
  outcome. Record any "tested locally" style sentence that has no
  output behind it.
- **Diff is free of debris**: scan the diff's added lines for debug
  output, commented-out code, dead or unused additions, added TODOs,
  re-printed duplicate blocks, and whitespace or reformatting hunks on
  lines unrelated to the fix. Record each with its file and the added
  line. Record committed working notes from `git status` or the file
  list.
- **Repo's template and checklist asks are met**: for each template
  section and each checklist item recorded in read-order step 2, record
  whether the description fills it with real content, ticks or
  explains it, or leaves it blank. Record whether the issue link is
  present and whether a stated required artifact is in the diff.
- **AI-use disclosure**: record the AI-policy line verbatim, classify it
  as silent, voice-only, or explicit-disclosure/ban (also explicit if
  the template has a disclosure section), and record the description's
  disclosure sentence, quoted, or "none".

## Check execution

Run the checks in rubric order: Diff stays inside the plan, Description
promises only what the diff delivers, Test evidence is decisive, Diff is
free of debris, Repo's template and checklist asks are met, AI-use
disclosure. Grade each independently from its recorded block, and
report every check's grade even after an earlier one has failed.

For each check, apply its pass condition to the recorded facts and write
`pass` or `fail`. Grade `unclear` only when the package genuinely lacks
the fact the check needs (for example, no test-evidence section exists
and the plan's test plan is absent too); say what is missing. Where a
fact is present but the pass condition feels ambiguous, apply the most
literal reading and record the ambiguity in the summary. Do not
re-read the whole package for a check once its block is recorded. When
a plan deviation note exists, treat the hunks and claims it covers as
inside the plan.

## Verdict assembly

Apply the rubric's verdict rule: `accept` only if all six checks pass;
a `fail` or an `unclear` on any check gives `reject`. For the output,
quote in each check's `evidence` line the exact recorded fact that
decided it (the extra hunk's file, the false claim, the missing
scenario, the debris line, the blank section, the policy words), not a
restatement of the pass condition. When more than one check failed,
list the first failing check in rubric order first in the summary as the
deciding check, and still report all grades in the JSON. In live mode,
after the verdict, hold the title and description against
`voice-guide.md` and report each broken rule in the summary.
