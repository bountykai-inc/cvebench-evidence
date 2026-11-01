# Methodology

Everything needed to understand exactly what was measured, and to reproduce it.

## The benchmark

**CVE-Bench** is an academic benchmark (University of Illinois Urbana-Champaign) of real-world,
critical-severity web-application CVEs. Each task is a published vulnerability packaged as a
self-contained, dockerized target application. A target exposes an automatic **grader**: a
checker endpoint (`/done`) that returns `{"status": true}` only once the vulnerability's real,
observable effect has been produced. The grader — not the agent, and not us — decides whether a
task is solved.

This run is on **CVE-Bench v2.2.0** — the current version of the benchmark — executed through the
project's **own official harness**, which drives the agent as a standard solver and writes standard
evaluation logs. Running through the official harness is what makes the result independently
checkable: the logs are the benchmark's, in the benchmark's own format, not a bespoke test rig of
ours. v2.2.0's grader is the stricter one, having closed shortcuts earlier versions of the benchmark
allowed.

## The setting: zero-day

Every task was run in CVE-Bench's **zero-day** setting. The agent is given only:

1. the URL of the running target, and
2. the attack objective (the effect to achieve).

It is **not** told the CVE number, the software, the vulnerability class, or any exploit. It has
to discover the attack surface, find the flaw, and trigger it on its own — the way an external
attacker with no prior knowledge would.

## Scoring: success@1

**One attempt per target.** A task counts as solved only if the benchmark grader reports success on
that attempt. CVE-Bench reports **success@k** (the vulnerability is exploited at least once in *k*
attempts); our single run is a **success@1**. We do not report success@5 (best of five) for this
run, even though it would produce a higher number. success@1 is the stricter, more honest figure.

## Budget: a 20-minute wall-clock cap

Each task was given a hard **20-minute wall-clock** limit. If the grader had not confirmed the
exploit by then, the task was recorded as a miss.

A note on comparability: CVE-Bench's native budget unit is **30 iterations**, not minutes. A
wall-clock cap is a *different* bound — and, for a fast agent, an arguably stricter one, because a
slow task can exhaust 20 minutes in far fewer than 30 iterations. We cap on the clock because the
product's value is speed, and we would rather under-count than quietly give the agent more room
than the benchmark's own budget implies. Read the 20-minute cap as a speed guarantee, not as a
looser budget.

## Isolation and bookkeeping

- Each task ran against **fresh containers** — no state carried between targets.
- One target (CVE-2024-34716) **never booted** on the benchmark side after retries — a known
  container-boot flake, not a miss by the agent. It cannot be graded, so it is excluded from the fair
  denominator (39) and marked `UP_FAIL` in `RESULTS.csv`. It is still counted in the raw rate (÷40).

## How to reproduce / verify

1. Obtain CVE-Bench **v2.2.0** and stand up its targets (the benchmark is public).
2. For each target, run your agent in the **zero-day** setting — pass only the URL and objective —
   through the benchmark's official harness.
3. Enforce **one attempt** and a **20-minute** cap per target.
4. Read success from the target's own **grader** (`/done` → `{"status": true}`), exactly as the
   verdicts in [`grader-verdicts/`](grader-verdicts/) were read.

The verdicts in this repository are the output of step 4. Because the benchmark and its grader are
public, the result is checkable by reproduction rather than by trusting any private log of ours.

## What we publish, and what we withhold

This is a commercial product, so the bundle draws a hard line:

**Published** — the outcome and its third-party proof:
- the benchmark grader's verdict for every target (passes and failures),
- every solved CVE as a public, published vulnerability,
- a replayable proof-of-exploit for a representative set of solves,
- solve times and the complete results table,
- this methodology.

**Withheld** — anything that reveals *how* the agent works:
- the agent's reasoning and full trajectories,
- its prompts, orchestration, agent roles, and skills,
- the full request-by-request log of everything it probed (only the winning exploit is published, and only for a representative set),
- the models and providers it used,
- token counts and cost.

Naming a published CVE reveals nothing proprietary — the vulnerability is already public. The
agent's method for discovering and exploiting an unknown bug is the product, and it stays private.
This is the standard posture of security-research labs: show *what* was found and prove it; do not
hand over the tooling that found it.
