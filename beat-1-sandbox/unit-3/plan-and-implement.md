# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

---

## Posted upstream

**GitHub username**

BharathChalla

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62#issuecomment-6031034638

```
Plan, building on my reproduction above: `Settings` only defines `redis_url`, so I swapped the probe's client construction in `api/routes/health.py` to `redis.from_url(settings.redis_url, decode_responses=True)` instead of the nonexistent `redis_host`/`redis_port` fields. One line, plus a regression test covering the healthy, unreachable, and from-url-argument cases. Not touching `core/config.py`: `redis_url` already fully specifies the connection, so adding parallel host/port fields would just duplicate it. Test plan: re-run my repro steps before/after and confirm Redis reports healthy while an actual outage still reports unhealthy. The change is built and tested locally on my fork's branch `fix/62-health-check-redis-url`; I'll open the PR from there.
```

---

## Your branch

**Branch**

`fix/62-health-check-redis-url`

Pushed to my fork, https://github.com/BharathChalla/pathreview-ai301-fa26-s3/tree/fix/62-health-check-redis-url
(commit `906ca56`, on top of `2f4e82f`). Diff: one line changed in
`api/routes/health.py`, one new file `tests/unit/test_health.py` (68
insertions, 6 deletions total).

**Evidence**

Before (Unit 2 reproduction, unfixed code, `2f4e82f`):

````
$ docker compose exec redis redis-cli ping
PONG
$ curl -s -w "\nHTTP status: %{http_code}\n" http://127.0.0.1:8000/health
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-09-23T08:09:19.022236"}}
HTTP status: 503

server log:
2026-09-23 03:09:19 [error] redis_health_check_failed error="'Settings' object has no attribute 'redis_host'" request_id=...
````

After (this branch, `fix/62-health-check-redis-url`, commit `906ca56`):

````
$ docker compose exec redis redis-cli ping
PONG
$ curl -s -w "\nHTTP status: %{http_code}\n" http://127.0.0.1:8000/health
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"healthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-09-27T07:35:48.127658"}}
HTTP status: 503

server log:
2026-09-27 02:35:48 [error] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')" request_id=...
2026-09-27 02:35:48 [debug] redis_health_check_passed      request_id=...
2026-09-27 02:35:48 [debug] vector_db_health_check_passed  request_id=...
````

`redis` flips from `"unhealthy"` to `"healthy"`, and the
`redis_health_check_failed`/`AttributeError` log line is gone,
replaced by `redis_health_check_passed`. The overall status stays 503
because of the unrelated Postgres bug (out of scope, called out in
`plan.md`), which is expected and unaffected by this change.

Outage recheck (Redis genuinely stopped, to confirm the fix still
detects a real failure rather than always reporting healthy):

```
$ docker compose stop redis
$ curl -s -w "\nHTTP status: %{http_code}\n" http://127.0.0.1:8000/health
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-09-27T07:35:57.695796"}}
HTTP status: 503
```

New unit tests, run locally:

```
$ .venv/Scripts/pytest tests/unit/test_health.py -v -m unit
tests/unit/test_health.py::TestHealthCheckRedisProbe::test_redis_healthy_when_reachable PASSED
tests/unit/test_health.py::TestHealthCheckRedisProbe::test_redis_client_built_from_redis_url PASSED
tests/unit/test_health.py::TestHealthCheckRedisProbe::test_redis_unhealthy_when_unreachable PASSED
3 passed, 4 warnings in 1.74s
```

## Eval iterations

**Run history**

