# github-actions

Reusable GitHub Actions workflow that starts the portable Codweft runner.

The Codweft GitHub App creates a job-scoped temporary branch and workflow in a
destination repository. The temporary workflow calls `codweft-runner.yml` and
is removed after the run; repositories do not keep a Codweft router workflow.

```yaml
uses: codweft/github-actions/.github/workflows/codweft-runner.yml@v0.4.10
```

## Versioning

This repository is published as semantic version tags. Generated temporary
workflows pin the immutable commit SHA behind an approved release, never
`@main` or a mutable tag.

The workflow selects the latest Node.js 24 patch with `check-latest: true` and the latest stable Bun release. The portable runner retains its patched Node.js minimum; the application requires Bun 1.4.4 or newer. Release v0.4.9 remains immutable with its original runtime selectors. Existing consumers must select the new approved workflow release to receive these selector updates.

## Workflow

`codweft-runner.yml` does not contain mode-specific agent logic. It:

1. requests a short-lived Codweft bootstrap token using GitHub Actions OIDC;
2. downloads the HTTPS runner tarball and verifies its required SHA-256 digest;
3. installs the verified package with the latest stable Bun release;
4. starts the package entrypoint with `CODWEFT_JOB_ID`, `CODWEFT_BOOTSTRAP_TOKEN`, and
   `CODWEFT_API_URL`.

The runner owns checkout, pinned agent setup, MCP configuration, model fallback,
server-side provider proxying, artifacts, and final result reporting. Raw model
provider credentials are never passed into repository-controlled agent processes.
