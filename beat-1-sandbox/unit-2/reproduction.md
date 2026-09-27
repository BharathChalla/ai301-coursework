# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

---

## Your identity upstream

**GitHub username**

BharathChalla

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62#issuecomment-5853690635

```
Hi, I'd like to take this one as a first contribution. I'm going to check
whether the health check's Redis probe is reading a `Settings` attribute
that doesn't actually exist there, and report back with a reproduction.
```

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62#issuecomment-5853692825

````
**Environment:** `codepath/pathreview-ai301-fa26-s3` @ `2f4e82f` (2026-09-16),
Windows 11 (10.0.26200), Git Bash (MSYS2), Python 3.12.10, Docker 29.8.0 /
Compose v5.5.1, fastapi 0.141.1, uvicorn 0.53.0, redis-py 8.1.0,
pydantic-settings 2.15.0. Backing services from the repo's own
`docker-compose.yml`: `postgres:16-alpine` and `redis:7-alpine`, started
with `docker compose up -d`.

**Steps:**

1. `cp .env.example .env` (defaults, no edits — `REDIS_URL=redis://localhost:6379/0`)
2. `docker compose up -d` — `db` and `redis` both report `healthy` in `docker compose ps` within ~15s
3. Confirmed Redis is actually reachable, independent of the app: `docker compose exec redis redis-cli ping` -> `PONG`
4. `python -m venv .venv && .venv/Scripts/pip install -e ".[dev]"`
5. `.venv/Scripts/alembic upgrade head` — migrations apply cleanly against the `db` container
6. `.venv/Scripts/python -m uvicorn api.main:app --host 127.0.0.1 --port 8000`
7. `curl -s -w "\nHTTP status: %{http_code}\n" http://127.0.0.1:8000/health`

**Actual:**

```
$ curl -s -w "\nHTTP status: %{http_code}\n" http://127.0.0.1:8000/health
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-09-23T08:09:19.022236"}}
HTTP status: 503
```

The server's own structured log for that request:

```
2026-09-23 03:09:19 [error] redis_health_check_failed error="'Settings' object has no attribute 'redis_host'" request_id=...
```

**Expected:** since `redis-cli ping` independently confirms Redis is up and
reachable on `localhost:6379` (step 3), `GET /health` should report
`"redis": "healthy"`.

**Root cause, read from the source (`api/routes/health.py`):** the Redis
probe builds its client from `settings.redis_host` and `settings.redis_port` —

```python
r = redis.Redis(
    host=settings.redis_host,
    port=settings.redis_port,
    db=0,
    decode_responses=True,
)
```

— but `core/config.py`'s `Settings` only defines `redis_url`
(`redis://localhost:6379/0`), no `redis_host`/`redis_port` fields. The
attribute access raises `AttributeError`, which the surrounding
`except Exception` catches and reports as `"redis": "unhealthy"`, exactly
as the log line above shows. This matches the issue's description
precisely, including the exact log line it names (`redis_health_check_failed`
+ `AttributeError`).

**Aside, not part of this issue:** the same response also shows
`"postgres": "unhealthy"`, for an unrelated reason — the log shows
`error="Textual SQL expression 'SELECT 1' should be explicitly declared as
text('SELECT 1')"`, a separate SQLAlchemy 2.0 raw-SQL issue. Noting it here
only so it isn't mistaken for part of this bug; it isn't touched by this
report.
````

## Eval iterations

**Run history**

1. Smoke run, `--limit 4` (pkg-01 through pkg-04): 3/4. `pkg-03` (gold `accept`) came back `reject`: the AI-disclosure check's fail condition ("conditions acceptance on AI use while naming a dedicated AI-policy document") fired on ripgrep's `AI_POLICY.md`, even though what that policy actually asks for is human-voiced comments, not a disclosure statement.
2. `--only pkg-01,pkg-03,pkg-05,pkg-07,pkg-20 --include-calibration`, after rewriting the disclosure check's fail condition to require an explicit disclosure-language signal or a stated ban on a specific AI-involvement category (rather than "names a policy doc + conditions on responsibility"): 5/5, including `pkg-20`, the one-item `disclosure` category.
3. Full run, 20 packages: 20/20, bar (18/20) PASS, category floor met on the first try (`clear-accept` 8/8, `disclosure` 1/1, `no-evidence` 4/4, `unfollowable-comms` 3/3, `wrong-target` 4/4).
4. Confirming full run with `--save-run`: 19/20 — `pkg-18` (gold `reject`) flipped to `accept` on this sample. The "Steps" check had no clause for a reproduction that depends on a resource nobody else can obtain (a private company monorepo and an unshared internal config), so a run that read the described steps charitably could pass it.
5. `--only pkg-18,pkg-06,pkg-19,pkg-01,pkg-03,pkg-09,pkg-10`, after adding a fail clause to the "Steps" check for reproductions that depend on a private/unshared resource the report itself says it cannot share: 7/7, including the two honest-cannot-reproduce accepts (`pkg-09`, `pkg-10`) and the other `unfollowable-comms` rejects, confirming the tightened wording didn't sink anything it shouldn't.
6. Final confirming full run, `--save-run eval-run.txt` (the file saved alongside this write-up): 20/20 scored items, bar (18/20) PASS, category floor met in all five categories.

