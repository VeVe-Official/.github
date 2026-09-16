# Caller for the shared issue-triage workflow

Org `.github` workflows are **not** inherited the way issue templates are, so each
repository needs this file. It is the only per-repo part; the logic stays central.

Create `.github/workflows/issue-triage.yml`:

```yaml
name: Issue triage

on:
  issues:
    types: [opened]

jobs:
  triage:
    uses: VeVe-Official/.github/.github/workflows/issue-triage.yml@main
```

## What it does

Issues created outside the web UI — from the Slack app in particular — bypass
issue forms completely. Templates and `blank_issues_enabled` are web-UI
constructs, so an issue opened from Slack arrives with **no type**, and an
untyped issue is invisible to the roadmap and timeline views.

On every newly opened issue the shared workflow:

1. Reads the issue's current type. **If it already has one it stops** — issues
   filed through a form are left completely alone.
2. Maps the first recognised label to a type: `epic` → Epic, `bug` → Bug,
   `feature`/`enhancement` → Feature, `task`/`chore` → Task.
3. Falls back to **Task** when no label matches, rather than leaving it untyped.
4. Comments with the questions that type's form would have asked, addressed to
   the author.
5. Adds `needs-triage`.

## Prerequisites

The labels `epic`, `feature`, `task`, `needs-triage` (plus the pre-existing
`bug` and `enhancement`) must exist in the repository. The workflow adds
`needs-triage`, and `gh issue edit` fails if the label is missing.
