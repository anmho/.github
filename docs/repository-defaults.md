# Repository Defaults

This repository is the source of truth for maintained ANM repository settings.
Use these defaults when creating or repairing repos so agent handoffs do not
drift across projects.

## Merge Policy

Maintained repos should use squash merges only:

- Squash merge: enabled.
- Merge commits: disabled.
- Rebase merges: disabled.

Apply the policy with:

```sh
gh repo edit anmho/<repo> \
  --enable-squash-merge=true \
  --enable-merge-commit=false \
  --enable-rebase-merge=false
```

Verify the policy with:

```sh
gh repo view anmho/<repo> \
  --json nameWithOwner,squashMergeAllowed,mergeCommitAllowed,rebaseMergeAllowed
```

Expected result:

```json
{
  "squashMergeAllowed": true,
  "mergeCommitAllowed": false,
  "rebaseMergeAllowed": false
}
```

The reusable settings template is
[`templates/repository-settings/squash-only.yml`](../templates/repository-settings/squash-only.yml).

## Current Repo

`anmho/.github` has already been changed to squash-only merges:

```json
{
  "mergeCommitAllowed": false,
  "rebaseMergeAllowed": false,
  "squashMergeAllowed": true
}
```

## Rollout Checklist

For each maintained repo:

1. Confirm the repo is active and should receive shared ANM defaults.
2. Apply the squash-only merge policy.
3. Verify the three merge settings with `gh repo view`.
4. Record explicit skip reasons for archived, deprecated, or template-only repos.
5. Keep future repo setup docs/templates pointed at this policy.
