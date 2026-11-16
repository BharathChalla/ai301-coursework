# Fixing a retired default model and stale pricing in identArk's Gemini integration

## Overview

identArk is an open source Python library that sits between an application and AI models and estimates what each call will cost. Its Gemini integration defaulted to `gemini-1.5-pro`, a model Google has shut down, and its price tables only listed retired models, so a call with no model name failed and current models were priced too low. I claimed the issue, reproduced the failure with a real API key, and opened [pull request #11](https://github.com/identArk/identark/pull/11) with a fix. Today the PR is open and waiting for review, the maintainer has assigned me the issue, and I have pushed a follow-up commit he asked for. I used Claude Code to help read the code and draft parts of the work; I ran every command myself.

## What happened

I claimed [issue #10](https://github.com/identArk/identark/issues/10) on 2026-11-10 and started by running the code as it was on `main`. Calling `GeminiGateway(api_key=...)` with no model name, which is what a new user would do, failed:

```
default model: gemini-1.5-pro
call with default model: FAILED NotFound 404 models/gemini-1.5-pro is not found for API version v1beta
```

That confirmed the first half of the issue. The second half was the price tables. A model that was not in the table was priced at the cheap Flash rate, so asking for the cost of 1M input plus 1M output tokens on an unlisted Pro model came back as 0.375 dollars with a warning, which would let a spending cap be overspent.

Both checks were run on 2026-11-10 against my own Gemini key and my own clone, not on sample data. After my change the same call worked, and the unlisted-model estimate rose to 14.0 dollars:

```
GeminiGateway(api_key=...) first call: OK model= gemini-3.6-flash cost_usd= 6e-06
gemini-99-pro   14.0   WARNING Unknown Gemini model gemini-99-pro, using gemini-3.1-pro-preview pricing
```

The fix changed the default model, replaced the retired entries in two price tables with five current models taken from Google's pricing page, and made unknown models fall back to the most expensive known rate instead of the cheapest. The core of the diff in `identark/integrations/gemini.py`:

```
-    "gemini-1.5-pro": {"input": 1.25, "output": 5.00},
+    "gemini-3.8-flash": {"input": 0.75, "output": 3.75},
+_FALLBACK_MODEL = max(_GEMINI_PRICING, key=lambda m: sum(_GEMINI_PRICING[m].values()))
```

It also updated the tests, docs, examples and changelog: 8 files, two signed-off commits at first. Along the way a test failed: `test_cost_estimation` had a "very cheap" bound of 0.001 that assumed the old model's prices, so I loosened it to 0.01 and added two new tests, one that the default model has a price entry and one that the fallback is the most expensive rate. The repo's lint and type checks passed, and the unit tests gave 213 passed and 2 failed; the two failures are Windows path tests that fail on `main` too and have nothing to do with Gemini.

The change I made because of someone else's input came after the PR was open. On 2026-11-14 the maintainer assigned me the issue and wrote that the matching TypeScript fix used `gemini-3.8-flash` as its default and that using the same default here would keep the two libraries in step. My first version used `gemini-3.6-flash`. I changed the default to `gemini-3.8-flash`, which was already in my price table at the same rate, and pushed that as a third signed-off commit (35a5d20) with the docs default marker and changelog line updated. Lint, types and the Gemini tests still pass locally.

Two things have happened because of the work. The maintainer assigned me the issue, which I read as confirmation the fix was wanted. And the project's 14 CI checks (lint, types, tests on Python 3.10 to 3.12, security and CodeQL) all passed on my branch on 2026-11-14. The PR has not been reviewed or merged. CI has not re-run on the follow-up commit, which probably needs a maintainer to approve it.

## Limitations and what's next

Two numbers in the fix are not verified by a real call. The Pro model, `gemini-3.1-pro-preview`, returned a quota error on my free-tier key, so its price comes from Google's pricing page only. I also did not call `gemini-3.8-flash` after switching to it, because I had already deleted my API key, so its price comes from the same page and from the maintainer's note that prices were checked on 11 November. The Flash prices also double on 2027-01-01; the table notes that but does not change.

Next, I would make a short live call to both models with a key that has quota and add the result to the PR, and I would ask the maintainer whether he wants the January price in the table now. The larger migration from the old `google-generativeai` package to `google-genai` was outside this issue and is the natural follow-up.
