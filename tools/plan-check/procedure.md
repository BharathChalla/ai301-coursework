# Procedure: how this skill grades a plan package

## Read order

1. Read the issue context first (title, body, labels): it names the
   defect a plan must address and, in live mode, whether the repo has a
   stated bug-report template or contribution policy worth noting.
2. Read the thread highlights (or the live thread) next, and write down
   any maintainer comment that names a cause, requests a specific test,
   states a preferred approach, or points at a specific file/PR. This is
   the "explicit maintainer direction" the Comms check needs; if none
   exists, note that explicitly rather than leaving it implicit.
3. Read the repro-evidence block third, and write down: the exact
   behavior it confirms, every control run it describes, and what each
   control rules in or out. This is the grounding the Diagnosis check
   and the Test-plan check both read against, so get it down before
   looking at the candidate plan at all — reading the plan first biases
   which parts of the evidence you notice.
4. Read the candidate plan in full (diagnosis, scope, files/approach,
   test plan, risks), then the candidate plan comment last, since the
   comment is graded partly on whether it reflects the plan and the
   thread the executor just read.
5. In live mode only: before any of the above, read `scope.md` and
   confirm the issue is in the scoped repo; note any house rule that
   changes how a check applies (for example, a classmate's plan comment
   on the same issue never blocks or lowers this one). Then read
   `voice-guide.md` so the comment can be checked against it in step 4's
   pass.

## Evidence gathering

For each check, the concrete fact to write down before grading:

- **Diagnosis grounded**: the plan's one-sentence stated cause, and the
  single strongest control run from the repro evidence that either
  matches or contradicts it. Quote both.
- **Bounded scope**: list every numbered item in the plan's
  approach/proposed-changes section. Tag each as "the one defect" or
  "separable additional work." More than one "separable additional
  work" tag is the fact this check needs.
- **Executability**: list every file/location the plan names. Scan the
  approach text for a deferred-decision phrase (a hedge about which
  approach, which layer, or where, left unresolved). Record the phrase
  verbatim if found.
- **Test plan decisive**: the plan's stated expected result. Check
  whether it names a concrete artifact/exit-code/threshold from the
  repro evidence, or only a feeling/unscoped action.
- **Honesty**: the plan's risk/unknown language, if any, and one fact
  from the repro evidence that the plan's confidence should account for
  (a partial control, a caveat, an untested platform). Note whether the
  plan's certainty matches that fact.
- **Comms**: the maintainer-direction note from read-order step 2 (or
  "none found"), read against whether the plan comment engages it. The
  repo-facts contribution-policy line, read for the two disclosure
  signals named in `references/evidence-guide.md`'s Comms section.

## Check execution

Run the checks in the table order above (Diagnosis, Scope, Executability,
Test plan, Honesty, Comms). Grade each independently: a fail on an
earlier check does not excuse skipping a later one, because the output
JSON reports every check's grade regardless of the verdict.

For each check, grade `pass` or `fail` by applying its pass condition in
`rubric.md` to the fact recorded in Evidence gathering above. Grade
`unclear` only when the package genuinely does not contain the fact the
check needs (for example, no repro-evidence block at all, live mode's
claim-only-style gap does not apply here since a plan package always
carries repro evidence by definition of what it builds on — so `unclear`
here should be rare; if it happens, say plainly what's missing rather
than guessing).

A check may be graded without re-reading the whole package once its
fact is recorded in Evidence gathering; do not re-derive facts already
written down.

## Verdict assembly

Apply the rubric's verdict rule: accept only if all six checks pass;
`unclear` on any required check counts as fail, same as an explicit
fail. Quote in the output's `evidence` field the exact fact recorded in
Evidence gathering for each check (the control run, the deferred-decision
phrase, the maintainer-direction note, etc.), not a restatement of the
pass condition. In live mode, after grading, hold the plan comment
against `voice-guide.md` and report any broken rule in the summary; a
broken voice-guide rule does not change the verdict unless a rubric
check reads it directly.
