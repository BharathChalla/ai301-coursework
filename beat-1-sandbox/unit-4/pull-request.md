# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/111

**Branch**

`fix/62-health-check-redis-url` (on my fork, `BharathChalla/pathreview-ai301-fa26-s3`)

**My own pr-precheck verdict on the draft** (live mode, rubric/procedure as uploaded to
`tools/pr-precheck/`, run on branch head `104798f`): `accept`, all six checks `pass`.
Per-check evidence: the three hunks are `health.py` (plan), `tests/unit/test_health.py`
(support) and the `pyproject.toml` `attr-defined` drop (covered by the Deviations section of
`plan.md`); every claim in the description is true of the diff; the test evidence shows the
repro before and after plus a stopped-Redis control, and the repo's own checks with their
outcomes and limitations; no debris; every template section has content with `Closes #62`;
the repo states no AI policy and the description discloses AI use anyway.

```json
{
  "item": "draft PR for https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62 (branch fix/62-health-check-redis-url @ 104798f)",
  "checks": [
    {"name": "Diff stays inside the plan", "grade": "pass", "evidence": "3 hunks: health.py (plan), tests/unit/test_health.py (support), pyproject.toml attr-defined drop (covered by plan.md Deviations)"},
    {"name": "Description promises only what the diff delivers", "grade": "pass", "evidence": "all 3 listed changes are in the diff; 'Not changed: core/config.py' is true; pyproject deviation disclosed in the Changes list"},
    {"name": "Test evidence is decisive", "grade": "pass", "evidence": "before/after /health output with redis flipping unhealthy->healthy plus a stopped-Redis control; ruff/black/mypy/pytest outcomes shown (378 passed); mypy workaround and unrun integration tests disclosed"},
    {"name": "Diff is free of debris", "grade": "pass", "evidence": "no debug output, commented-out code, dead code, churn, or added TODOs in the 3 hunks; git status clean"},
    {"name": "Repo's template and checklist asks are met", "grade": "pass", "evidence": "all template sections filled with real content, 'Closes #62' present, unticked CI/integration/typecheck boxes each explained"},
    {"name": "AI-use disclosure matches the repo's policy", "grade": "pass", "evidence": "CONTRIBUTING.md states no AI policy and the template has no disclosure section; description still discloses 'I used an AI assistant (Claude Code)'"}
  ],
  "verdict": "accept"
}
```

## Eval iterations

**Run history**

1. Smoke run, `--limit 4` (pkg-01 to pkg-04): 4/4.
2. Full run with `--include-calibration`, first draft of the tool: 18/20 scored (bar 18/20, PASS, floor met). Disagreements: `pkg-05` and `pkg-19` (gold `accept`, graded `reject`), and the unscored `calib-01`, all on "Test evidence is decisive". The check required pasted output for every scenario and the repo's own checks even where the plan names none, so evidence that reported a control outcome or secondary scenario in a sentence, or that had no suite to run, failed.
3. `--only pkg-05,pkg-19,calib-01,calib-04,pkg-04,pkg-07,pkg-10,pkg-14,pkg-13 --include-calibration`, after rewriting that check: 7/7 scored, and the calibration trap `calib-04` still rejected, with `pkg-04`, `pkg-07`, `pkg-10`, `pkg-14` (the not-tested rejects) still rejected.
4. Final confirming full run, `--save-run eval-run.txt` (the file committed alongside this write-up): 20/20 scored items, bar (18/20) PASS.

**Package analysis**

`pkg-19` (`mikefarah/yq#2819`). My tool's final decision: **accept**. Gold label: **accept**
("terse but complete: one emitter-state fix mirroring the double-quote writer, byte-identical
round-trip shown with diff, regression scenario added"). On the first full run my rubric
rejected it on "Test evidence is decisive": the plan's test plan names the primary repro plus
"the two controls unchanged", and the evidence shows the primary repro before and after with a
byte-identical `diff` but reports the controls in one sentence ("Controls ... unchanged"), not
as pasted output. My first pass condition demanded pasted output for every scenario, so a
correct, honest PR failed on how it formatted a control. I rewrote the condition so the
primary repro needs a pasted before and after, while other scenarios the plan names need only
be reported with their outcome; silence about a named scenario still fails, which is what keeps
`calib-04` (evidence silent on the `--exec` abort its plan names) rejected.

**Check rationale**

From `tools/pr-precheck/rubric.md`, the "Test evidence is decisive" check's pass condition, as
it reads now:

> Pass if (a) the plan's primary repro (the scenario that fails today) is shown before and
> after with pasted output or a pasted observable value (output, exit code, count) on the
> path the fix changes, and the expected-after is stated; (b) every other scenario the
> plan's test plan names (a control, a second failure mode) is at least reported with its
> outcome, in pasted output or a stated result; and (c) if the plan's test plan or the
> repo's contributing docs name a test suite or check command, its outcome is stated. A
> failing or skipped check disclosed with its reason passes. Fail if the evidence is an
> assertion ("tested locally", "tests pass", "verified working") with no observable
> result; shows only a control or the unchanged path in place of the primary repro; never
> mentions a scenario the plan's test plan names (silence, not a stated "not run"); or
> omits the suite or check the plan or docs name.

It reads this way because the eval set separates two things my first draft treated as one:
evidence that is missing (`pkg-04`, `pkg-10`: "tested locally", "verified working";
`pkg-07`, `pkg-14`: only a control or the unchanged path; `calib-04`: a named failure mode never
mentioned) and evidence that is merely terse (`pkg-19`, `pkg-05`, `calib-01`). Clauses (a) and
(b) put the burden of proof on the primary repro and on not staying silent about a named
scenario, and clause (c) ties the repo's checks to what the plan or docs actually name, so a
repo that names no suite (`calib-01`) is not failed for lacking one. The disclosed-shortfall
sentence is the honest-outcome rule: a failing or skipped check reported with its reason passes.

**Trade-offs**

Clause (b) lets a secondary scenario pass on a stated result alone, so a PR could write
"controls unchanged" without having run them and my check would accept it; only the primary
repro is held to pasted output. I accept that gap because tightening it back to "paste
everything" is exactly what failed `pkg-05`, `pkg-19` and `calib-01`. I re-ran
`--only pkg-04,pkg-07,pkg-10,pkg-14,calib-04` as canaries after loosening, and all five still
rejected, so the loosening did not let any of the not-tested packages through; the invented-controls
case is one this eval set does not contain, so I cannot show the check catches it.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
