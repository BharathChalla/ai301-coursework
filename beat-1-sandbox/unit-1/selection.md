# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62

**Verdict output**

````
Live-mode grading: issue-select on 3 candidates

Scope check: all three URLs are in codepath/pathreview-ai301-fa26-s3, the
scoped Path Review repo. House rule noted (student claim comments don't
block a candidate) — moot here, none of the three have any comments.

Ranked read-out (accepted, fit order):

1. #62 — Health check references `settings.redis_host`, which does not
   exist on Settings — best fit: matches stated backend/API experience
   directly, and carries the most precisely diagnosed root cause of the
   three (exact wrong attribute name, exact file, exact reproduction
   steps), which the fit profile weights above a flashier pick when
   candidates are close.
2. #69 — Output parser crashes on a top-level JSON array fallback — good
   fit: matches the stated interest in RAG/LLM-pipeline practice; slightly
   more moving parts (fallback logic plus an xfail test to unmark) than
   #62.
3. #73 — README and .env.example disagree about which LLM API key to set
   — accepted but ranked last: docs-only, lowest technical fit to the
   stated backend/RAG interests, though it's the lowest-risk warm-up if
   minimizing first-PR risk mattered more than fit.

Rejected: none — all three cleared every required check.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62",
    "checks": [
      {"name": "Maintainer alive", "grade": "pass", "evidence": "most recent commit to main: 2026-09-16 by Aburke225, within 90 days of today (2026-09-21)"},
      {"name": "Repo in use", "grade": "pass", "evidence": "not archived; last push 2026-09-16, within 180 days"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "single-file fix in api/routes/health.py, exact wrong attribute (settings.redis_host vs redis_url) named, exact repro steps given, no maintainer states core-internals difficulty"},
      {"name": "Nobody already on it", "grade": "pass", "evidence": "assignees: none; linked PRs: none; comments: none"},
      {"name": "Contribution policy allows this workflow", "grade": "pass", "evidence": "docs/CONTRIBUTING.md has no AI-use statement; silence passes; recent commits are co-authored with Claude, confirming AI-assisted work is the norm here"},
      {"name": "Has a \"good first issue\" style label", "grade": "pass", "evidence": "labels: api, bug, good first issue, tier-1"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/69",
    "checks": [
      {"name": "Maintainer alive", "grade": "pass", "evidence": "most recent commit to main: 2026-09-16 by Aburke225, within 90 days of today"},
      {"name": "Repo in use", "grade": "pass", "evidence": "not archived; last push 2026-09-16, within 180 days"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "one named crash (AttributeError on list.items()) in rag/generator/output_parser.py with a companion xfail test to unmark, no core-internals claim from a maintainer"},
      {"name": "Nobody already on it", "grade": "pass", "evidence": "assignees: none; linked PRs: none; comments: none"},
      {"name": "Contribution policy allows this workflow", "grade": "pass", "evidence": "docs/CONTRIBUTING.md has no AI-use statement; silence passes"},
      {"name": "Has a \"good first issue\" style label", "grade": "pass", "evidence": "labels: bug, good first issue, rag, tier-1"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/73",
    "checks": [
      {"name": "Maintainer alive", "grade": "pass", "evidence": "most recent commit to main: 2026-09-16 by Aburke225, within 90 days of today"},
      {"name": "Repo in use", "grade": "pass", "evidence": "not archived; last push 2026-09-16, within 180 days"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "single cohesive fix (align README.md and .env.example on the LLM_PROVIDER/OPENROUTER_API_KEY variables), effort estimate 1-2h, no design debate or core-internals claim"},
      {"name": "Nobody already on it", "grade": "pass", "evidence": "assignees: none; linked PRs: none; comments: none"},
      {"name": "Contribution policy allows this workflow", "grade": "pass", "evidence": "docs/CONTRIBUTING.md has no AI-use statement; silence passes"},
      {"name": "Has a \"good first issue\" style label", "grade": "pass", "evidence": "labels: bug, docs, good first issue, tier-1"}
    ],
    "verdict": "accept"
  }
]
```
````

---

## Eval iterations

**Run history**

1. Smoke run, `--limit 3` (issue-01, issue-02, issue-03): 2/3. `issue-01` (gold `accept`) came back `reject`: the rubric's scope check read a single cohesive multi-file docs change as an "umbrella issue meant to be split into separate work."
2. `--only issue-01,issue-05,issue-10,issue-15,calib-04 --include-calibration`, after loosening the umbrella wording to require an *explicit* split-into-separate-work statement: 3/4. `issue-15` (gold `reject`) flipped to `accept` on one sample — the claim check landed on `unclear` on one run and `pass` on another, because the bundle only shows 40 of 97 comments and the 2024–2026 gap is genuinely unverifiable; relying on that alone was too fragile.
3. `--only issue-15,issue-09,issue-01`, after adding a scope-check clause that fails an issue with two or more closed/abandoned linked-PR attempts (issue-15 has two; issue-09, gold `accept`, has only one, so it isn't caught by the same clause): 3/3.
4. Full run, 20 issues: 18/20, bar (18/20) PASS, category floor met. Two disagreements: `issue-19` (gold `accept`, graded `reject` — the scope check treated a bug's own listed alternative fixes as an unstated core-internals rework) and `issue-20` (gold `reject`, graded `accept` — a bot-filed feature request with an unresolved "TBD" design decision and no maintainer endorsement slipped through as bounded).
5. `--only issue-19,issue-20,issue-01,issue-15,issue-09,issue-05,issue-10`, after (a) restricting the core-internals fail condition to an *explicit maintainer/opener statement* rather than my own difficulty estimate, and (b) adding a fail clause for an unendorsed feature request that leaves a design decision marked "TBD"/"if needed": 7/7.
6. Full run with `--include-calibration`: 20/20 scored items, bar (18/20) PASS, category floor met (all four categories matched), all 4 calibration issues also correct.
7. Final confirming full run, `--save-run eval-run.txt` (the file committed alongside this write-up): 20/20 scored items, bar (18/20) PASS.

**Issue analysis**

`issue-19` (`zxcalc/zxlive#517`). My rubric's final decision: **accept**. Gold label: **accept** ("maintainer-diagnosed performance bug with named causes, unclaimed"). The issue lists two diagnosed root causes for a UI freeze plus three *additional suggestions* for how to fix them (multiprocessing, category-aware matching, threaded rewrite application). On the full run in step 4 above, my rubric's scope check misread those suggested approaches as proof the fix "requires rework of the matching engine's concurrency model" and failed it as touching core internals — but nobody in the issue said that; I inferred it myself from the shape of the suggestions. Once I tightened the rubric's core-internals clause to require an *explicit statement in the text* rather than my own estimate of difficulty, the check correctly read this as one named bug (the freeze) with several optional ways to fix it, filed by a collaborator, unclaimed, in an active repo — a bounded first issue, matching gold.

**Check rationale**

From `tools/issue-select/rubric.md`, the "Nobody already on it" check's pass condition, quoted as currently written:

> Fail if any of: the assignees list is non-empty; any linked PR is listed as open; the thread has a claim comment ("I'll take this", "working on this", "can I work on this") posted within 12 months of the capture/live date with no sign of abandonment. A claim older than 12 months, or one accompanied by a closed/abandoned linked PR or a bot's stale-issue mark with no further activity from the claimant, does not fail this check.

The 12-month cutoff exists because "someone claimed it" is not the same fact as "someone is claiming it now": `issue-09` (`conda/conda#7617`, gold `accept`) has a claim comment from 2022, acknowledged by a maintainer, but the bot marked it stale within a year and nobody followed up before the 2026 capture date. Without an explicit staleness rule, that four-year-old comment would sink an issue the maintainer has effectively re-opened for a new contributor.

**Trade-offs**

The 12-month threshold is a real trade-off, not a free win. I re-ran `--only issue-09` before and after adding the clause: before, a version of the check with no staleness carve-out failed `issue-09` on the claim comment alone (disagreeing with gold); after, it passes. What it gives up: a claim posted 11 months ago with zero follow-up activity since — arguably just as abandoned as `issue-09`'s — still fails the check today, because the rule only looks at the comment's age, not at whether the claimant went quiet. I accept that miss because the eval set's `claimed`-category rejects (`issue-03`, `issue-08`, `issue-13`, `issue-18`) all also carry an open linked PR or a set assignee, so the looser 11-month case never actually surfaces in this set — but it's a real gap in the check as written, not a case I've verified is safe in general.

---

## Selection rationale

**Selection rationale**

1. **Fit to interests and time available.** I told the tool I have real Python backend/API experience and want more practice on the RAG/LLM-pipeline side, with no strong time constraint beyond wanting the safer bet if candidates were close. `#62` is a single-file fix in `api/routes/health.py` (a wrong `settings.redis_host` attribute instead of `redis_url`) with an exact reproduction (`GET /health` while Redis is running returns a false-negative 503) — it plays directly to the backend experience and is small enough to fit comfortably around other coursework.
2. **What the verdict identified correctly, and what I weighed that the rubric could not.** The rubric correctly established that all three candidates were live, unclaimed, in-scope, and policy-clear — none of that changes verdict-to-verdict between them. What it can't do is tell me which one *I* should take: `#62`, `#69`, and `#73` all cleared every required check, so the choice came down to my own fit (backend vs. RAG vs. docs) and my own read that `#62`'s root cause was the most precisely diagnosed of the three (exact wrong attribute, exact file, exact repro), which matters to me more than which topic area is flashiest.
3. **The anticipated difficulty in claiming it.** Low. The issue has no assignee and no linked PR, `docs/CONTRIBUTING.md` only asks that I comment on the issue to signal I'm working on it, and the Path Review house rule means even a stray classmate claim comment wouldn't block me. The main risk is a classmate picking it up between now and when I claim it in Unit 2 — mitigated by claiming promptly rather than by anything the rubric can verify ahead of time.
