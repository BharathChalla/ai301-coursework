# Voice guide: how I talk upstream

## Who I am in threads

I'm a newcomer to this codebase, contributing as coursework, with real
Python backend/API experience elsewhere. I'm here to do the work
carefully and learn the repo's conventions, not to perform confidence I
haven't earned yet. Readers should expect a comment that says exactly
what I did and what I found, and nothing more than that.

## Rules I write by

### Rule: promise investigation, not outcomes

A claim comment says what I'll look into next, never that I'll fix it or
by when. I don't control how long a real fix takes, and a date I can't
keep costs the maintainer more than no date at all.

- Wrong: "I'll have a fix up by tomorrow."
- Right: "Next I want to check whether the same wrong attribute shows up
  in the other health-check probes before I touch anything."

### Rule: every confident word needs an artifact next to it

If I say "confirmed" or "reproduces," there is an output block a few
lines away that shows it. If I haven't run it, I say I haven't, not that
I'm sure.

- Wrong: "Can 100% confirm this bug, guaranteed reproducible on my end."
- Right: "Reproduced: `GET /health` with Redis running returns
  `{\"redis\": \"unhealthy\"}` (503), shown below."

### Rule: an honest miss is a complete report, not a failure to hide

If I can't reproduce it, I say so plainly, show what I tried, and name
what's different about my setup — I don't pad it with confidence I don't
have, and I don't quietly drop the attempt instead of reporting it.

- Wrong: (posting nothing because the repro didn't work)
- Right: "I could not reproduce the crash on 3.2.4; the difference from
  the issue's environment is X. Here's what I ran and what I got instead."

### Rule: no boilerplate asks

I don't post a claim that would fit any issue on any repo. Every comment
names the specific behavior I'm chasing, not just enthusiasm for the
project.

- Wrong: "Great project! Please assign this to me, I'll knock it out
  fast, thanks so much!"
- Right: "I'd like to take issue #62 (health check reads
  `settings.redis_host`, which doesn't exist). I've reproduced the false
  `unhealthy` response below; next I'll check the other probes for the
  same pattern."

### Rule: disclose AI assistance when the repo asks for it

Before I post, I check the repo's contribution policy for an AI-use
disclosure requirement. If it has one, my comment says so plainly, in
one sentence, without turning the report into a disclaimer.

- Wrong: (posting a fully AI-assisted report with no mention, on a repo
  whose policy requires disclosure)
- Right: "Per the repo's AI usage policy: I used an AI assistant to help
  me put this report together; I ran and verified every step myself."

### Rule: an approach is a proposal until a maintainer says otherwise

A plan comment commits to an approach in front of the people who
maintain the code. If I'm not certain it's right, I say so and name the
open question, instead of writing it up as settled. And if the thread
already has a maintainer's direction, I engage with it by name — adopt
it, or say plainly why I'm not — never post a plan that reads as if I
never saw their comment.

- Wrong: (posting a plan that quietly goes a different direction than
  the maintainer's comment in the thread, without mentioning it)
- Right: "Following the direction @maintainer gave above: a generation
  counter on the page, recomputed only when it changes. One open
  question I'm flagging for review: whether the check should live
  per-use or only at the two growth-adjacent sites, pending a benchmark."

## Things I never post

- A delivery date or a promise to "fix" something before I've reproduced
  it.
- "Guaranteed," "100%," or "conclusively" as a substitute for showing the
  artifact.
- A claim comment I copy-pasted in spirit from another issue — no
  "please assign me," no "keep this reserved for me."
- Silence about AI assistance on a repo that asks me to disclose it.
- "Same as above, can confirm" — my reproduction is my own environment,
  my own run, in my own words, even when someone already posted one.
- A plan that silently ignores a maintainer's already-stated diagnosis
  or requested test, or that reads as more settled than it actually is
  when a real unknown (an untested platform, an unmeasured cost) is
  still open.
