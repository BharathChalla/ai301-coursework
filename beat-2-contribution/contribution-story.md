# Contribution: gemini: default model and pricing table still point at retired Gemini 1.5 models

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Bharath Challa
**GitHub Username:** BharathChalla
**Issue:** https://github.com/identArk/identark/issues/10
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Built the issue-scout and wrote a Python bug-fix search strategy**

I turned my issue-select skill into issue-scout, with my fit profile (Python backend/API and RAG work, small well-diagnosed bug fixes) and three `gh search issues` queries ranked by no assignee, recent update, few comments and fewest comments first. My first scan graded five issues, two accepted and three rejected, because each of those three already had an open linked PR, which the ranking filters cannot see in search results. I added the `-linked:pr` qualifier to all three queries and checked in the terminal that it drops those issues; the rubric needed no change because its rejections were correct.

**Unit 6: Installed the voice guard, patched the scope check, wrote a repo memory file**

I wrote an Upstream section for my voice guide (claims, questions, pings, each with a wrong/right pair), made it the one guide all my skills read, and installed the voice-guard hook, which stopped `gh issue comment --help` as expected. The hook edit to my global settings was blocked when I tried it, so I added it myself after that; I also had to use `python` because `python3` here is a Windows Store stub. My claim passed the guard and repro-check before I posted it, and `CLAUDE.local.md` now holds the repo's conventions, the files my issue touches and my Windows setup, kept out of the PR with `.git/info/exclude`. Nothing in the guard misfired on my claim, which I know from its log line showing a pass before the post.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I wrote search queries from my fit profile (Python backend/API work, small well-diagnosed bug fixes, some LLM-pipeline interest) and ran my scout, issue-scout, twice. The first scan's top pick, neura-robot#13, turned out to be already fixed on `main` (the repo is a one-way mirror, so the issue stayed open), which I found by reading the code before claiming. I added a check for that to my scan and re-scanned: all five graded issues were accepted, and identArk/identark#10 ranked first. I also looked at the Issue Selection site, but its Python bug-tagged list was mostly C++ and build work in pytorch repos, so I stayed with the scan's pick.

Issue #10 says the Gemini gateway still defaults to `gemini-1.5-pro` and both pricing tables only know retired models, so a current model is estimated at the cheapest Flash rate. My scout accepted it on every check (re-graded after my scope patch) with a difficulty note of **straightforward**: the issue names the files and lines, the tests need no network, and the repo has no AI restriction (it ships a `CLAUDE.md` for AI agents and asks for `git commit -s`). It fits me because it is a precisely diagnosed Python bug in an LLM gateway, and I hope to learn how a real project keeps pricing data honest. Claim comment: https://github.com/identArk/identark/issues/10#issuecomment-6775614131

On the scout itself: it fell short in two places on real searches. Its ranking filters cannot see linked PRs, so three of five graded slots in the first scan were wasted; I added `-linked:pr` to the queries. And it accepted an issue whose fix was already on `main`, so I added a default-branch check. Zero-comment ordering also favours brand-new issues, which I still handle by hand.

**Toolkit**

- Tool: issue-scout (my scout: my search strategy, my rubric). I ran two scans and graded the top five each time, then graded #10 directly after patching the rubric.
- What it produced: scan 1 accepted 2 of 5 and rejected 3 (open linked PRs); scan 2 accepted 5 of 5, with identArk/identark#10 first; the direct grade of #10 was accept, difficulty straightforward.
- Difficulties: ranking could not see linked PRs, and the scout accepted neura-robot#13 although its fix was already on `main`. The old scope check was also too strict for real issues.
- What changed: added `-linked:pr` to all three queries, added a "fix already on default branch" check to my scan, and replaced the scope check with the scope-feasible row plus a difficulty note (dated lines in scan-notes.md).

---

## Understanding the Issue

**Problem Description**

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Reproduction Process

**Environment Setup**

Windows 11, Git Bash, Python 3.12.10. I forked identArk/identark, cloned my fork, and worked on `main` at c974f1a (release 1.3.0). Setup: `python -m venv .venv`, then `.venv/Scripts/python -m pip install -e ".[dev]"`. The first install attempt left the dependencies missing (`No module named pytest`); running the same pip command again installed everything. I have no Gemini API key, so I did not make a live API call.

**Steps to Reproduce**

1. Run `.venv/Scripts/python -m pytest tests/unit/ -q` on `main`.
2. In Python, read the default `model` of `GeminiGateway.__init__` and list the keys of `_GEMINI_PRICING`.
3. Call `_estimate_gemini_cost(model, 1_000_000, 1_000_000)` for `gemini-1.5-pro`, `gemini-2.5-pro` and `gemini-3-pro-preview`.
4. Observed: the default is still `gemini-1.5-pro`; every pricing key is a 1.x, `gemini-pro` or 2.0 model; the two newer models get a flash-rate fallback.

**Reproduction Evidence**

- **Commit showing reproduction:** none (nothing was changed; the evidence is output from `main`)
- **Screenshots/logs:**

```text
default model: gemini-1.5-pro
old entries only: True
gemini-1.5-pro       cost for 1M in + 1M out = 6.25
gemini-2.5-pro       cost for 1M in + 1M out = 0.375   WARNING Unknown Gemini model gemini-2.5-pro, using flash pricing
gemini-3-pro-preview cost for 1M in + 1M out = 0.375   WARNING Unknown Gemini model gemini-3-pro-preview, using flash pricing
pytest tests/unit/: 211 passed, 3 skipped, 2 failed (Windows path tests in test_gateways.py, unrelated)
```

- **My findings:** The issue is real on `main`: the default model is the retired `gemini-1.5-pro`, and a Pro-class model is priced at the Flash fallback (0.375 against 6.25 for the same tokens under the old 1.5 Pro rate), so costs are undercounted by about 94%, a lower bound. What I did not show is that the first API call with the default model fails; that needs a key, and I rely on the issue's citation of Google's deprecations page. The two failing tests also fail on `main` and do not touch Gemini.

**Toolkit**

- Tool: repro-check, graded with my claim comment and my reproduction report together.
- What it produced: accept on all six checks. It suggested stating that the 2 failures are the same on `main`, which I confirmed (I ran on `main`).
- Difficulties: none met in the grading; the real friction was the incomplete first `pip install`.
- What changed: nothing in the report beyond clarifying that the baseline was `main`.

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
