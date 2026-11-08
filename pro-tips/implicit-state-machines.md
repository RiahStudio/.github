# Pro tip: find the implicit state machines

- **Source:** Vox (@Voxyz_ai) on X, 2026-11-08 — https://x.com/Voxyz_ai/status/2119444845890617507
- **Added:** 2026-11-08
- **Kind:** audit (read-only report). Fixing is a separate, human-started step.
- **Best for:** anything with a job that moves through steps — sends, deploys, uploads,
  sign-ups, retries, alarms, game rounds, review queues.

## The original prompt (verbatim)

> Find all of the implicit state machines in my app. Make the best decision that will
> elevate all three: the user experience (UX), developer experience (DX), and agent
> experience (AX) always, without breaking anything.

## What it catches, in plain English

Lots of code tracks "where is this job up to?" without ever saying so in one place.
The progress lives in a marker file here, a status field there, a flag, a timestamp,
the time of day a cron fires. Each piece is fine alone. Together they can land in a
combination nobody planned for: "sent" and "failed" at the same time, a retry that
can never finish, an alarm that fires after the job already succeeded. Those are the
quiet bugs that show up as a missed send or a duplicate email.

Making the state machine **explicit** means one list of the allowed states, one place
that moves between them, and a test that proves the bad combinations can't happen.

## Why the automated run only reports

The tweet's prompt tells the AI to go ahead and change things. That's fine when a
person is watching. An unattended weekly run should not rewrite a live pipeline, so
the automated version below writes a ranked report and touches nothing else. A person
reads it, picks one finding, and asks an agent to fix just that one.

## Prompt (automated run)

<!-- prompt:start -->
Find all of the implicit state machines in this repository: places where something
moves through states (for example not-started, running, done, failed, retrying,
alarmed) but the states and the allowed moves between them are spread across flags,
marker files, status files, environment variables, schedules, return codes or string
comparisons instead of one explicit model.

Focus on the code that actually runs in production. Follow imports from the entry
points (CI workflows, scheduled jobs, main scripts) to find it, and skip archives,
backups, prototypes and dead variants. If the repository has an incident log or a
failure-mode catalog, read it first and cite its entries rather than repeating them.

For each one, report:
1. Name, and where it lives (file:line).
2. The states and transitions as the code really has them today.
3. What makes it implicit, and each concrete way it can reach a stuck, illegal or
   ambiguous state (give a real scenario, not a hypothetical category).
4. The smallest safe change that makes it explicit, its risk (low/medium/high), and
   what it improves for UX (the people using it), DX (the people changing it) and AX
   (the AI agents maintaining it).

Rank the findings most valuable first. If a machine is already sound, say so in one
line. Do not pad. Mark anything you could not confirm as unverified.

Start the report with a five-line summary a non-programmer can read.

This is a READ-ONLY review. Do not modify, create or delete any file except the
report file you were told to write. Do not run anything that sends email, deploys,
or calls paid or production services.
<!-- prompt:end -->

## Fixing a finding (by hand)

Pick one finding from the report and give an agent this, filled in:

> From the state-machine report, fix finding #N only. Make that state machine explicit
> with the smallest change that works, add a test that proves the bad state it
> describes can no longer happen, run the project's existing checks, and do not change
> anything outside that machine.
