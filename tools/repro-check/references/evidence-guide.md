# Evidence guide: where proof lives in a reproduction package

## Environment

Where it lives: the repro report's opening "Environment:" line(s), in an
eval bundle or in a live draft. In live mode, cross-check the version
named against the repo's latest release / current default branch shown
on the repo's front page, so a stale version gets flagged as a version
delta rather than accepted silently.

What good looks like: the OS/platform, the exact version of the tool or
library under test, and the install method (package manager, source
build, CDN, etc.) when it plausibly matters. When the issue's behavior
is known to hinge on a specific detail (a driver, a build profile, a
browser), that detail is named too, not left implicit. A version delta
from the issue (testing current vs. the version originally reported) is
fine as long as it is stated, not silently substituted.

## Steps

Where it lives: the repro report's numbered/described steps, read next
to the issue body's own reproduction recipe (its exact command, flags,
input, or trigger condition).

What makes steps followable: a stated starting state (what file, config,
or command was in place before the trigger), then the exact command(s)
run, in an order a stranger could copy verbatim and get the same
starting point. What breaks faithfulness: substituting a different flag,
operator, or input shape than the one the issue names (even one that
looks similar, like `:` for `=`, or a prefix range for an offset-from-end
range) without saying so, or omitting a condition the issue states is
required to trigger the bug (e.g., a specific flag, a specific driver, a
specific OS).

## Behavior shown

Where it lives: the artifact block(s) in the repro report — command
output, log excerpts, stack traces, screenshots — read directly against
the exact wording of the issue's "actual behavior" section.

What it means for an artifact to show the issue's behavior: the same
class of failure (the same exception type, the same error message shape,
the same observable symptom), not merely "something went wrong" or "the
command didn't do what I wanted." A crash reported as the bug needs a
crash in the artifact, not a graceful validation error or exit code; a
"still responsive" trace where the issue claims a hang is an adjacent
symptom, not a match. An honest cannot-reproduce is a valid outcome here
too: the report shows what was tried, states the behavior did not occur,
and names what's different about the attempt (version, environment,
timing) that might explain it, rather than silently omitting the miss.

## Honesty

Where claims and their backing meet: any sentence in the report that
asserts certainty ("confirmed", "100% reproducible", "guaranteed",
a stated root cause, "verified") is a claim; the artifact shown in the
same report (or explicitly, its absence) is the backing.

Telling a well-backed report from an over-claimed one: read every
confident sentence and ask whether an artifact a few lines away actually
demonstrates it. "I ran this on two machines with identical results,
which conclusively demonstrates the bug" backed only by a single output
block that shows a *different* error than the one reported is not
honesty, it's confidence borrowed against the wrong evidence. An honest
report's confidence rises and falls with what it actually shows: a
tentative reproduction says so, and a clean one can say so plainly
without needing intensifiers to compensate for a missing artifact.

## Comms

Where the words meet the repo: the candidate claim comment, read against
(a) whether it is specific to this issue and promises only investigation
(never a fix, a timeline, or a guarantee), and (b) the repo's contribution
policy line in repo facts, specifically any AI-use disclosure requirement.

What specific-and-honest looks like next to boilerplate: a claim that
names the exact behavior being chased and a concrete next step ("I
reproduced X, next I want to check Y") reads as this issue, this person;
a claim built from stock phrases that would fit any issue ("please
assign me", "I'll fix this in N days guaranteed", "keep this issue
reserved for me") reads as templated regardless of enthusiasm.

Reading the AI-disclosure line: silence on AI means no disclosure is
required. A policy that only asks for comments in the contributor's own
words (not a disclosure mandate) is satisfied by a first-person, specific
comment. A policy that states an explicit disclosure requirement, or
that names a dedicated AI-policy document (`AI_POLICY.md`,
`AI_USAGE_POLICY.md`) while conditioning acceptance on AI use (e.g.
banning fully-AI-generated contributions, requiring the human to
understand and take responsibility for every change), needs an explicit
disclosure statement in the comment ("I used an AI assistant to help me
organize this report; I ran and verified every step myself") — its
absence fails the check even if the reproduction itself is flawless.
