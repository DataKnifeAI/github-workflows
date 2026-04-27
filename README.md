# DataKnife GitHub workflows

Reusable **workflows** (`workflow_call`) and a **composite action** shared across DataKnife repositories (game server containers, GitLab mirrors, security scans).

## Consumers

Pin a **tag** (for example `v1`) in caller repos; avoid floating `@main` in branch protection once stable.

```yaml
jobs:
  verify:
    uses: DataKnifeAI/github-workflows/.github/workflows/reusable-game-server-ci.yml@v1
    secrets: inherit
```

## Contents

| Path | Purpose |
|------|---------|
| `.github/actions/verify-game-server` | Shellcheck, Hadolint (configurable), Markdownlint, Yamllint, `kubectl kustomize` |
| `reusable-game-server-ci.yml` | Single **Verify** job for PRs and `main` |
| `reusable-game-server-mirror.yml` | Verify gate + optional push to GitLab mirror on `main` |
| `reusable-game-server-security.yml` | Build image + Trivy SARIF upload (GitHub Security tab) |

For **mirror** workflows on pull requests, `GITLAB_TOKEN` may be absent; the reusable workflow declares that secret as **optional** so the verify gate still runs. The push job only runs on `main` and will fail there if the token is missing.

## Inputs (CI / mirror verify)

- `hadolint_dockerfile` (default `Dockerfile`)
- `hadolint_dockerfile_secondary` (default empty; set e.g. `Dockerfile.staging` for Windrose)
- `hadolint_failure_threshold` (default `error`)
- `markdownlint_config`, `yamllint_config` (defaults match game-server repos)
- `kustomize_path` (default `deploy`; set empty string to skip)

## Adding a new consumer repo

1. Copy `.markdownlint.jsonc` / `.yamllint.yml` patterns from an existing game server repo (or override via inputs).
2. Add a workflow that `uses:` the reusable workflow with `secrets: inherit` where needed.
3. For mirrors, pass `gitlab_mirror_path` and ensure `GITLAB_TOKEN` exists on the caller repo.

## License

MIT — see `LICENSE`.
