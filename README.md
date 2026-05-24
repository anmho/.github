# anmho GitHub defaults

Shared GitHub workflows and repository defaults for `anmho` repositories.

## Reusable Workflows

### Linear PR Sync

Add this caller workflow to each repository that should comment PR lifecycle
events back to linked Linear issues:

```yaml
name: Linear PR Sync

on:
  pull_request:
    types: [opened, edited, reopened, closed]

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
