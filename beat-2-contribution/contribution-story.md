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
**Pull Request:** https://github.com/identArk/identark/pull/11

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

**Unit 7: Installed the ship-gate and wrote a manifest that runs on Windows**

The ship-gate ran my three checks (ruff, mypy, unit tests) and then my pr-precheck on the branch, and it passed on the second run with all six checks. It stopped the first time because my manifest used `.venv/Scripts/python`, which cmd.exe does not understand, and the hook as installed called `python3`, which is a Store stub on this machine; I fixed both. The two Windows path tests that fail on `main` went into the manifest's excluded table with a date, so the gate did not hide them or fail on them. The pass let the PR through within the 30-minute window.

**Unit 8: Installed the PR monitor and wrote a playbook from a repo with no outside-PR history**

I installed the PR monitor and wrote my four-section playbook, but identark's merged PRs were all the maintainer's own, so I had no outside-contributor wait time to base my staleness number on; I used the issue's "reply within 48 hours" promise plus margin, a 5-day nudge threshold, and said that in the playbook. The monitor read PR #11 (no comments, no reviews, no checks, under a day old) and drafted a wait note: no nudge until 2026-11-15, one ask only, no "bumping". I did not post anything, because the playbook rule says wait, and I would not have trusted a draft nudge on an empty thread anyway. What fell short: with nothing on the PR the monitor had little to do, so I cannot say how it handles a real review; that is untested.

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

identark's `GeminiGateway` talks to Google's Gemini models. Its default model, `gemini-1.5-pro`, has been shut down by Google, and the library's price tables (used to estimate cost and enforce spending caps) only list retired models. Any current model is priced with a cheap fallback, so costs are undercounted.

**Expected Behavior**

`GeminiGateway(api_key=...)` with no `model` should work on its first call, the price tables should list current models, and an unknown model should be estimated conservatively (high), so a cost cap cannot be overspent.

**Current Behavior**

On `main` the first call with the default model fails with `404 models/gemini-1.5-pro is not found`. An unlisted Pro-class model is estimated at 0.375 per 1M input plus 1M output tokens, the Flash rate, instead of the Pro-class rate.

**Affected Components**

`identark/integrations/gemini.py` (default `model`, `_GEMINI_PRICING`, `_estimate_gemini_cost`), `identark/pricing.py` (`_BUNDLED_PRICING`), `tests/unit/test_integrations_gemini.py`, `examples/`, `docs/adapters/gemini.md`, `CHANGELOG.md`.

**Toolkit**

- Tool: Claude Code, reading the issue and the code, plus `CLAUDE.local.md` (my repo memory file) holding the file map.
- What it produced: A file-and-line map of where the retired names live, which matched the issue, and a list of the other places (docs, examples) the issue did not name.
- Difficulties: The issue was precise, so little fought me; the one gap was that I had to check the code, not trust the issue, for the docs file.
- What changed: Added `docs/adapters/gemini.md` to the plan and disclosed it in the PR.

---

## Reproduction Process

**Environment Setup**

Windows 11, Git Bash, Python 3.12.10. I forked identArk/identark, cloned my fork, and worked on `main` at c974f1a (release 1.3.0). Setup: `python -m venv .venv`, then `.venv/Scripts/python -m pip install -e ".[dev]"`. The first install attempt left the dependencies missing (`No module named pytest`); running the same pip command again installed everything. My first reproduction used no API key; later I generated a free-tier Gemini API key in Google AI Studio and made the live call shown under Reproduction Evidence. The key stayed in an environment variable and in no file or log.

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

Live call on `main`, added once I had a key (the key is redacted from the output):

```text
default model: gemini-1.5-pro
call with default model: FAILED NotFound 404 models/gemini-1.5-pro is not found for API version v1beta, or is not supported for generateContent.
```

- **My findings:** The issue is real on `main`: the default model is the retired `gemini-1.5-pro`, and a Pro-class model is priced at the Flash fallback (0.375 against 6.25 for the same tokens under the old 1.5 Pro rate), so costs are undercounted by about 94%, a lower bound. The live call confirms the other half: with a real key, the first call with the default model returns `404 models/gemini-1.5-pro is not found`. The two failing tests also fail on `main` and do not touch Gemini.

**Toolkit**

- Tool: repro-check, graded with my claim comment and my reproduction report together.
- What it produced: accept on all six checks. It suggested stating that the 2 failures are the same on `main`, which I confirmed (I ran on `main`).
- Difficulties: none met in the grading; the real friction was the incomplete first `pip install`.
- What changed: nothing in the report beyond clarifying that the baseline was `main`.

