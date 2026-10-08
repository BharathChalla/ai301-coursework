# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

Where it lives: in an eval bundle, the plan-context block (its "Scope"
pair of in-scope and not-in-scope lines, its named files, its test plan,
and any deviation note) and the candidate PR's diff and description. In
live mode, `plan.md` (its Scope, Files, and Deviations sections), the
output of `git diff main...HEAD`, and the draft description in
`pr_draft.md`.

What good looks like: list the files the plan names, then list every
file and hunk the diff changes; each hunk maps to the plan's one fix, to
a test or changelog entry for that fix, or to a recorded deviation note.
A mismatch re-tied by a deviation note is honest; the same mismatch with
no note is drift. Drift runs both directions: more than the plan (a
second fix, a rename pass, a new option, a rewrite of an unnamed
function) and less than the plan (a promised deliverable absent from the
diff). The description is read here too: every claim of fidelity ("exactly
as planned", "no changes beyond the fix") or of a deliverable ("docs now
document X") is checked against the diff, and a claim the diff
contradicts is drift even when the diff itself is fine.

## Test evidence (harness category: not-tested)

Where it lives: in an eval bundle, the candidate PR's "Test evidence"
section, read against the plan-context block's test plan and repro
evidence. In live mode, `test_evidence.md` (and the "Testing" section of
the draft description), read against `plan.md`'s test plan and the repro
steps posted on the issue.

What good looks like: the plan's own repro command, run before and after
the change, with the output pasted and the expected-after stated, on the
path the fix actually changes; every scenario the test plan names is
either shown or plainly marked as not run; and the repo's own checks (the
test suite and linters the contributing guide or CI config names) are
shown with their outcome. A control run alone is not the repro; a test
that passes without the fix does not prove it. What is not evidence:
"tested locally", "tests pass", "verified working", with no output
behind it. A failing or skipped check reported with its reason is
evidence, not a defect.

## Diff quality (harness category: unreviewable)

Where it lives: in an eval bundle, the candidate PR's unified diff and
commit list. In live mode, `git diff main...HEAD` and `git log
main..HEAD --oneline`; run `git status` as well, since working notes
(`plan.md`, `pr_draft.md`, `test_evidence.md`) must not appear in the
diff.

What good looks like: the fix is visible at a glance and nothing rides
along. Debris tells: added print or log lines for debugging, commented-out
blocks, dead or unused functions or imports, a TODO added by the PR,
blank-line or re-indent churn on lines unrelated to the fix, a block of
lines re-printed identically, and working notes committed into the
branch. Churn that is mechanical and sits around a correct fix is still
debris. Commit messages like "wip" are context only.

## Standards and comms (harness category: standards-wall)

Where it lives: in an eval bundle, the repo-facts block's "pull requests"
line (template sections, checklist, linked requirements) and
"contribution policy" line (including any AI-use rule), read against the
candidate PR's description and diff. In live mode, the repo's
`.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, and any
`AI_POLICY.md`, read against the draft description and the diff.

What good looks like: each section the template names has real content
written for this change, the issue is linked the way the template asks,
checklist boxes that apply are ticked (and only ticked if true) or
explained, and any artifact the repo states as required for this kind of
change is in the diff. Template-driven repos name the check items; a
checklist left unticked and unexplained, a missing `Closes #N`, or a
required changelog entry absent from the diff is a visibly ignored ask.
For AI use: silence in the policy means no disclosure is needed; a policy
that asks only for human-voiced comments or for the human to understand
the work is met by a plain, first-person description; explicit
disclosure language ("must be disclosed", "state the tool and extent")
or a stated ban on a category of AI involvement, or a disclosure section
in the PR template, needs a disclosure sentence in the description in the
author's own words. Naming a dedicated policy file is not itself the
signal: read what the summarized line asks. Whether the description's
claims match the diff is plan fidelity, above.
