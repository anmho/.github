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

The workflow extracts Linear identifiers such as `ANM-123` from the PR title or
body, including `linear.app` issue links, and posts an idempotent comment with
the PR URL.
