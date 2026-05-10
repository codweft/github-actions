# github-actions

Reusable GitHub Actions workflow that starts the portable Codweft runner.

The Codweft GitHub App installs a single router workflow in destination
repositories: `.github/workflows/codweft.yml`. That router calls
`codweft-runner.yml` by tag.

```yaml
uses: codweft/github-actions/.github/workflows/codweft-runner.yml@v0.4.1
```

## Versioning

This repository is published as semantic version tags. Destination repositories
must pin a tag, never `@main`. Codweft opens workflow update PRs when it wants
to move a repository to a new runner workflow version.

## Workflow

`codweft-runner.yml` does not contain mode-specific agent logic. It:

1. requests a short-lived Codweft bootstrap token using GitHub Actions OIDC;
2. installs the pinned public `@codweft/runner` package;
3. starts the runner with `CODWEFT_JOB_ID`, `CODWEFT_BOOTSTRAP_TOKEN`, and
   `CODWEFT_API_URL`;
4. passes configured model provider secrets through the environment.

The runner owns checkout, Pi setup, MCP configuration, model fallback,
artifacts, and final result reporting.
