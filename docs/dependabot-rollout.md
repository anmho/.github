# Dependabot Rollout Policy

This repository is the source of truth for Dependabot policy in maintained ANM
repositories. Copy the applicable blocks from
[`templates/dependabot/maintained-repo.yml`](../templates/dependabot/maintained-repo.yml)
into each target repository as `.github/dependabot.yml`, then remove ecosystem
blocks that do not have matching manifests in that repository.

The `.github` repository itself applies the policy only to GitHub Actions in
[`.github/dependabot.yml`](../.github/dependabot.yml), because that is the only
dependency ecosystem present in this repo.

## Standard Policy

- Cadence: weekly on Monday at 09:00 America/Los_Angeles.
- Scope: configure only ecosystems with manifests or lockfiles in the target
  repository. Do not add placeholder update blocks for absent ecosystems.
- Ecosystems:
  - `github-actions` for workflows in `.github/workflows`.
  - `npm` for npm, pnpm, or yarn JavaScript package manifests and lockfiles.
  - `bun` for Bun projects that use the current text `bun.lock` lockfile.
  - `gomod` for Go modules with `go.mod`.
  - `terraform` for Terraform modules and providers.
- Pull request limits:
  - GitHub Actions, Go modules, and Terraform: 2 open version-update PRs per
    ecosystem.
  - JavaScript package managers: 3 open version-update PRs per ecosystem.
  - Security updates keep GitHub's separate security-update limit.
- Grouping:
  - Minor and patch updates are grouped per ecosystem.
  - Major updates stay separate so agents can review breaking-change risk in
    isolation.
  - Terraform updates are grouped together because provider and module changes
    are evaluated through the same plan/apply review path.
- Cooldown:
  - Default cooldown is 7 days for normal version updates.
  - Semver major updates for JavaScript and Go wait 30 days.
  - Patch updates for JavaScript and Go wait 3 days.

This is intentionally low-noise for agent-maintained repos: Dependabot checks
weekly instead of daily, groups routine non-major updates, keeps major updates
reviewable one at a time, and caps each ecosystem so stale dependency PRs do
not take over a repo queue.

## Rollout Checklist

For each repo:

1. Inspect manifests and lockfiles on the default branch.
2. Add `.github/dependabot.yml` with only the applicable ecosystem blocks from
   the maintained-repo template.
3. Preserve repo-specific private registry configuration if present.
4. Validate YAML syntax before opening a PR.
5. Explain in the PR which ecosystem blocks were enabled and why.

| Repository | Rollout action |
| --- | --- |
| `symphony` | Replace the disabled Dependabot config (`open-pull-requests-limit: 0`) with the standard policy blocks for ecosystems present in that repo. |
| `create-svc` | Add standard policy blocks for the package manifests and any Go/Terraform surfaces present after inspection. |
| `auth` | Add standard policy blocks for the package manifests and any Go/Terraform surfaces present after inspection. |
| `terraform` | Add standard GitHub Actions and Terraform policy blocks; include JavaScript/Go only if manifests are present. |
| `create-app-saas` | Add standard GitHub Actions and JavaScript package-manager policy blocks. |
| `create-app-consumer` | Add standard GitHub Actions and JavaScript package-manager policy blocks. |
| `website` | Add standard GitHub Actions and JavaScript package-manager policy blocks. |
| `agent` | Add standard policy blocks for package manifests and any Go modules present after inspection. |
| `omnichannel` | Add standard policy blocks for package manifests and any Go/Terraform surfaces present after inspection. |
| `skills` | Add standard GitHub Actions and package-manager policy blocks if manifests are present. |
| `better-auth-studio` | Add standard GitHub Actions and JavaScript package-manager policy blocks. |
| `tab-organizer` | Add standard GitHub Actions and JavaScript package-manager policy blocks. |

The rollout belongs in follow-up repo-specific PRs. This issue only applies the
safe `.github` repo config and provides the shared policy/template.

## References

- GitHub Dependabot supported ecosystems:
  <https://docs.github.com/en/code-security/reference/supply-chain-security/supported-ecosystems-and-repositories>
- GitHub Dependabot options reference:
  <https://docs.github.com/en/code-security/reference/supply-chain-security/dependabot-options-reference>
