# Pro tips

Tricks from people who build with AI coding agents, saved here so every Riah Studio
repo can use them, by hand or on a schedule.

Each tip is one file. It holds the original wording and where it came from, what it
catches in plain English, and a tuned prompt for an automated run between
`<!-- prompt:start -->` and `<!-- prompt:end -->`.

| Tip | What it catches | Kind |
|---|---|---|
| [implicit-state-machines](implicit-state-machines.md) | Jobs whose progress is tracked in scattered flags and files, so they can get stuck or do things twice | audit |

## Run a tip by hand

Open the project in Claude Code (or Codex) and paste the prompt from the tip's
**Prompt (automated run)** section.

## Run a tip automatically

The reusable workflow [`pro-tip-audit.yml`](../.github/workflows/pro-tip-audit.yml)
runs one tip against a repo and posts the report as a GitHub issue in that repo
(it is also attached to the run). To switch it on for a repo, add this file there as
`.github/workflows/pro_tips_audit.yml`:

```yaml
name: Pro tip audit
on:
  workflow_dispatch:
    inputs:
      tip:
        description: "Which tip to run"
        default: implicit-state-machines
  schedule:
    - cron: "17 3 1 * *"   # monthly, 1st of the month
jobs:
  audit:
    permissions:
      contents: read
      issues: write
    uses: RiahStudio/.github/.github/workflows/pro-tip-audit.yml@main
    with:
      tip: ${{ inputs.tip || 'implicit-state-machines' }}
    secrets: inherit
```

It needs one secret, `ANTHROPIC_API_KEY`, set on the repo or on the organization.
Without it the run does nothing except leave a warning, so adding the file is safe.
Each run costs a few dollars of API usage, depending on the size of the repo.

Automated runs only ever **report**. Fixes are started by a person, one finding at a
time.

## Add a new tip

1. Copy `implicit-state-machines.md` to a new file named after the tip.
2. Put the original wording and source link at the top.
3. Rewrite the prompt for an unattended run: read-only, writes nothing but its
   report, never sends, deploys or calls paid services, and gives concrete findings
   with file:line references.
4. Add a row to the table above.