**Package analysis**

`pkg-18` (`golangci/golangci-lint#4242`). My rubric's final decision:
**reject**. Gold label: **reject** (category `unfollowable-comms`, note:
"reproduction lives entirely in a private monorepo with an unshared
config; a stranger cannot re-run any step"). The candidate report describes
reproducing the `gomodguard` panic in "our company monorepo (private; I
cannot share it or its layout)" using "our internal `.golangci.yml`...
also not shareable." The artifact shown (the panic trace) does look right,
and on the run in step 4 above my rubric read the steps as matching the
issue's shape (go.mod in a subdirectory) and passed them — but a stranger
reading that comment has no repo, no config, and no way to recreate either
one; the steps are complete for the author and useless for anyone else,
which is exactly what "followable" is supposed to rule out. Adding an
explicit fail clause for steps that depend on a resource the report itself
says it cannot share fixed this without touching the checks that pass
`pkg-09`/`pkg-10` (honest cannot-reproduce reports that share everything
they used, just from a different environment than the issue).

**Check rationale**

From `tools/repro-check/rubric.md`, the "AI-assistance disclosure matches
the repo's policy" check's pass condition, quoted as currently written:

> Fail only if the policy text contains one of two explicit signals AND no
> comment in the package discloses that AI assistance was used: (1)
> explicit disclosure language ("must be disclosed", "disclosing", "stating
> the tool used and the extent of the assistance"), or (2) a stated ban on
> a specific category of AI involvement (e.g. "fully AI-generated
> contributions are not accepted") — a ban like this requires the
> maintainer to know the involvement level to enforce it, so it functions
> as a disclosure requirement even without the word "disclose". Pass in
> every other case: the policy is silent on AI; it asks only that
> comments/code be authored or reviewed by a human in their own words (a
> voice/authorship rule, not a ban on any AI-involvement category) and the
> comment reads as first-person and specific; it welcomes AI use
> conditioned on human responsibility without banning any category or
> naming an explicit disclosure ask; or a comment already discloses.
> Naming a dedicated AI-policy document by itself is not a fail signal —
> read what that line actually asks for.

It reads this way because my first draft used a cruder signal — "names a
dedicated `AI_POLICY.md`/`AI_USAGE_POLICY.md` file and conditions
acceptance on AI use" — which is what step 1 above shows failing on
`pkg-03`: ripgrep names `AI_POLICY.md` and does condition on a human being
in the loop, but what it actually asks for is comments "written by humans
in their own words," not a disclosure statement, and its accepted
candidate comment satisfies that by reading as plainly first-person. I
rejected the cruder signal in favor of reading what the named policy
document is actually asking for, because the eval set specifically
contrasts a voice-authorship policy (ripgrep, `pkg-03`) against a
disclosure-or-category-ban policy (p5.js's `pkg-07`, ghostty's `pkg-20`),
and the cruder rule could not tell them apart.

**Trade-offs**

The rewritten check still draws a line, and the line can be wrong in a
case the eval set doesn't contain: a policy that names a dedicated AI
policy document, bans no specific category, and never uses the word
"disclose," but whose linked document (which the check's evidence is only
the summarized repo-facts line, not the full linked file) actually does
require disclosure in its body. My check would pass a comment like that,
because the summarized line gives it nothing to fail on. I accept this
gap because every package in this set that requires disclosure states it
plainly enough in the repo-facts line for the check to see it (`pkg-07`
and `pkg-20` both do), and I re-ran `--only pkg-03,pkg-05,pkg-07,pkg-20`
as a canary after the rewrite specifically to confirm the four
policy-shape packages the set contrasts still land correctly (they do) —
but a repo whose real policy is stricter than its one-line summary lets on
is a case this check cannot see from the evidence it's given.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