---

## Solution Approach

**Analysis**

`GeminiGateway.__init__` in `identark/integrations/gemini.py` defaults `model` to the retired `gemini-1.5-pro`. `_GEMINI_PRICING` in the same file and `_BUNDLED_PRICING` in `identark/pricing.py` list only Gemini 1.0, 1.5 and 2.0-exp models. `_estimate_gemini_cost` falls back to `gemini-1.5-flash`, the cheapest tier, for any model not in the table, so current models are underestimated and a cost cap can be overspent.

**Proposed Solution**

Default to `gemini-3.6-flash` (verified with a real call), replace the retired price entries in both tables with current Flash, Flash-Lite and Pro entries at Google's paid-tier standard text prices (checked 2026-11-10), and make the unknown-model fallback the most expensive known rate. Not changing: the `google-generativeai` to `google-genai` migration (the issue scopes it out), any gateway logic, or the TypeScript SDK (identark-js#24).

**Implementation Plan**

1. `gemini.py`: add `DEFAULT_MODEL`, replace `_GEMINI_PRICING`, add `_FALLBACK_MODEL` (the most expensive entry), use it in `_estimate_gemini_cost`, fix docstring model names.
2. `pricing.py`: replace the three Gemini entries with the same current models and prices.
3. Tests: current names, loosen the "very cheap" bound, add a test that the default model is priced and a test that the fallback is the most expensive rate.
4. Examples (3 files) and `docs/adapters/gemini.md`: current names and the price table.
5. `CHANGELOG.md`: one line under `[Unreleased]`.

Deviations: none from the plan; `docs/adapters/gemini.md` was in plan step 4 from the start, though the issue's file list did not name it, and the PR says so.

**Toolkit**

- Tool: plan-check, grading `plan.md` and a draft plan comment (the comment stays unposted, as the assignment says).
- What it produced: Reject on the first run, accept on the second.
- Difficulties: It failed "confidence matches the evidence": my comment said "I confirmed... on a real key" with no output next to it, and the 2027-01-01 price doubling was only in the comment, not in the plan's risks.
- What changed: Pasted the 404 output into the comment, added the AI-use line, and added a Risk section to plan.md with the price doubling and the unverified Pro price.

---

## Testing Strategy

Primary repro, re-run on my branch with a real Gemini key: `GeminiGateway(api_key=...)` with no `model`, first call. Before (on `main`): `NotFound 404 models/gemini-1.5-pro is not found for API version v1beta`. After: `OK model= gemini-3.6-flash cost_usd= 6e-06`.

Second scenario, `_estimate_gemini_cost` for 1M input plus 1M output tokens on an unlisted model: before 0.375 (Flash fallback, with a warning), after 14.0 (the most expensive known rate, `gemini-3.1-pro-preview`, with a warning naming it).

Repo checks from CONTRIBUTING.md: `ruff check identark/ tests/` passes; `mypy identark/` reports no issues; `pytest tests/unit/` gives 213 passed, 3 skipped, 2 failed. The two failures are Windows path tests (`test_gateways.py`) that also fail on `main` and do not touch Gemini. Two new tests pin the default model to a price entry and the fallback to the most expensive rate. Not shown: a call to `gemini-3.1-pro-preview`, which returned 429 on my free-tier key.

**Toolkit**

