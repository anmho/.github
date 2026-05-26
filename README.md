# anmho GitHub defaults

Shared GitHub workflows and repository defaults for `anmho` repositories.

## Dependabot

Maintained ANM repositories should use the shared Dependabot policy in
[`docs/dependabot-rollout.md`](docs/dependabot-rollout.md). The reusable
template lives at
[`templates/dependabot/maintained-repo.yml`](templates/dependabot/maintained-repo.yml).

## Reusable Workflows

### Linear PR Sync

Add this caller workflow to each repository that should comment PR lifecycle
events back to linked Linear issues:

```yaml
name: Linear PR Sync

on:
  pull_request:
    types: [opened, reopened, ready_for_review, closed]
  pull_request_review:
    types: [submitted]

jobs:
  linear-pr-sync:
    uses: anmho/.github/.github/workflows/linear-pr-sync.yml@main
    secrets:
      LINEAR_API_KEY: ${{ secrets.LINEAR_API_KEY }}
```

PRs should include an explicit ticket line in the body:

```md
Ticket: https://linear.app/anmho/issue/ANM-123
```

The workflow prefers `Ticket: <linear.app issue URL>` lines, preserves that
actual ticket URL in the Linear comment, and posts an idempotent comment with
the PR URL. Plain `linear.app` issue links and bare issue identifiers are kept
as fallback detection only.

The workflow also syncs Linear state from PR lifecycle:

- `opened`, `reopened`, and `ready_for_review` move linked non-draft PR issues
  to `In Review`.
- GitHub reviews submitted as `changes_requested` move linked issues back to
  `In Progress` so Symphony can pick up rework. Graphite review flows should
  rely on the GitHub review event they create or mirror.
- merged PRs move linked issues to `Done`.
- closed unmerged PRs only comment; they do not change issue state.

The workflow mirrors the highest linked Linear issue priority onto the PR with
one of these labels: `linear-priority:urgent`, `linear-priority:high`,
`linear-priority:normal`, `linear-priority:low`, or `linear-priority:none`.
Review inboxes should use that label, or fetch Linear directly, when ordering
agent handoffs for review.

Override the target state names if a repository uses different Linear workflow
states:

```yaml
jobs:
  linear-pr-sync:
    uses: anmho/.github/.github/workflows/linear-pr-sync.yml@main
    with:
      review_state: Human Review
      changes_requested_state: Rework
      merged_state: Done
    secrets:
      LINEAR_API_KEY: ${{ secrets.LINEAR_API_KEY }}
```
