# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

Where it lives: the plan's "Diagnosis" section or opening paragraph, read
next to the repro-evidence block's numbered steps and any control run
described there (a second run that changes one variable and reports a
different or unchanged result). In live mode: the student's own posted
repro comment (or the house repro pack) is the repro evidence; the
issue thread's own maintainer comments sometimes already name a cause.

What it means for a diagnosis to follow the evidence: the stated cause is
exactly what a control run isolates (turn the suspected variable off or
change it, and the symptom tracks that change), or is a cause the thread
has already settled and the repro evidence doesn't contradict. What it
means to ignore or contradict it: a control run in the repro evidence
behaves in a way the stated cause cannot explain (the symptom appears
with the suspected cause absent, or disappears with it present) and the
plan says nothing about that control.

## Scope

Where it lives: the plan's "Scope"/"In scope"/"Not in scope" lines, and
—more importantly—what the "Approach"/"Proposed changes"/"Changes"
section actually commits to building, since a plan can label something
"not in scope" while still building it anyway in the approach section.

What one bounded change looks like: the approach section's numbered
steps all serve the one named defect, even across a few sites, when
those sites share the same underlying defect (e.g., two subtraction
sites with the same underflow, or a tokenizer regex touched only where
the reported failure occurs). What a drive-by rewrite looks like: the
approach section itself lists a second, third, or fourth substantial
and separable piece of work (a rewrite, an unrelated dependency bump, a
new abstraction, a new feature, a new CI job) as things that will be
built now, not merely mentioned as a future idea.

## Executability

Where it lives: the plan's named files/paths/locations, and its
approach or steps list, read for whether every decision needed to start
is already made.

What it means for a stranger to be able to start: specific files or
code locations are named, and the approach is an ordered, concrete list
of what will change there — a groupmate who has never seen the issue
could open those files and start. What it means for a plan to defer
every real decision to build time: language that names the decision
without making it ("upstream or vendored, whichever is easier", "not
sure which layer — gocui? tcell?", "somewhere around linter execution"),
or a "files" list that is empty or unnamed.

## Test plan

Where it lives: the plan's "Test plan" section, read against the
repro-evidence block's own steps, artifacts, and any timing or exit-code
values it reports.

What a decisive test plan names that a vague one does not: a specific
action from the repro evidence (re-run this exact command) plus a
specific expected result (this exit code, this output, this artifact,
a stated timing threshold) — something a stranger could check and get a
yes/no answer from, tied to the fix under test. A vague test plan names
only a feeling ("should feel fast", "shouldn't feel broken") or an
unscoped action with no assertion about the fix itself ("run the full
test suite," with nothing said about what result would mean the fix
worked).

## Honesty

Where claims meet uncertainty: the plan's stated risks, its "not yet
verified" or "open question" language (if any), read against what the
repro evidence and thread actually establish and what they leave open.

How to tell an honest unknown from false confidence: an honest plan
names what it hasn't checked yet (a benchmark not yet run, a platform
not yet tested, a design question deferred to review) as exactly that,
right next to the parts it is sure of. False confidence asserts a claim
the repro evidence does not actually back ("this conclusively proves
X"), or reads as fully certain while skipping a risk the repro evidence
itself makes visible (e.g., an evidence block that shows a control only
partially confirms the mechanism, and the plan never mentions the gap).
A deviation recorded honestly, mid-build, in `plan.md`'s deviations
section (live mode only) is this same family: say what changed and why,
don't let the diff be the only record of it.

## Comms

Where the words meet the thread and the repo: the plan comment, read
against (a) the thread highlights (or the live thread) for anything a
maintainer already said — a diagnosed cause, a requested test, a
preferred approach, a linked PR to coordinate with — and (b) the
repo-facts contribution-policy line for an AI-use disclosure ask.

What thread-aware looks like next to a plan that ignores the room: a
comment that names the maintainer's direction and either follows it or
says plainly why it takes a different path reads as written by someone
who read the thread. A comment that pivots to an unrelated angle (a
docs-only workaround, say) while the thread already has a maintainer
pointing at the exact code and asking for a specific test, without
mentioning any of that, reads as though the thread was never opened.

Reading the AI-disclosure line: silence means no disclosure required.
A policy that asks only for comments in the contributor's own words (not
a disclosure mandate) is satisfied by a first-person, specific comment.
A policy with explicit disclosure language ("must be disclosed", "stating
the tool used and the extent of the assistance") or a stated ban on a
specific AI-involvement category needs an explicit disclosure statement
in the comment; its absence fails the check even if the plan itself is
excellent. Naming a dedicated AI-policy document by itself is not the
signal — read what that document's summarized line actually asks for.