- Tool: The ship-gate manifest (the repo's own checks) and `test_evidence.md`, plus a real Gemini API key for the live calls.
- What it produced: A manifest with ruff, mypy and pytest, and an evidence file with before and after output.
- Difficulties: `.venv/Scripts/python` worked in my shell but the gate runs commands through cmd.exe, where it failed ("is not recognized"); the two Windows tests also fail on `main`, so the pytest row needed a `--deselect` and a dated entry in the excluded table.
- What changed: Rewrote the manifest commands with backslashes, deselected the two tests with a dated reason, and kept the live key out of every file and log.

---

## Implementation Notes

**2026-11-10**

Claimed #10 and posted a claim comment (first step: run the tests on `main` and the no-model call). Set up a venv on Windows; the first `pip install` left the dependencies missing, and running it again fixed that. Reproduced on `main`: default `gemini-1.5-pro`, a table of retired models, an unknown model priced at the Flash rate.

**2026-11-10**

Wrote `CLAUDE.local.md`, then went to Google for current model names and prices. A subagent fetched the pricing page, but the page was truncated and part of its report was a summary, so I did not trust it; I saved the full pricing page text from AI Studio and used only the numbers I could read in it. Wrong turn: I expected the Google AI Pro plan to let me call the Pro models, but that plan does not carry API quota, so those calls returned 429.

**2026-11-10**

Built the fix in two commits (code, tests and changelog; then docs and examples), both signed off with `git commit -s`. First test run failed `test_cost_estimation` because its "very cheap" bound (0.001) assumed 1.5 Flash prices; I loosened it to 0.01. Decision: use today's prices and note the 2027-01-01 doubling in a comment, instead of the higher future rate. `ruff format --check` flags 18 files on `main` already, so I did not reformat unrelated code.

**Toolkit**

- Tool: `CLAUDE.local.md`, my repo memory file, which Claude Code loads at the start of every session in this clone.
- What it produced: The file map, the repo's commit and test conventions, and my Windows setup notes, so I did not re-explain them each session.
- Difficulties: I wrote it before I knew which prices I would use, so the price decisions are not in it.
- What changed: Nothing yet; I would add the price-source rule (use the page text, not a summary) next time.

---

## Pull Request

**PR Link:** https://github.com/identArk/identark/pull/11

**PR Description:** The PR defaults `GeminiGateway` to `gemini-3.6-flash`, replaces the retired entries in `_GEMINI_PRICING` and `_BUNDLED_PRICING` with five current Flash, Flash-Lite and Pro models, prices unknown models at the most expensive known rate, and updates tests, docs, examples and the changelog (8 files, +86/-54, two signed-off commits). It carries the before and after output, the repo checks with the two failing Windows tests disclosed, and the open edges: the Pro price is from the pricing page and was not call-verified, the Flash prices double on 2027-01-01, and the google-genai migration is out of scope. It ends with a plain sentence that I used Claude Code and ran every command myself.

**Maintainer Feedback:**

- 2026-11-10: none yet. The repo's CI had reported no checks on the PR when I opened it.
- 2026-11-10: my PR monitor read the PR and drafted a wait note; under my playbook (5 quiet days) the first possible nudge is 2026-11-15, so I sent nothing.

**Status:** Awaiting review

**Toolkit**

- Tool: The ship-gate (my manifest checks, then my pr-precheck on the branch, plan, evidence and description).
- What it produced: The first run stopped on a repo check, the second passed: all three repo checks and all six pr-precheck checks, with a note that the "signed off" claim needed a `git log` check and the default-model constant sat under the pricing comment.
- Difficulties: The gate runs commands in cmd.exe, so my forward-slash venv path failed on the first run, and the hook installed with `python3`, which is a Windows Store stub here.
- What changed: Fixed the manifest paths and changed the hook to `python`; I checked the sign-off line with `git log` (both commits have Signed-off-by).

---

## Learnings and Reflections

**Technical Skills Gained**

I learned how a gateway library estimates LLM cost (a price table plus a fallback) and why the fallback must err high, how to check a model name against a real API (`list_models` and a one-token call), and how to read a retired-model 404. I also learned to read a repo's contributing rules, run its checks in a Windows venv, and sign off commits with `git commit -s`.

**Challenges Overcome**

The hardest part was the data: Google's pricing page was truncated for the subagent that fetched it and partly summarized, so I stopped, saved the page text myself, and used only numbers I could read. Pro models returned 429 on a free-tier key, so I disclosed the Pro price as not call-verified instead of claiming it. My first scan's top pick, neura-robot#13, was already fixed on `main`, which I only caught by reading the code before claiming, and the ship-gate's cmd.exe path and `python3` hook problems on Windows were fixed after they stopped the first run.

**What I'd Do Differently Next Time**

I would check an issue's default branch for the fix before grading it, which my scout now does. I would pick the price source and write it into my repo memory file before building, and ask the maintainer the open Pro-model question in the claim thread instead of in a PR note. I would also look at how outside PRs were handled in a repo before claiming, since identark had none to learn from.

---

## Resources Used

- identArk/identark#10, the issue itself: it named the files, lines and the fix shape, and it is what the reproduction and plan followed. https://github.com/identArk/identark/issues/10
- identark's CONTRIBUTING.md and CLAUDE.md: the checks to run, the commit format, and the sign-off rule.
- Google's Gemini API pricing page (saved as text from AI Studio): the only source I used for model names and prices. https://ai.google.dev/gemini-api/docs/pricing
- The Gemini API `list_models` call and a one-token request on my own key: how I confirmed the default 404 and that `gemini-3.6-flash` works.
- The course's unit 6 to 8 starters (voice guard, ship-gate, PR monitor): the install steps I followed, and my own scan notes in `scan-notes.md`.