1. Smoke run, `--limit 4` (pkg-01 through pkg-04): 4/4, including `pkg-04` (the thread-convention trap where a well-formed docs-only plan ignores a maintainer's explicit code pointer and test request).
2. Full run, 20 packages: 18/20, bar (18/20) PASS, category floor met. Two disagreements, both in `clear-accept`: `pkg-13` and `pkg-14` (gold `accept`, graded `reject`) — the Executability check's fail wording ("not sure which layer") pattern-matched on both packages' single honestly-flagged implementation detail (an exact clamp site, an exact function name) as if it were an unresolved core decision, when both plans actually committed to a fully concrete approach and named the detail only in their risk section.
3. `--only pkg-13,pkg-14,pkg-10,pkg-17,pkg-18,pkg-02,pkg-09`, after rewriting the Executability check to distinguish an honestly-flagged exact-implementation detail (which does not fail) from the core approach itself being left undecided (which does): 7/7, including the genuinely unbuildable packages (`pkg-10`, `pkg-17`, `pkg-18`) and other clear-accepts (`pkg-02`, `pkg-09`).
4. Full run with `--include-calibration`: 20/20 scored items, bar (18/20) PASS, category floor met in all five categories, all 4 calibration packages also correct (including `calib-03`'s operator-swap trap and `calib-04`'s decisive-test-plan borderline).
5. Final confirming full run, `--save-run eval-run.txt` (the file committed alongside this write-up): 20/20 scored items, bar (18/20) PASS.

**Package analysis**

`pkg-13` (`microsoft/terminal#20370`). My rubric's final decision:
**accept**. Gold label: **accept** ("bounded to the erase-scrollback
branch, grounded in the repro and the maintainer's invalidation-bug
confirmation, splits out the mouse symptom, decisive script-based test
with a reference terminal"). The plan's risk section says: "I have not
yet verified which layer clamps the viewport, so the exact fix site
within the branch may move one level during implementation." On the
full run in step 2 above, my Executability check's wording ("not sure
which layer") matched that sentence and failed the package, even though
the plan's diagnosis and approach are fully committed (re-anchor the
viewport, invalidate the renderer, in the named `EraseInDisplay`
scrollback branch) — only one exact clamp site, not the approach
itself, was left to confirm, and it's flagged as a risk rather than
posed as an open question in the approach. Once I rewrote the check to
require the *core decision* (not an exact-site detail already
committed to an area) to be unresolved before failing, `pkg-13`
correctly passed, matching gold, without letting genuinely unbuildable
plans like `pkg-17` (no diagnosis, no chosen layer, no files) back in.

**Check rationale**

From `tools/plan-check/rubric.md`, the "A stranger could start
executing it" check's pass condition, quoted as currently written:

> Pass if the diagnosis and approach commit to a specific mechanism,
> subsystem, or area (a named file, module, or code path) and an
> ordered sequence of what will change there, sufficient for someone
> who has never seen the plan to start work without asking the author
> anything. A single exact-implementation detail honestly flagged as
> unconfirmed (an exact function name, an exact line, the precise layer
> within an already-named area) does not fail this check when it
> appears as a stated risk/unknown alongside an otherwise fully
> committed approach — that is honesty, not indecision. Fail if the
> diagnosis or approach section itself poses the core decision as an
> open question — which subsystem or approach to use is not yet chosen
> ("investigate the input stack, not sure which layer is responsible",
> "upstream or vendored, whichever is easier", "maybe also check other
> linters while at it" as a stand-in for scope), or no files/locations
> are named at all.

It reads this way because my first draft only listed hedge phrases
("not sure which layer", "whichever is easier") as fail triggers
without saying *where* they had to appear to count. `pkg-13` and
`pkg-14` both contain almost the same words ("not yet verified which
layer...", "exact functions to be pinned...") but in their risk
sections, describing one remaining implementation detail against an
already-fully-committed approach — not, like `pkg-17`, a plan whose
entire diagnosis is "investigate... not sure which layer is
responsible" with no chosen approach at all. I rewrote the check to
key on *where the decision lives* (approach vs. risk section) rather
than on the hedge language alone, because the eval set specifically
contrasts an honestly-flagged detail against a genuinely undecided
core approach, and phrase-matching alone could not tell them apart.

**Trade-offs**

The rewritten check still draws a line, and I accept it will
occasionally sit wrong on a case this set doesn't contain: a plan whose
risk section names something that is, in fact, load-bearing to whether
the approach even works (not a minor site detail but a real open
question about feasibility), phrased with the same honest hedging
language as `pkg-13`'s. My check would pass that plan on the strength
of its risk-section placement alone, when a stricter reading might say
the "committed" approach was never actually validated. I re-ran
`--only pkg-13,pkg-14,pkg-10,pkg-17,pkg-18` specifically as canaries
after the rewrite to confirm the four packages this distinction
actually separates in the eval set still land correctly (they do), but
a plan that hides a real feasibility gap inside a well-worded risk
paragraph is a case this check cannot see from the wording alone.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your
skill's files in `tools/plan-check/`.
