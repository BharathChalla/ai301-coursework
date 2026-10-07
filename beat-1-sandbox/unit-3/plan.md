# Plan: codepath/pathreview-ai301-fa26-s3#62

## Diagnosis

The Redis probe in `api/routes/health.py` builds its client from
`settings.redis_host` and `settings.redis_port`. `Settings` in
`core/config.py` defines neither field — it carries a single
`redis_url` (`redis://localhost:6379/0`). The attribute access raises
`AttributeError`, caught by the surrounding `except Exception`, so the
probe reports `"redis": "unhealthy"` even when Redis is reachable.
Confirmed in my Unit 2 reproduction: with Redis independently confirmed
healthy (`redis-cli ping` -> `PONG`), `GET /health` still returned 503
with the exact log line the issue names:
`redis_health_check_failed error="'Settings' object has no attribute 'redis_host'"`.

## Scope

One bounded change: build the Redis client from `settings.redis_url`
instead of the nonexistent `redis_host`/`redis_port` fields.

In scope: the Redis client construction in `api/routes/health.py`'s
`health_check` function, and a regression test for it.

Not in scope:
- The unrelated Postgres failure on the same endpoint (`"postgres":
  "unhealthy"`), which I noticed as an aside during Unit 2's
  reproduction — the log shows a separate cause (`Textual SQL
  expression 'SELECT 1' should be explicitly declared as
  text('SELECT 1')`), a different seeded defect this issue does not
  ask me to fix.
- Adding new `redis_host`/`redis_port` fields to `Settings`. The issue's
  own manifest entry names `core/config.py` as a file this defect
  touches, but `redis_url` already fully specifies the connection;
  adding parallel host/port fields would just duplicate that as
  redundant configuration surface rather than fix anything. I reviewed
  `core/config.py` and made no change there — noted here as a
  deliberate scope decision, not an oversight.

## Files

- `api/routes/health.py` — the Redis client construction (one line, the
  fix itself)
- `tests/unit/test_health.py` — new: a regression test covering the fix

## Approach

1. Replace the `redis.Redis(host=settings.redis_host,
   port=settings.redis_port, db=0, decode_responses=True)` construction
   with `redis.from_url(settings.redis_url, decode_responses=True)`,
   which builds the client from the one connection string `Settings`
   actually exposes.
2. Add `tests/unit/test_health.py` with three cases: Redis reachable
   reports `"healthy"`; the client is built from `settings.redis_url`
   (not a nonexistent attribute); Redis genuinely unreachable still
   reports `"unhealthy"` (so the fix doesn't just make the check always
   pass).

## Test plan

Re-run my Unit 2 reproduction steps against the built change:
`docker compose up -d`, confirm Redis independently
(`redis-cli ping` -> `PONG`), run the live server, `GET /health`.
Before the fix: 503 with `"redis": "unhealthy"` and the
`AttributeError` log line. After the fix: `"redis": "healthy"`, no
Redis-related error in the log (the log's error output is limited to
the unrelated Postgres failure only). Additionally: stop the Redis
container and re-run `GET /health`, expecting `"redis": "unhealthy"`
again, to confirm the fix still correctly detects a genuine outage
rather than always reporting healthy. All three new unit tests pass
locally (`pytest tests/unit/test_health.py -v -m unit`).

## Risk

Low. The change is a one-line client-construction swap using the
connection string the app already reads from `.env`/`REDIS_URL`
everywhere else; no behavior other than the Redis probe's client
construction changes. One unrelated finding along the way: a full
`mypy` run in this environment currently fails before reaching any of
my files, on a numpy-stub incompatibility (`numpy/__init__.pyi:737:
error: Type statement is only supported in Python 3.12 and greater`).
This is a pre-existing environment issue unconnected to this fix; I did
not chase it, and I'm naming it here rather than silently working
around it.

## Deviations

Nothing changed; the plan held. The build matched this plan exactly:
one line in `api/routes/health.py`, one new test file, no changes to
`core/config.py`, and the before/after evidence came out exactly as
predicted (see `beat-1-sandbox/unit-3/plan-and-implement.md`'s Evidence
field for the actual command output).
